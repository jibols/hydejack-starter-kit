---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: "Visualizing the English Premiership over the last decade"

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  Using EPL data to visualize Arsenal FCs performance over the last decade. 

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: assets/img/epl/PL-Lion.png

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
hide_description: false
hide_image: false

categories: [descriptive]
tags: []
languages: [Python]
---
![]({{site.url}}/assets/img/restaurants/header.jpg){:.lead}

<!--more-->

* dummy list
{:toc}

# Background
The Premier League (often referred to as the English Premier League (EPL) outside England) is the top level of the English football league system. Contested by 20 clubs, it operates on a system of promotion and relegation with the English Football League (EFL).

# Scenario
The dataset used contains the half-time, full-time match statistics for each of the 3800 games in the last 10 seasons. The dataset contains 22 fields, 18 fields contain actual match statistics while 4 fields contain details such as Date, HomeTeam, AwayTeam and the referee who officiated. Full description can be found at: https://datahub.io/sports-data/english-premier-league#data.

# Dataset
We have 3 tables to work with, courtesy of Wake County Open Data:
* [Restaurants](https://data-wake.opendata.arcgis.com/datasets/restaurants-in-wake-county)
<br>`Restaurants` gives details on each restaurant location, such as `NAME`, `PHONENUMBER`, and `RESTAURANTOPENDATE`.
* [Inspections](https://data-wake.opendata.arcgis.com/datasets/food-inspections)
<br>`Inspections` gives details on each restaurant inspection. Most restaurants have multiple entries in `inspections`,
including information like `INSPECTDATE`, `INSPECTOR`, and `SCORE`.
* [Violations](https://data-wake.opendata.arcgis.com/datasets/food-inspection-violations)
<br>`Violations` details the violations detected in each restaurant inspection. Most inspections have multiple entries
in `violations`, including information like `CATEGORY`, `SEVERITY`, and `COMMENTS`.

<br>All datasets are updated as of today.

## Cleaning
Our first task is reading the data from the url and wikipedia
```python
season_one = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-0910.csv", parse_dates=['Date'])
season_two = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1011.csv", parse_dates=['Date'])   
season_three = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1112.csv", parse_dates=['Date'])
season_four = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1213.csv", parse_dates=['Date']) 
season_five = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1314.csv", parse_dates=['Date'])
season_six = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1415.csv", parse_dates=['Date'])
season_seven = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1516.csv", parse_dates=['Date']) 
season_eight = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1617.csv", parse_dates=['Date'])
season_nine = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1718.csv", parse_dates=['Date'])   
season_ten = pd.read_csv("https://datahub.io/sports-data/english-premier-league/r/season-1819.csv", parse_dates=['Date'])
```
# Enrich data by adding stadium location
```python
clubs_to_drop = ['Oldham Athletic','Ipswich Town','Charlton Athletic','Bradford City', 'Swindon Town', 'Nottingham Forest', 'Barnsley',\
                 'Ipswich Town', 'Leeds United', 'Sheffield Wednesday', 'Sheffield United', 'Derby County']
stadia = pd.read_html("https://en.wikipedia.org/wiki/List_of_Premier_League_stadiums", header = 0)
columns = ['Club','Stadium','Coordinates', 'Closed', 'Opened']
stadia = stadia[0][columns]
stadia = stadia[~stadia['Closed'].isin([2006,1995,2004,1997,2001,2002,2005,2003])].reset_index(drop = True)
stadia = stadia[~stadia['Club'].isin(clubs_to_drop)].reset_index(drop = True)

# Clean Club columns for Queens Park Rangers and Crystal Palace
stadia['Club'].replace("Crystal Palace & Wimbledon", "Crystal Palace", inplace = True)
stadia['Club'].replace("Queens Park Rangers& Fulham", "Queens Park Rangers", inplace = True)

# Update the club names in the allseason dataframe to the full names of the clubs
allseasons['HomeTeam'].replace(np.sort(allseasons['HomeTeam'].unique()), np.sort(stadia['Club'].unique()), inplace = True)
allseasons['AwayTeam'].replace(np.sort(allseasons['AwayTeam'].unique()), np.sort(stadia['Club'].unique()), inplace = True)
stadia.drop_duplicates(subset ="Club",inplace=True)
```
