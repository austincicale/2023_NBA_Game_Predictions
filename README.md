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

This project was conducted during a Sports Analytics course (STOR 538) at The University of North Carolina at Chapel Hill throughout the Spring semester of 2023. While this project was a collaborative task, I was responsible for all programming aspects of the assignment. 

### Data Sources

Nathan Lauga's *games* data set, which includes observations for each NBA game from the 2003-2004 season to December 22 of the 2022-2023 season.

 

### Tools

### Data Cleaning/Preparation

For the *games* data set, we kept all variables except GAME_STATUS_TEXT and HOME_TEAM_WINS because they didn’t have potential for importance in data collection or predicting Total, Spread, or OREB.

- Then we created variables for Total and Spread, adding PTS_away to PTS_home for Total and subtracting PTS_away from PTS_home for Spread.

- It was apparent that the OREB variable wasn’t included in this data set, which was troubling because observations for OREB is necessary if we are going to create models predicting the variable. However, the games_details data set, also created by Nathan Lauga, included values for OREB and other potentially useful variables.

*games_details*: contains observations for each game on the player level, unlike the *games* data set
- Convert this data set into team level observations per game, the sum of player level stats were taken for all players with the same team and game ID.
- Selected eight variables from this data set we thought had potential for our predictions


