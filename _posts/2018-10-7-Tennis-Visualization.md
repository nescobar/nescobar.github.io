---
layout: post
title: Analysis of ATP Tennis Competitions
categories: data visualization
tags: 'data_visualization, seaborn, matplotlib'
published: true
---
In this post I will make use of Python's libraries: pandas, matplotlib and seaborn to analyze data from **ATP tennis competitions from the year 1968 up to 2016**, including Grand Slams, Masters Series, Masters Cup and International Series competitions.

The __[dataset](https://github.com/JeffSackmann/tennis_atp)__ prepared by __[Jeff Sackmann](https://github.com/JeffSackmann)__ contains information on every single match played since 1968. It includes details such as match’s date, location, tournament type, surface, winner and loser, score, duration and additional statistics such as players’ rankings, age and height, aces, double faults, break points faced and saved, service points among other helpful stats for both players.

In the first section I will focus on competitions from 2000 to 2016 but later on will be working with the full data from 1968.

### Import libraries

```python
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
import numpy as np
import random
import os
import glob
sns.set_style('white')

%matplotlib inline

plt.rcParams.update({'font.size': 14})
```

### Load the data

```python
path ='tennis_atp_data' 
files = glob.glob(path + "/*.csv")
tennis_df = pd.concat((pd.read_csv(f) for f in files))
```




