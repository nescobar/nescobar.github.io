---
layout: post
title: Analysis of ATP Tennis Competitions
categories: data visualization
tags: 'data_visualization, seaborn, matplotlib'
published: true
---
In this post I will make use of Python's libraries: pandas, matplotlib and seaborn to analyze data from **ATP tennis competitions from the year 1968 up to 2018**, including Grand Slams, Masters Series, Masters Cup and International Series competitions.

The __[dataset](https://github.com/JeffSackmann/tennis_atp)__ prepared by __[Jeff Sackmann](https://github.com/JeffSackmann)__ contains information on every single match played since 1968. It includes details such as match’s date, location, tournament type, surface, winner and loser, score, duration and additional statistics such as players’ rankings, age and height, aces, double faults, break points faced and saved, service points among other helpful stats for both players.

The full notebook can be accessed [here](https://kaggle.com/nescobar/data-visualizations-of-atp-tennis-competitions/).

### Loading the data
There is one _.csv_ file per year per tournament type. In this analysis I'm only loading _atp_matches_YYYY_ files which contain the ATP matches for that year. 

```python
# Path to all atp_matches_YYYY.csv files
path ='tennis_atp_data' 
files = glob.glob(path + "/*.csv")
tennis_df = pd.concat((pd.read_csv(f) for f in files))
```

### Deriving new columns
I derive two new columns to store year and year/month attributes.

```python
# YYYYMM of the tournament
tennis_df['tourney_yearmonth'] = tennis_df.tourney_date.astype(str).str[:6]
## YYYY of the tournament
tennis_df['tourney_year'] = tennis_df.tourney_date.astype(str).str[:4]
```

### Exploratory Data Analysis

#### Distribution of most important attributes
```python
dimensions = ['winner_rank','loser_rank','winner_age','loser_age','winner_ht',
'loser_ht','w_svpt','l_svpt']

plt.figure(1, figsize=(20,12))

for i in range(1,9):
    plt.subplot(2,4,i)
    tennis_df[dimensions[i-1]].plot(kind='hist', title=dimensions[i-1])
```
![Histograms]({{ site.baseurl }}/images/2018-10-7-Tennis-Visualization/1_histograms.png "Histograms of key variables")

#### Evolution of winners' rankings in Grand Slam finals
```python
tourneys = ['Australian Open','Roland Garros','Wimbledon','US Open']

# New dataframe that stores final matches with valid winners' rankings
tennis_df_1 = tennis_df[~np.isnan(tennis_df['winner_rank']) & (tennis_df['round']=='F')].copy()
plt.figure(figsize=(20,4))

# For tournament, plot a graph with evolution of rankings
for i in range(1,5):
    plt.subplot(1,4,i)
    plt.title(tourneys[i-1])
    plt.scatter(tennis_df_1[tennis_df_1['tourney_name']==tourneys[i-1]]['tourney_year'],tennis_df_1[tennis_df_1['tourney_name']==tourneys[i-1]]['winner_rank'], s=tennis_df_1['loser_rank'])
    plt.gca().invert_yaxis()
    plt.xlabel('Year')
    plt.ylabel('Winners' Ranking')
```

![Scatter Plot]({{ site.baseurl }}/images/2018-10-7-Tennis-Visualization/1_scatter_plot.png "Scatter plot of Winners' Rankings")

In this graph we are using a [scatter plot](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.scatter.html) to represent rankings of players that won Grand Slam finals in each year. 

#### 