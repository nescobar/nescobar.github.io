---
layout: post
title: Analysis of ATP Tennis Competitions
categories: data visualization
tags: 'data_visualization, seaborn, matplotlib'
published: true
---
In this post I will make use of Python's libraries: pandas, matplotlib and seaborn to analyze data from **ATP tennis competitions from the year 1968 up to 2018**, including Grand Slams, Masters Series, Masters Cup and International Series competitions.

The __[dataset](https://github.com/JeffSackmann/tennis_atp)__ prepared by __[Jeff Sackmann](https://github.com/JeffSackmann)__ contains information on every single match played since 1968. It includes details such as match’s date, location, tournament type, surface, winner and loser, score, duration and additional statistics such as players’ rankings, age and height, aces, double faults, break points faced and saved, service points among other helpful stats for both players.

In the first section I will focus on competitions from 2000 to 2016 but later on will be working with the full data from 1968. The full notebook can be accessed [here](https://kaggle.com/nescobar/data-visualizations-of-atp-tennis-competitions/).

### Load the data
There is one _.csv_ file per year per tournament type. In this analysis I'm only loading _atp_matches_YYYY_ files which contain ATP the matches for that year. 

```python
path ='tennis_atp_data' 
files = glob.glob(path + "/*.csv")
tennis_df = pd.concat((pd.read_csv(f) for f in files))
```

### Deriving new columns
I derive two new columns to store year and year/month attributes.

```python
tennis_df['tourney_yearmonth'] = tennis_df.tourney_date.astype(str).str[:6]
tennis_df['tourney_year'] = tennis_df.tourney_date.astype(str).str[:4]
```

### Exploratory Data Analysis

#### Looking at distribution of key variables
```python
dimensions = ['winner_rank','loser_rank','winner_age','loser_age','winner_ht','loser_ht','w_svpt','l_svpt]

plt.figure(1, figsize=(20,12))

for i in range(1,9):
    plt.subplot(2,4,i)
    tennis_df[dimensions[i-1]].plot(kind='hist', title=dimensions[i-1])
```
![Histograms]({{ site.baseurl }}/images/2018-10-7-Tennis-Visualization/1_histograms.png "Histograms of key variables")



