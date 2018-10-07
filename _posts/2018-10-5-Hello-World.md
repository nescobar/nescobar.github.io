---
layout: post
title: Data Guasu
published: true
---

Welcome to **Data Guasu**! In this blog I will be sharing opinions, ideas, experiences and details of projects I have worked on, and my journey to becoming a _data passionist_ (due to the impostor syndrome I'm having trouble calling myself a data scientist). 

As the name implies most posts will be related to data, covering a variety of topics, techniques and technologies as **tools to answering (real life) questions**. 

Now, where does _Guasu_ come from? It comes from Guarani (one of the official languages in Paraguay, in addition to Spanish) and it means **big**. So there it is, the name Data Guasu might refer to big data (I could not find a suitable Guarani word for diverse data) but the scope of this blog is much _guasu_ than that.  

![_config.yml]({{ site.baseurl }}/images/Chipa_Guasu.jpg)

_Traditional Chipa Guasu dish from Paraguay._ [Source](http://micorazondearroz.com)

## My Journey

### Data Warehousing 1.0
My interest in data started in 2009, when I started working as a [Data Warehouse](https://en.wikipedia.org/wiki/Data_warehouse) Consultant for a telecommunications company in Accra, Ghana. At that time we had a Data Warehouse running in SQL Server 2008 with multiple [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) processes in place to load [CDRs](https://en.wikipedia.org/wiki/Call_detail_record) from call, SMS and data transactions. I got to learn a lot about SQL Server technology stack (SSIS, SSRS and SSAS) and dimensional data modeling based on Kimball techniques. 

### Wrong Data = No Data
After being part of the DWH team for a year and a half, I moved to a different role focusing on building dashboards and reports for the operational team so they could track their sales and monitor their commissions. 

It was a difficult time to join the team because there were a lot of claims on how commissions were paid. _I remember receiving easily a few hundred emails per day from freelancers that wanted to understand why they got paid that amount_. Going over the process and doing analysis on the data we identified multiple inconsistencies that were generated because freelancers were usually sharing phones (and thus, phone numbers). The numbers were used to match the sales to their names so one freelancer that did not sell a line was receiving a commission for the one that did. 

To solve this, we worked out a new registration system via [USSD](https://en.wikipedia.org/wiki/Unstructured_Supplementary_Service_Data) asking each freelancer to send their details and we assigned a unique ID for each. So, if they wanted to use a different phone number they could just use their ID to ensure their sale was going to be assigned to the correct person. This solution not only improved commission payment process but also increased sales because freelancers were motivated!

### (Business) Value > Technology 
When we got over the data issues, we felt that we needed to have a better _and faster_ way to keep track of the sales. From DWH we could only get data the next day but the team was eager to look at how they were doing _during the day_ to take immediate action. 

I started doing some research on possible reporting/dashboard tools and found one called [Dundas Dashbard](https://www.dundas.com/). The integration with SQL Server was pretty straightforward so I decided to build a set of specific dashboards to show it out to the business stakeholders.  

![_config.yml]({{ site.baseurl }}/images/BOC_GH.jpg)

It wasn't anything fancy but it attracted enough attention because of the potential value. These simple dashboards were providing data that was _not previously available_ in that format and with an hour delay. Soon after, my request to buy more licenses was approved and the dashboards were shown not only in offices but also sent by email to territory managers. The project then became regional and was replicated to other countries in Africa.
