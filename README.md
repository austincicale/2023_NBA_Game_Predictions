# NBA 2023 Predictions: Spread, Total Points, Offensive Rebound Total

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Predictive Modeling](#predictive-modeling)
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

Our search for data started with Nathan Lauga's [games](https://www.kaggle.com/datasets/nathanlauga/nba-games) dataset,  covering NBA game observations from the 2003-2004 season through December 22 of the 2022-2023 season. Recognizing the need for supplementary data, we incorporated two additional datasets, also compiled by Nathan Lauga. The [games_details](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=games_details.csv) dataset provided valuable insights, including OREB and other potentially useful variables. Additionally, we utilized the [ranking](https://www.kaggle.com/datasets/nathanlauga/nba-games?select=ranking.csv) dataset to integrate a win percentage variable (WIN_PCT) for both home and away teams in each game. Updated team statistics for the 2022-2023 season were used to make our final predictions, as further explained in the [Additional Data Source](#additional-data-source) section.

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

- #### Engineered Variables
  - **Possessions (POSS)**: Quantifying the number of possessions a team has throughout a game, this variable is calculated using the formula: POSS = 0.96 * (FGA + TO + 0.44 * FTA - OREB). The coefficient of 0.96 adjusts for the instances where possessions end in offensive rebounds rather than turnovers or missed field goals. This formula, widely attributed to [Dean Oliver](http://www.basketballonpaper.com/), is a standard method for estimating possessions when the exact count is unavailable. Notably, the expression within the parentheses is a familiar component of Oliver’s turnover percentage calculation, which can be found [here](https://www.basketball-reference.com/about/factors.html).
  - **Win Percentage (WIN_PCT)**: This variable reflects the win percentage of both the home and away teams prior to each game observation. It was derived from the HOME_RECORD and AWAY_RECORD variables by dividing the number of wins by the sum of wins and losses.
  - **Effective Field Goal Percentage (eFG_PCT)**: Following Dean Oliver’s [Four Factors of Basketball](https://www.basketball-reference.com/about/factors.html), this variable was calculated using the formula (FGM + 0.5 * 3FGM) / FGA. It measures the effectiveness of a team's shooting by accounting for the added value of three-pointers.
  - **Turnover Percentage (TO_PCT)**: This variable represents the turnover percentage of a team and was calculated by dividing turnovers (TO) by possessions (POSS). It provides insight into a team's ability to maintain possession of the ball.
  - **Defensive Rebound Percentage (DREB_PCT)**: This variable indicates the proportion of available defensive rebounds a team secures and was calculated by dividing defensive rebounds (DREB) by the sum of defensive rebounds and opponent offensive rebounds (Opp OREB). It offers insights into a team's defensive rebounding efficiency.

- #### Selected Potential Predictors
  For our predictive modeling, we carefully selected potential variables believed to be valuable predictors of Spread, Total, and OREB. These predictors were chosen based on their relevance and significance in previous basketball analytics research. Below are the selected potential predictors:
  
  <img width="540" alt="Screenshot 2024-05-06 at 11 09 29 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/2ea5ff9e-9632-4e1b-a21e-08bb510c3eef">
  
  *Note: To distinguish between home and away teams, each variable is accompanied by a suffix: _H for the home team and _A for the away team.

- #### Aggregated Variables for Selected Potential Predictors
  To facilitate predictive modeling for future NBA games, we constructed lag variables representing historical averages for each selected potential predictor. These lag variables offer insights into team performance trends over time. For each predictor, we generated the following aggregated variables:

   <img width="483" alt="Screenshot 2024-05-06 at 11 35 45 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/505c15ef-6758-4b08-bcaa-80c0b899bf6d">
   
   *Note: "VAR" is replaced with the specified potential predictor.

- #### Additional Data Source
  In addition to our primary dataset, we collected supplementary data from [teamrankings.com](https://www.teamrankings.com/nba/team-stats/). This dataset encompasses NBA team data for each date of the 2022-2023 season. Specifically, it includes comprehensive data for all of our selected potential predictors, categorized by team and providing the following metrics:
  - Yearly average
  - Last 3 game average
  - Last game performance
  - Home game average
  - Away game average
  - Previous year average

  After the games of April 2, 2023, the updated data from teamrankings.com was used to calculate our predictions for the games scheduled from April 4 to April 9.

### Predictive Modeling

The following steps provide an overview of the methodology used to create predictive models for Spread, Total, and OREB:

1. **Data Splitting**: The cleaned data was divided into training and testing sets. The training data consisted of all games that occurred in February or March, while the testing data included all games in April. The data was split this way to ensure the models could properly predict April games after being trained to predict games in February and March, since the game outcomes being predicted in this project are those occurring in April. (ADD CODE)
2. **Bi-directional Stepwise Progression**: We applied bi-directional stepwise progression on the training set to identify potential independent variables. All non-aggregate variables were considered as potential predictors.
3. **Cross-validation and Model Evaluation**: The model identified in step 2 was cross-validated on the testing data to ensure accurate predictions. We analyzed the mean, variability, and normality conditions to assess the model's performance. Additionally, the shrinkage value was closely examined to ensure model reliability.
4. **Refinement with Aggregate Variables**: Bi-directional stepwise progression was applied again, this time using aggregate values for the variables identified as good predictors in the previous step. Outliers were checked by analyzing studentized and standardized residuals, leverage values, and Cook's distance. After ensuring no significant outliers, the model was cross-validated to assess its accuracy in predicting the testing data.
5. **Final Model Creation with LASSO Cross-validation**: A final model was created using aggregate values for the identified good predictors, employing LASSO cross-validation. The coefficients from these models were utilized to make our final predictions.

### Calculation Methos

### Predictions

### References

[youtube](https://www.youtube.com/watch?v=U7r_QVcBmBE)


  
