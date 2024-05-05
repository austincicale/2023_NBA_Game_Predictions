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

This project was undertaken as part of the Sports Analytics course (STOR 538) at The University of North Carolina at Chapel Hill during the Spring semester of 2023. While the project was collaborative, I assumed responsibility for all programming aspects. The objective was to develop predictive models for real NBA games played between April 4 and April 9 of 2023, focusing on three key variables: Spread, Total, and OREB. For clarity, these variables are defined as follows:

- Spread = Home Points - Away Points
- Total = Home Points + Away Points
- OREB = Home OREB + Away  OREB


### Data Sources

Our search for data started with Nathan Lauga's [games](https://www.kaggle.com/datasets/nathanlauga/nba-games) dataset,  covering NBA game observations from the 2003-2004 season through December 22 of the 2022-2023 season. Recognizing the need for supplementary data, we incorporated two additional datasets, also compiled by Nathan Lauga. The [games_details](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=games_details.csv) dataset provided valuable insights, including OREB and other potentially useful variables. Additionally, we utilized the [ranking](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=ranking.csv) dataset to integrate a win percentage variable (WIN_PCT) for both home and away teams in each game.

### Tools

### Data Cleaning/Preparation

- #### Variable Removal
  - ***games* dataset**: retained all variables except GAME_STATUS_TEXT and HOME_TEAM_WINS due to their lack of potential significance in data collection or predicting Total, Spread, or OREB.
  - ***games_details* dataset**: removed all variables except FGA, FG3A, FTA, OREB, STL, BLK, TO, and PF as they were deemed essential for analysis.
  - ***ranking* dataset**: retained HOME_RECORD and ROAD_RECORD variables for further analysis.
  
- #### Outcome Variable Creation
  - **Total and Spread**: Using data from the *games* dataset, the Total variable was derived by summing PTS_away and PTS_home, while the Spread variable was calculated by subtracting PTS_away from PTS_home.
  - **OREB**: While the necessary elements to create the OREB variable were absent in the *games* dataset, the *games_details* dataset possesses an OREB variable. However, the *games_details* dataset records observations at the player level, unlike the *games* dataset, which contains observations at the team level. To reconcile this disparity, the player-level stats for each team and game ID were aggregated by summing them, thus converting the dataset to team-level observations.

- #### Data Merging
  - ***games_details* dataset integration**: merged into the *games* dataset by matching GAME_ID and TEAM_ID
  - ***ranking* dataset integration**: merged into the *games* dataset by matching the team ID and date of each observation
    
- #### Observation Removal
  - **Shifts in NBA Style of Play**: Recognizing the evolution of NBA basketball with a diminished emphasis on traditional "Big Man" roles and an increased prominence of point guards as scoring threats, all data before the 2010 season was removed to account for Stephen Curry's influential transition into the league.
  - **Impact of COVID-19**: Data from the 2020 and 2021 NBA seasons was excluded to accommodate the environmental impact of COVID-19 on NBA games.
  - **Playoff Game Removal**: Playoff game data was omitted due to the significantly higher intensity and specialized preparation involved, which could potentially skew the analysis.
  - **Pre-Trade Deadline Focus**: Observations before February of each season were disregarded to capture team composition changes post-trade deadline, typically occurring in early February.
  - **Handling Missing Values**: Any missing values were removed to ensure data integrity and consistency.

- #### Feature Engineering
  - **Possessions (POSS)**: Created to determine the number of possessions a team has throughout a game, this variable was calculated as POSS = 0.96 * (FGA + TO + 0.44 * FTA - OREB). The .96 is used to account for the fact that some possessions end in offensive rebounds, and not in turnovers or missed field goals. This formula is widely used to calculate possessions if the exact number is not known. The portion in parentheses should be familiar as part of Dean Oliver’s calculation of turnover percentage.
  - **Win Percentage (WIN_PCT)**: This factor was calculated by combining the HOME_RECORD and AWAY_RECORD variables
  - **Effective Field Goal Percentage (eFG_PCT)**: Using Dean Oliver’s Four Factors of Basketball, this variable was calculates using (FGM + .5*3FGM) + FGA
  - **Turnover Percentage (TO_PCT)**: TO/POSS
  - **Defensive Rebound Percentage (DREB_PCT)**: DREB / (DREB + Opp OREB)
  - 


- Engineered Variables:
  For each variable we created, it's important to note that we created one for the home team and one for the away team
  - Created a possessions variable (POSS) to determine the number of possessions a team has throughout a game. This variable was calculated as
    POSS = 0.96 * (FGA + TO + 0.44 * FTA - OREB). The .96 is used to account for the fact that some possessions end in offensive rebounds, and not in turnovers or missed field goals. This formula is widely used to calculate possessions if the exact number is not known. The portion in parentheses should be familiar as part of Dean Oliver’s calculation of turnover percentage.
- Calculated a W_PCT variable by combining the HOME_RECORD and ROAD_RECORD variables in the *ranking* data set


### Data Modeling

### Calculation Mehtod

### Predictions


  
