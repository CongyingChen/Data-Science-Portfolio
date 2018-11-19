# Forecasting Outcomes of 2018 Women’s March Madness
## W207 Final Project 
## Julia Buffinton, Charlene Chen, Arvindh Ganesan,  Prashant Kumar Sahay
### Due: 4/17/18

In this project, we use historical data about NCAA Division I women's college basketball games (both tournament and regular season) and teams, with the goal of predicting the winning probabilities for every possible matchup of teams of the 2018 NCAA "March Madness" tournament.  

## Business Understanding

As mentioned in the brief introduction above, the purpose of this project is to build a model to predict the outcome probabilities for every possible matchup of teams of the 2018 NCAA Division I Women's Basketball Tournament. The NCAA tournament is also well-known as March Madness, not only because it occurs annually in March, but also because of the fast-paced elimination mechanism (single elimination) and the surprising results that often arise when pairing teams across leagues that have previously never played each other (upsets where the lower-seeded team defeats the higher-seeded team). Therefore, building a better model to predict the results of the tournament is interesting and difficult, and the prediction of the winning probabilities for every possible matchup becomes the first and most critical part for predicting the NCAA bracket.  

## Data Understanding

In this project, the data sets include historical data of tournaments and regular season games back to 1998, team information, etc. We will begin exploring the data such as detailed performance data for tournaments and regular season games and previous and current seed numbers in the tournament.

- What are the raw data sources?

  Raw data sources include .csv files from Kaggle:
  1. WCities - city number and associated English city/state names
  2. WGameCities - winning team id, losing team id, and city id
  3. WNCAATourneyCompactResults - season, day number, winning team id, winning score, losing team id, losing score, location, and number of overtimes for each tournament game since 1998
  4. WNCAATourneyDetailedResults - in addition to the above, for each team, includes 2 pointers attempted/made, 3 pointers attempted/made, free throws attempted/made, offensive/defensive rebounds, assists, time outs, steals, blocks, and personal fouls for each tournament game since 1998
  5. WNCAATourneySeeds - seed number and associated team for each tournament since 1998
  6. WNCAATourneySlots - indicates seeds of teams that play at each round of 64 'slot'  
  7. WRegularSeasonCompactResults - season, day number, winning team id, winning score, losing team id, losing score, location, and number of overtimes for each regular season game since 1998
  8. WRegularSeasonDetailedResults - in addition to the above, for each team, includes 2 pointers attempted/made, 3 pointers attempted/made, free throws attempted/made, offensive/defensive rebounds, assists, time outs, steals, blocks, and personal fouls for each tournament game since 1998
  9. WSeasons - locations of championship game played in each region for tournaments since 1998
  10. WTeams - team id and associated English spelling team name
  11. WTeamSpellings - alternative spellings and abbreviations for each team

- What does each 'unit' (e.g. row) of data represent?

  The bulk of the information we plan to use for analysis are in the detailed results from regular season and tournament games. Each unit of data represents a game that was played between two teams, and the fields are relevant statistics.

- What are the fields (columns)?

  These include game-level statistics such as: season (year), day number in the season the game was played, teams participating, final score, location, and number of overtimes. Additionally, it includes team-level statistics for each team: 2 pointers attempted/made, 3 pointers attempted/made, free throws attempted/made, offensive/defensive rebounds, assists, time outs, steals, blocks, and personal fouls.

- EDA
  - Distribution of each feature

    The count of winning games of each team from either tournament or regular season shows the overall performance of that team across years. Tops 5 count of winning time of regular season is UConn (632), Tennessee (554), Duke (544), Stanford (543), WI Green Bay (531). However, the count of winning games among top 15 teams are quite close to each other, indicating overall high performance of those team in regular season. The top 5 count of winning games in the tournament is: UConn (93), Tennessee	(69), Duke (52), Stanford (52), and Notre Dame (51). We noticed that the top rankings of tournament game wins is quite similar to winning games in the regular season, indicating a consistent performance of each team from regular season to tournament.

    Additionally, we explored the possibility of using past game performance (regular season and in the tournament) as a feature. Of the 364 teams, 252 of them have participated in the NCAA March Madness tournament at least once since 1998. Median = 3 appearances; mean = 5.33; mode = 0,1. Nonzeros: 252/364. This indicates that 1) not all teams make it to the tournament, even over a 21 year period. Also, a higher mean than median suggests a positive skew, meaning that there are several teams that have appeared many times in the tournament (max = 21; UConn, Notre Dame, Stanford, Tennessee), but most teams appear infrequently or not at all.

    Our dataset also includes seeds for all teams in the tournament since 1998. Seeds are between 1 and 16, within each region (4 regions). Each year, there are 4 teams with seeds of each value. Some teams seem to regularly perform well in the tournament, so we examined the average seed value for teams. The top 5 teams with the best (lowest) average seed are: UConn (1.24, 21 appearances), Duke (2, 20 appearances), Tennessee (2, 21 appearances), USC (2.56, 9 appearances), and Baylor (2.59, 17 appearances).

    For the relevant fields statistics, we did the Kernel density estimation (KDE) plot to compare the winning team and losing team. Almost all the fields metrics have similar distribution between winning team and losing team. However, winning teams show an advantage in the means of Field Goal Percentage (FGP), 2 point field goal shooting (FGP2), 3 point field goal shooting (FGP3),and Assist Per Game (Ast), indicating those metrics maybe important features to examine in detail and be used in the modeling.  

  - Missing values

    Right now, we do not see any missing values in the fields. There may be missing games (rows), but it is not apparent at this point. It would be too difficult to cross-reference every game played in the past 21 seasons, so we assume the data is complete.

  - Distribution of target

    The output data for training and development data set is the win or lose label for each game. Since each team will either win or lose the game, the distribution of output data is uniform distributed with equally probability of value 1 and 0.

  - Relationships between features

    There are 13 field performance features for each team in each game of tournaments or regular seasons (FGM, FGA, FGM3, FGA3, FTM, FTA, OR, DR, Ast, TO, Stl, Blk, PF). These indicate 2 pointers made/attempted, 3 pointers made/attempted, free throws made/attempted, offensive/defensive rebounds, assists, time outs, steals, blocks, and personal fouls, respectively.

    PCA, as a tool to reduce the dimension and do the feature construction, is used to examine the feasibility of aggregating all these features into one feature, overall performance. However, after PCA, the first dimension only maintains 40% of the total variance of all features, and in order to reach the 90% variance, 6 dimensions need to be kept, which rather negates the purpose of PCA, as there are only 13 features to begin with. Therefore, for now, we believe that this case is not suitable for using PCA for feature construction or dimension reduction, we turn to other methods of feature selection and construction.  

    Below are some insights about the relationship between those features and score margin, which we use as a proxy for success (it's imperfect at best, but is useful for regular season EDA, when teams are playing within their leagues):

      **Field Goal Percentage Difference: WFGM/WFGA - LFGM/LFGA** Calculate the percentage of 2 point field goals made for winning and losing team, then determine the difference between the percentages. This field goal percentage difference is strongly associated with win margin.

      **Three-Point Field Goal Percentage Difference: WFGM3/WFGA3 - LFGM3/LFGA3** Similar to the above, we were able to calculate the percentage of 3 point field goals made for both winning and losing teams. The correlation between 3 point field goal percentage difference and win margin is not too strong. This suggests that teams that win a game with a larger margin tend to have only slightly better 3 point field goal percentage than the losing team in that game.


      **Free throw percentage: WFTM/WFTA** Free throw performance by itself is not strongly associated with win margin. This is not surprising. Some teams have a lower 2 point field goal rate but they draw a larger number of personal fouls from the opposing team. This can lead to a difference in tactics of how the teams accumulate 2 point goals without impacting their overall success in scoring 2 point goals.

      **Rebound difference: (WOR + WDR) - (LOR + LDR)** Determine the difference between the number of rebounds achieved (both defensive and offensive) of winning and losing teams. This indicates relative performance in offensive and defensive rebounds: it has a significant correlation with win margin.

      **OR + DR** as the rebound performance: the winning team's rebound performance is strongly associated with the number of field goal attempts. The correlation is less strong for the losing team.

      **Assist difference: WAst - LAst** as relative performance in assists: it has a strong correlation with win margin. Assists indicate a more orchestrated style of play and may be an indicator how a team collaborates and creates opportunities on the field.

      **Steal difference: WStl - LStl** as relative performance in steals: it has a modest correlation with win margin.

    Then we explored how performance on different measures has impacted game outcomes over time. Over the years, the importance of field goal performance in determining game outcomes has remained constant whereas the importance of assists and rebounds in determining game outcomes has been rising.

  - Other idiosyncrasies?

    One thing that was very apparent in EDA is the overall dominance of the University of Connecticut team (and several other teams such as Notre Dame or Tennessee) in regular season and tournament games. As compared to NCAA men's basketball, few teams regularly achieve success in regular season and tournament play, UConn and these other teams have been the best for many years in a row, suggesting some other factor besides player-level skills (since turnover in college basketball is relatively high) contributes to the success (such as a coach, etc.).

     On that note, we plan to continue to explore the appropriate amount of data that should be used to predict tournament success. For example, it seems unlikely that performance in games in 1998 indicates success of a completely new team in 2018. We hypothesize that approximately a 5-year window may be most informative.


## Data Preparation

- What steps are taken to prepare the data for modeling?
  - feature transformations? engineering?

    While conducting EDA, we noticed that the regular season statistics are not necessarily indicative of tournament performance due to the difference in competitiveness between leagues. In general, ACC, American Athletic Conference, A-10, Big 12, Big East, Big Ten, Mountain West, Pac-12, and SEC are seen as the most competitive leagues. Leagues such as the Patriot or Ivy leagues, while also Division I, are not typically as competitive. We will use aggregate statistics for win percentage in the tournament as an indicator of a league's competitiveness. This seems to track with general knowledge of the more competitive leagues, particularly for women's basketball: ACC (0.55), American (0.24), A-10 (0.27), Big 12 (0.54), Big East (0.39), Big Ten (0.44), Mountain West (0.17), Pac-12 (0.54), and SEC (0.59).

    Eventually, more transformation has to be done for training/prediction so that the input for each is a pairing consisting of two teams + associated stats (which may include, based on our exploration above: past tournament performance, league membership, regular season game aggregate statistics, etc.) for each team in the matchup.

  - table joins? aggregation?

    Currently, data on regular season + postseason play + team/league information is in separate files, with statistics for each game played. We are adding to that aggregate statistics about the strength of each team playing as well.

- Precise description of modeling base tables.

  - What are the rows/columns of X (the predictors)?

    After finalizing feature selection and construction, we will have a detailed explanation of X.

  - What is y (the target)?

    The output data _y_ for training and development data set is the win (1) or loss (0) label of the first team in the matchup for each game.

    However, because we are predicting the winning probability by logistic model, the target of the deployment or the output of the model _y_ is the probability that the first team (lowest team #) will win. To minimize duplication, team pairs are always listed with the smaller number team id first.


Feature Engineering:

Our EDA identified significant correlations between a team’s performance during the regular season and its wins during the tournament. Based on the findings of the EDA, we utilized both regular season and tournament data for feature development. The following regular season data was used for feature development:
•	Field goals attempted
•	Field goals made
•	Free throws attempted
•	Free throws made
•	Blocks
•	Rebounds
•	Assists
•	Steals
•	2 point goals
•	3 point goals

We calculated intrinsic percentage-based features as follows:
•	Field goal percentage = Field goals made / Field goals attempted
•	Free throw percentage = Free throws made / Field goals attempted

In addition, we synthesized the concepts of Point Opportunity Developed (POD) and Opportunity Conversion Rate (OCR). The concept of Point Opportunity Developed (POD) represents the effectiveness with which a team develops new opportunities. It is defined as:

POD = three-point goals attempted * 3 + two-point goals attempted * 2 + free throws attempted * 1

The concept of Opportunity Conversion Rate (OCR) represents the effectiveness with which a team seizes an opportunity to score. It is defined as:

OCR = score in the game / point opportunity developed

Features related to tournament performance were calculated cross sectionally. For example, we compared league performance across time periods to operationalize the notion of a league’s relative strength.  The relative strength of the a league is intended to be used to normalize teams’ performance during the regular season.

a league’s performance in tournaments across multiple years was calculated 
Feature Scaling and Standardization:

All features were calculated the performance of a team during a season and within a league. We calculated the features in following steps:
1.	Aggregation: Calculate the performance of a team within season and league
2.	Centering: De-mean the data by subtracting the corresponding league level mean
3.	Normalization: Rescale by dividing by the corresponding league level standard deviation
4.	Binning: To dampen the effect of small variations (noise)

Later in the project, we utilize these binned features as ordinal measures.

## Modeling

- Model that we are using

  We plan to use a logistic regression model as the base model because we are dealing with a binary outcome (win vs. loss), and predicting the winning probability of the first team in each matchup. But we may try the concept of bootstrapping from random forest to improve the model.

- Assumptions

  Because we are using logistic regression, we assume:

  1. the dependent variable to be binary

  2. each features or observation to be independent, and model have little or no multicollinearity

  3. dependent (_y_) and independent (_x_) variables are linearly related to the log odds

- Regularization

  We will discuss the regularization after model is build.

We used an ensemble approach to modeling by combining Naïve Bayes, Random Forest, and Logistic Regression. We used a cross-validation training dataset to generate first stage classification using Naïve Bayes and Random Forest models. In the second stage, we used the first stage classifiers as features to generate the final classification log probabilities.

We ran into overfitting issues with SVM and random forest classifiers. We were getting very impressive log losses with training data but the models did not generalize well to test data. This is probably due to the fact that we have less than a thousand tournament match ups but over 25 synthesized features. To produce generalizable models we reduced the dimensionality of data using PCA. This resulted in models that did not fit or predict well.

Our approach of using the 2017 data as a test set also caused issues. Although we used cross validation to select models, we think that some of our modeling decisions were based on how well the model performed with test data. As a result, in hindsight, we ended up selecting models that did well with 2017 data but did not generalize as well to future years. In hindsight, it is critically important to maintain heterogeneity in training and test data sets to ensure that we do not optimize models to a particular kind of data generating process (in this case, the 2017 tournament).




## Evaluation

- Log Loss

  - Log Loss is the most important classification metric based on probabilities. It is also a key way of evaluation of our prediction from Kaggle NCAA page.
  - It is also called the Log Likelihood Function. The log of the likelihood that the 2018 tournament bracket actually happens based on our prediction of wining probability of each team matchups.

  $$
  LogLoss = \sum_{i=1}^{n} [y_i log(\hat{y_i})+(1-y_i) log(1-\hat{y_i})]
  $$

  - The result will be a negative number that's in a range computers keep track of. However, Scikit-learn has a conventin in its metrics that lower scores are better.
      - So, scikit-learn report $$-1* \frac{LogLoss}{NumObservations}$$

  $$
  LogLoss = -\frac{1}{n} \sum_{i=1}^{n} [y_i log(\hat{y_i})+(1-y_i) log(1-\hat{y_i})]
  $$

      The division by the number of datapoints is used so the range of values doesn't systematically vary with the dataset size.
      - The rage of that is 0 to infinit.
      - The lower the metrix is, the better model performance is.
  - Log loss penalizes both types of errors, but especially those predications that are confident and wrong.

  - In our case, the Log Loss value is ......

- Accuracy

  - Accuracy measures a fraction of the classifier's predictions that are correct.
  - In our case, the accuracy is ...

- F1 measure with Cross Validation

  - **Precision** is the fraction of positive predictions that are correct, which means the team who we predicts as winner that are actually winner.
      - (TP = True Positive, FP = False Positive)

  $$ P = \frac{TP}{TP+FP}$$


  - **Recall**, the true positive rate, is the fraction of the truly positive instances that the classifier recognizes. A recall score of one indicates that the classifier did not make any false negative predictions.
  In our case, recall is the fraction of winner team that were truly classified as winner, according to our prediction of winning proberbility.
      - (TP = True Positive, FN = False Negative)

  $$ P = \frac{TP}{TP+FN}$$

  - In our case, the precis- The **F1 measure** is the harmonic mean, or weighted average, of the precision and recall scores. Also called the f-measure or the f-score.
    - (P = precision, R = recall)
    $$ F1 = 2 \frac{PR}{P+R}$$


  - The F1 measure penalizes classifiers with imbalanced precision and recall scores, like the trivial classifier that always predicts the positive class.
  - A model with perfect precision and recall scores will achieve an F1 score of 1. A model with a perfect precision score and a recall score of zero will achieve an F1 score of 0.
  - Models are sometimes evaluated using the F0.5 and F2 scores, which favor precision over recall and recall over precision, respectively.ion and recalls are...

  - - The **F1 measure** is the harmonic mean, or weighted average, of the precision and recall scores. Also called the f-measure or the f-score.
    - (P = precision, R = recall)
    $$ F1 = 2 \frac{PR}{P+R}$$


  - The F1 measure penalizes classifiers with imbalanced precision and recall scores, like the trivial classifier that always predicts the positive class.
  - A model with perfect precision and recall scores will achieve an F1 score of 1. A model with a perfect precision score and a recall score of zero will achieve an F1 score of 0.
  - Models are sometimes evaluated using the F0.5 and F2 scores, which favor precision over recall and recall over precision, respectively.
  - In our case, the arithmetic mean of our classifier's precision and recall scores is 0.77. As the difference between the classifier's precision and recall is small, the F1 measure's penalty is small.

- ROC Curve

  - Receiver Operating Characteristic, or ROC curve, visualizes a classifier's performance.
  - ROC curve illustrates the classifier's performance for all values of the discrimination threshold.
      - The true positive rate (Sensitivity, Recall ) is plotted in function of the false positive rate (100-Specificity, Fall-out) for different cut-off points.
      - Each point on the ROC curve represents a sensitivity/specificity pair corresponding to a particular decision threshold.
  - AUC is the area under the ROC curve; it reduces the ROC curve to a single value, which represents the expected performance of the classifier.
  - The dashed line in the following figure is for a classifier that predicts classes randomly; it has an AUC of 0.5. The solid curve is for a classifier that outperforms random guessing.
  - A test with perfect discrimination (no overlap in the two distributions) has a ROC curve that passes through the upper left corner (100% sensitivity, 100% specificity). Therefore the closer the ROC curve is to the upper left corner, the higher the overall accuracy of the test (Zweig & Campbell, 1993)

  - In our case, the ROC curve ......

- Summary

  After checking Log Loss, Accuracy, Precision and recall, F1 measure, and ROC Curve, the model....

## Deployment

- This model is used to predict the winning percentage of the every possible 2016 matchups in the 2018 NCAA Tournament Women's. Once the tournament begins and we have the bracket frame, with the threshold of 50% winning probability, the model can help users predict the total bracket including the winner of the tournament.

- To extend the model, if rolling the date set years forward, users can also use this model to predict the winning probability of NCAA Tournament Women's of year 2019, 2020, so on and so forth. However, as time goes by, the effectiveness of the model may change by rules, environment, players, fundings, etc. So, adjusting the model accordingly, is necessary for the forture years prediction and coreponding support will be provided by be provided by the team for users.
