# NBA 2023 Predictions: Spread, Total Points, Offensive Rebound Total

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Predictive Modeling](#predictive-modeling)
- [Modeling Results](#modeling-results)
- [Calculation Method](#calculation-method)
- [Predictions Analysis](#predictions-analysis)
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
  - **Effective Field Goal Percentage (eFG_PCT)**: Following Dean Oliver’s [Four Factors of Basketball Success](https://www.basketball-reference.com/about/factors.html), this variable was calculated using the formula (FGM + 0.5 * 3FGM) / FGA. It measures the effectiveness of a team's shooting by accounting for the added value of three-pointers.
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

  - #### Functions for Generating Aggregated Variables
    ```r
    # Function for computing yearly averages:
    VAR_AVG_TY = function(var, team_col, var_col) {
      result = NBA_DATA %>%
      arrange(DATE) %>%
      group_by(SEASON, !!sym(team_col)) %>% 
      mutate(!!sym(var) := rollapplyr(lag(!!sym(var_col)), width = 1:(n()-1), FUN = function(x) mean(x, na.rm =TRUE), fill = NA)) %>%
      ungroup() %>%
      select(GAME_ID, !!sym(var))
      NBA_DATA <<- merge(NBA_DATA, result, by = c("GAME_ID"), all.x=TRUE) 
      return(result)
    }

    # Function for computing last 3 game averages:
    VAR_AVG_L3 = function(var, team_col, var_col) {
      result = NBA_DATA %>%
      arrange(DATE) %>%
      group_by(SEASON, !!sym(team_col)) %>% 
      mutate(!!sym(var) := ifelse(row_number() > 3, lag(rollapply((!!sym(var_col)), width = 3, FUN = mean, na.rm = TRUE, fill = NA, align = "right", partial = TRUE), n = 1),          NA))   %>%
      ungroup() %>%
      select(GAME_ID, !!sym(var)) 
      NBA_DATA <<- merge(NBA_DATA, result, by = c("GAME_ID"), all.x=TRUE) 
      return(result)
    }
    ```
  - #### Example Usage: Generating Aggregated Variables for PTS
    ```r
    VAR_AVG_TY("AVG_PPG_TY_H", "TEAM_H", "PTS_home")
    VAR_AVG_TY("AVG_OPPG_TY_H", "TEAM_H", "PTS_away")
    VAR_AVG_L3("AVG_PPG_L3_H", "TEAM_H", "PTS_home") 
    VAR_AVG_L3("AVG_OPPG_L3_H", "TEAM_H", "PTS_away")

    VAR_AVG_TY("AVG_PPG_TY_A", "TEAM_A", "PTS_away")
    VAR_AVG_TY("AVG_OPPG_TY_A", "TEAM_A", "PTS_home")
    VAR_AVG_L3("AVG_PPG_L3_A", "TEAM_A", "PTS_away") 
    VAR_AVG_L3("AVG_OPPG_L3_A", "TEAM_A", "PTS_home")
    ```

- #### Additional Data Source
  In addition to our primary dataset, we collected supplementary data from [teamrankings.com](https://www.teamrankings.com/nba/team-stats/). This dataset encompasses NBA team data for each date of the 2022-2023 season. Specifically, it includes comprehensive data for all of our selected potential predictors, categorized by team and providing the following metrics:
  - Yearly average
  - Last 3 game average
  - Last game performance
  - Home game average
  - Away game average
  - Previous year average

  After the April 2, 2023 games, the updated data from teamrankings.com was used to calculate our predictions for the games scheduled from April 4 to April 9.

### Predictive Modeling

The following steps provide an overview of the methodology used to create predictive models for Spread, Total, and OREB:

1. **Data Splitting**: The cleaned data was divided into training and testing sets. The training data consisted of all games that occurred in February or March, while the testing data included all games in April. The data was split this way to ensure the models could properly predict April games after being trained to predict games in February and March, since the game outcomes being predicted in this project are those occurring in April.
    ```r
    # Separate "DATE" variable into "year", "month", and "day" variables:
    NBA_DATA = NBA_DATA %>%
      separate(DATE, sep="-", into = c("year","month","day"))

    # Create training set, consisting of February and March games
    TRAIN_DATA = NBA_DATA %>%
      filter(month == "02" | month == "03")

    # Create testing set, consisting of April games
    TEST_DATA = NBA_DATA %>%
      filter(month == "04")
    ```
2. **Bi-directional Stepwise Progression**: We applied bi-directional stepwise progression on the training set to identify potential independent variables. All non-aggregate variables were considered as potential predictors.
3. **Cross-validation and Model Evaluation**: The model identified in step 2 was cross-validated on the testing data to ensure accurate predictions. We analyzed the mean, variability, and normality conditions to assess the model's performance. Additionally, the shrinkage value was closely examined to ensure model reliability.
4. **Refinement with Aggregate Variables**: Bi-directional stepwise progression was applied again, this time using aggregate values for the variables identified as good predictors in the previous step. Outliers were checked by analyzing studentized and standardized residuals, leverage values, and Cook's distance. After ensuring no significant outliers, the model was cross-validated to assess its accuracy in predicting the testing data.
5. **Final Model Creation with LASSO Cross-validation**: A final model was created using aggregate values for the identified good predictors, employing LASSO cross-validation. The coefficients from these models were utilized to make our final predictions.

### Modeling Results
This section provides an overview of the results obtained from our predictive modeling for Spread, Total, and OREB. Below are the coefficients for each final model, Actual vs Predicted plots, and Root Mean Squared Error (RMSE) values. These insights offer an understanding of the accuracy and performance of our predictive models.

- **Spread:**
  - **Coefficients:**

    <img width="633" alt="Screenshot 2024-05-07 at 2 03 56 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/b64a53af-e2d9-46db-8034-15adceadec43">

  - **Actual Spread vs Predicted Spread Plot:**
    ```r
    yhat_spread = predict(SPREAD_MOD, newdata = TEST_DATA_SPREAD)
    spread.test = TEST_DATA_SPREAD$Spread
    plot(spread.test, yhat_spread, ylab = "Predicted Spread", xlab = "Actual Spread")
    abline(0,1, col = 'red')
    ```
  <img width="720" alt="Screenshot 2024-05-07 at 2 04 30 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/8a92473d-e4ce-401a-8671-98664a736f70">
 
  - **RMSE:** 12.8276
  
- **Total:**
  - **Coefficients:**
  
    <img width="583" alt="Screenshot 2024-05-07 at 2 06 11 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/86142993-ad93-44f1-ad08-3fdbb2e806b7">

  - **Actual vs Predicted Total Plot:**
    ```r
    yhat_total = predict(TOTAL_MOD, newdata = TEST_DATA_TOTAL)
    total.test = TEST_DATA_TOTAL$Total
    plot(total.test, yhat_total, ylab = "Predicted Total", xlab = "Actual Total")
    abline(0,1, col = 'red')
    ```
  <img width="669" alt="Screenshot 2024-05-07 at 2 05 42 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/06241b75-a903-43e4-ba55-0d5157b8a126">

   - **RMSE:** 18.9079

- **OREB:**
  - **Coefficients:**

    <img width="577" alt="Screenshot 2024-05-07 at 2 07 42 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/006498cf-a05a-465d-864e-1194e0b7f013">

  - **Actual vs Predicted Plot:**
    ```r
    yhat_oreb = predict(OREB_MOD, newdata = TEST_DATA_OREB)
    oreb.test = TEST_DATA_OREB$OREB
    plot(oreb.test, yhat_oreb, ylab = "Predicted OREB", xlab = "Actual OREB")
    abline(0,1, col = 'red')
    ```
  <img width="689" alt="Screenshot 2024-05-07 at 2 07 19 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/ca539489-cee0-4e2d-872e-a4904e59fd1a">
    
  - **RMSE:** 5.4176
    
### Calculation Method

In this section, I outline the method used to generate predictions for Spread, Total, and OREB. The process involved utilizing Excel / Google Sheets and the coefficients obtained from our previous predictive models.

- #### Data Import and Setup:
  - Each variable's data was imported from teamrankings.com into separate sheets using the IMPORTHTML function in Google Sheets. The data was refreshed before making final predictions.
  - The first six rows of the first sheet resembled the following:
    <img width="693" alt="Screenshot 2024-05-07 at 11 19 23 AM" src="https://github.com/austincicale/2023_NBA_Game_Predictions/assets/77798880/4c1aebc9-cf54-43ad-90ae-13cf5d02b5a3">
   - Each variable is listed in column A, data imported from teamrankings.com is listed in columns B and C, and the coefficients for the Total model are listed in columns D and E, corresponding to Home and Away respectively.
  - Similar to Total, the coefficients for Spread and OREB were listed in columns for Home and Away, but they are not listed in the image above due to space.
  - If a coefficient was not used in a particular model, the "Weight" was set to 0. 

- #### Calculation Process:
  - Columns B and C utilized Excel's VLOOKUP function to search for the corresponding value based on the team name from the relevant variable's sheet.
  - For instance, B5 looked for "Charlotte" in the 'Possessions Per Game' sheet and returned the value in the 4th column, representing the Last 3 data provided by teamrankings.com.
  - To calculate the predictions for Total, each value in column B was multiplied by the corresponding coefficient in column D, and each value in column C was multiplied by the corresponding coefficient in column E. The results from these multiplications were added together to obtain the predictions for Total.
  - An identical process was used to calculate predictions for Spread and OREB.

### Predictions Analysis
Once we obtained predictions for each of the three outcome variables, we thoroughly analyzed historical data to determine if any revisions were necessary. For instance, when examining our Spread predictions, we calculated the average spread across all our predictions.

Our Spread model initially projected that all home games would win by an average of 5.5056, marking an increase of 2.7923 from the average spread of 2.7133 observed across the rest of the 2022-23 season. However, upon reviewing historical data dating back to 2010, we found that the spread increased by only 0.1066 in April compared to other months.

Based on this analysis, we concluded that our model tends to overestimate home advantage by 2.6857 (2.7133 - 0.1066). To rectify this discrepancy, we adjusted our spread predictions accordingly by subtracting 2.6857 from each spread. As a result, our final spread predictions now range from -7.45 to 12.83, with an average of 2.82.

Similar adjustments were made to account for overestimations in our Total and OREB predictions. Our final predictions for Spread, Total, and OREB for all NBA games between April 4 and April 9, 2023, can be found [here](Predictions.csv).

### References

- YouTube Video: [How to Build a Sports Betting Model for NBA Against the Spread and Totals](https://www.youtube.com/watch?v=U7r_QVcBmBE)
- Book: [Basketball on Paper](http://www.basketballonpaper.com/) by Dean Oliver

  
