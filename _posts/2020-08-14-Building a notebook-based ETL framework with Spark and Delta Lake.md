---
published: true
---
The process of extracting, transforming and loading data from disparate sources (ETL) have become critical in the last few years with the growth of data science applications. In addition, data availability, timeliness, accuracy and consistency are key requirements at the beginning of any data project.

Even though there are guidelines, there is not a one-fits-all architecture to build ETL data pipelines. It depends on multiple factors such as the type of the data, the frequency, the volume and the expertise of the people that will be maintaining these. Data pipelines need to be reliable and scalable but also relatively straight forward for data engineers and data scientists to integrate with new sources and make changes to the underlying data structures.

There is a myriad of tools that can be used for ETL but [Spark](https://spark.apache.org/){:target="_blank"} is probably one of the most used data processing platforms due to it speed at handling large data volumes. In addition to data processing, Spark has libraries for machine learning, streaming, data analytics among others so it's a great platform for implementing end-to-end data projects. It also supports Python (PySpark) and R (SparkR, sparklyr), which are the most used programming languages for data science.

On the other hand there is [Delta Lake](https://delta.io/){:target="_blank"}, an open source data lake that supports [ACID](https://en.wikipedia.org/wiki/ACID){:target="_blank"} transactions which makes it a great option to handle complex data workloads. In addition, it has multiple features such as schema evolution (changes to the data model are straightforward to implement) and schema enforcement (to ensure that the data that arrives is aligned with the destination schema), data versioning (going back in time), batch and streaming ingestion and last but not least, it's fully compatible with Spark.

[Databricks](https://databricks.com/){:target="_blank"}, the company behind Spark, has an Analytics cloud-based platform that provides multiple tools to facilitate the use of Spark across different use cases. Their collaborative notebooks allow to run Python/Scala/R/SQL code not only for rapid data exploration and analysis but also for data processing pipelines. In fact, [notebooks play a key role in Netflix's data architecture](https://netflixtechblog.com/notebook-innovation-591ee3221233){:target="_blank"}.

After that brief introduction we are ready to get into the details of a proposed ETL workflow based on Spark Notebooks. **In this architecture, the notebook that act as the orchestrator pulls the data from Delta, executes the notebooks in the list and then stores the results of the runs back into Delta.**

The idea of this article is not provide the full implementation but an overview of the workflow with some code snippets to help in the understanding of *how the process works*.

- First, a master table is created in Delta Lake that contains the name and path of the notebooks to be executed along with the job group, timeout (number of minutes the job can take until it is suspended), maximum retries (number of tries if job fails), status (enabled/disabled) and priority (order of run or -1 if the notebook can run in parallel).

```sql
CREATE TABLE delta.`/mnt/delta/master_runs`
  notebook_path STRING,
  job_group STRING,
  timeout_minutes SMALLINT, 
  maximum_retries SMALLINT,
  priority SMALLINT,
  is_enabled BOOLEAN
) USING DELTA
```

- This table will be queried by the main Spark notebook that acts as an orchestrator. It gets the list of notebooks that need to be executed for a specific job group order by priority. That is, each job configured in Databricks can include a parameter that will be passed to the main notebook to get the notebooks to run for that group only. The groups can be defined, for example, based on frequency or data source.

```python
# Gets job group from the Spark job definition
job_group = getArgument("job_group")

df_notebooks_to_run = spark.sql("SELECT notebook_path, status, timeout_minutes, maximum_retries, priority FROM master_runs WHERE job_group = {} and is_enabled = True ORDER by priority".format(job_group)

list_notebooks_to_run = df_notebooks_to_run.collect()
```

Once the list of notebooks is available, we iterate over each one and split them into separate lists based on whether they should run sequentially or not. For example, notebooks that depend on the execution of other notebooks should run in the order defined by the priority field. When there are no dependencies, notebooks are marked with priority = -1 and can run in parallel.

```python
notebooks_parallel = []
notebooks_sequential = []
for nb in list_notebooks_to_run:
  if nb['priority'] >= 0:
    notebooks_sequential.append(nb['job_name'])
  else: 
    notebooks_parallel.append(nb['job_name'])
```

- To run notebooks in parallel we can make use of the standard Python concurrent package. The pool of workers will execute the notebooks in the tuple job_tuple_parallel asynchronously. 
(More details on how to run Databricks notebooks in parallel can be found here)

```python
from concurrent.futures import ThreadPoolExecutor, wait
job_tuple_parallel = tuple(notebooks_parallel)
run_in_parallel = lambda x: run_notebook(x) 
 
futures = [] 
results = [] 
with ThreadPoolExecutor() as pool: 
 results.extend(pool.map(run_in_parallel, job_tuple_parallel)) 
 for x in futures: 
   if x.result() is not None: 
     results.append(x.result()) 
   else: 
     results.append(None) 
```

- The method run_notebook will use Databricks dbutils library (dbutils.notebook.run) to execute the notebook and log the results of the execution back into Delta.

- Each execution of a notebook will have its own run_id. The run id can be obtained using Scala and then stored in a temporary view so that it could be accessed by PySpark.

```scala
%scala
val runId = dbutils.notebook.getContext.currentRunId.toString
Seq(runId).toDF("run_id").createOrReplaceTempView("run_id")
%python
run_id = spark.table("run_id").head()["run_id"]
run_id = re.findall(r'\d+', run_id)[0]
run_id = int(run_id)
```

- Once the run_id is obtained for the current run, it can be logged along with other run parameters into a resulting Delta table for auditing purposes. This table can also store the start and end time of the run and the status (success, failed) so that it can later be used to build dashboards to track the performance of the multiple notebook runs.

## Conclusion
This workflow can of course be improved and augmented but based on personal experience it can work pretty well with heavy workloads and it's straightforward to add new pipelines when the need arises. There are also open source tools that should be considered to build, schedule and monitor workflows. Apache Airflow is one of them; a powerful open source platform that can be integrated with Databricks and provides scheduling of workflows with a Python API and a web-based UI.
