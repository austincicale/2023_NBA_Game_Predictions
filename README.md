# NBA 2023 Predictions: Spread, Total Points, Offensive Rebound Total

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)

### Project Overview

This project was conducted during a Sports Analytics course (STOR 538) at The University of North Carolina at Chapel Hill throughout the Spring semester of 2023. While this project was a collaborative task, I was responsible for all programming aspects of the assignment. This was a predictive modeling project for actual NBA games (all games between April 4 and April 9 of 2023), with the primary goal of designing models for the prediction of three variables – Spread, Total, and OREB. Below you can find clear definitions of these outcome variables:
- Spread = Home Points - Away Points
- Total = Home Points + Away Points
- OREB = Home OREB + Away  OREB


### Data Sources

Our search for data started with Nathan Lauga's [games](https://www.kaggle.com/datasets/nathanlauga/nba-games) data set, which includes observations for each NBA game from the 2003-2004 season to December 22 of the 2022-2023 season. 

 

### Tools

### Data Cleaning/Preparation

For the *games* data set, we kept all variables except GAME_STATUS_TEXT and HOME_TEAM_WINS because they didn’t have potential for importance in data collection or predicting Total, Spread, or OREB.

- Then we created variables for Total and Spread, adding PTS_away to PTS_home for Total and subtracting PTS_away from PTS_home for Spread.

- It was apparent that the OREB variable wasn’t included in this data set, which was troubling because observations for OREB is necessary if we are going to create models predicting the variable. However, the [games_details](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=games_details.csv) data set, also created by Nathan Lauga, included values for OREB and other potentially useful variables.

*games_details*: contains observations for each game on the player level, unlike the *games* data set
- Convert this data set into team level observations per game, the sum of player level stats were taken for all players with the same team and game ID.
- Selected eight variables from this data set we thought had potential for our predictions
  - FGA, FG3A, FTA, OREB, STL, BLK, TO, and PF

- Merged the eight predictors from our cleaned *games_details* data set into our *games* data set by matching GAME_ID and TEAM_ID. However, the games_details data did not have the GAME_ID variable split into home and away teams like the games data set, so we had to temporarily rename the HOME_TEAM_ID variable in games to TEAM_ID to match the game identification variable in the games_details data set. After renaming the variable, we were able to successfully merge the data by TEAM_ID and GAME_ID. The TEAM_ID variable in the games dataset was then renamed back to HOME_TEAM_ID. This same process was repeated to gather away team data.
- A similar process was used to add the W_PCT variable from Nathan Lauga's [ranking](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=ranking.csv) data set to the *games* data set. W_PCT was added my merging *games* and *ranking* by team id and date of each observation
- The shift of style of play in NBA basketball was accounted for, acknowledging the diminished importance of the traditional "Big Man" and increased role of point guards as prominent scoring threats. Accrediting Stephen Curry's influence on this transition, we decided to remove all data prior to the 2010 season, which marks the year Curry was introduced to the league.
- Data was also removed from the 2020 and 2021 NBA seasons to account for impact of COVID on environment of NBA games
- Playoff game data was removed as those games are played at a much higher instensity wherre teams go into the series with greater preparation for their specific opponent
- All observations before February of each season were ignored with the though that team composition changes throughout the year, so we focused specifically on data after the trade deadline which typically occurs in early February
- Removed missing values if they were to exist

Engine
