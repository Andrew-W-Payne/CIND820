# Investigating the Impact of Travel and Rest on an NBA Win/Loss Predictive Model

## Repository Outline

Data Preprocessing steps were completed in [dataset prep.Rmd](https://github.com/Andrew-W-Payne/CIND820/blob/main/dataset%20prep.Rmd) using:
- [df.csv](https://github.com/Andrew-W-Payne/CIND820/blob/main/df.csv) (the original dataset with minor preparations for ease of use)
- [distance.csv](https://github.com/Andrew-W-Payne/CIND820/blob/main/distance.csv) (a matrix of distances between NBA cities in km, prepared as described in dataset prep.Rmd)
- [timezones.csv](https://github.com/Andrew-W-Payne/CIND820/blob/main/timezones.csv) (a matrix of timezone differences between NBA cities, prepared manually in Excel). 

The dataset prep.Rmd file processes the dataset above in order to be useable for future modeling stages. It undergoes the following steps:
- filters duplicate games from the dataset, as the original dataset has six rows per game (one official per row, or three officials, per team). This step is already completed in the df.csv file.
- initializes the factors of timezone shift and distance between matches using distance.csv, timezones.csv, and scripting to append the relevant data to each match in the dataset
- processes the game statistics for each match so that they are rolling averages of team's last 10 games rather than single game statistics so that they can be used to predict wins/losses in a classification model

This outputs the file [df processed.csv](https://github.com/Andrew-W-Payne/CIND820/blob/main/df%20processed.csv), which is then used for modeling in [dataset modeling.Rmd](https://github.com/Andrew-W-Payne/CIND820/blob/main/dataset%20modeling.Rmd) after some unnecessary attributes are removed once more.

The dataset modeling.Rmd file then creates a classification model based on the matches provided in df processed.csv after some additional data manipulation:

- remove rows with NA values: these correspond to matches too early into a season to establish rolling averages of game statistics
- remove outliers: using the "mean +- 3\*SD formula" for outliers, matches with >4 days of rest are considered extremes and are removed. Additionally, games with more or less than 240 minutes played are identified and removed, as they are either computing errors or overtime matches. This includes games with non-zero values in the team/opptPTS5-8 columns (overtime periods 1-4 team scores)
- a small correction is made to fix %-based attributes to be based on rolling averages of attempted field goals and made field goals
- all numeric data is then normalized
- redundant features are identified using correlation and removed
- k-fold cross validation (k = 10) is undertaken, and a Naive Bayes classification model is trained using the remaining factors in the cross validated dataset.

The resulting model has an approximate accuracy of ~65.85%. Further stages of this project aim to improve this value further by using different classifiers (Naive Bayes, Decision Tree, and Logistic Regression) and comparing performance across different selections of features.
