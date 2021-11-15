# Report: Predict Bike Sharing Demand with AutoGluon Solution
#### Ashutosh A. Patwardhan

## Initial Training
### What did you realize when you tried to submit your predictions? What changes were needed to the output of the predictor to submit your results?
Kaggle did not accept negative predictions. Negative predictions, if any, had to be replaced with zeros in order for Kaggle to accept the submission.

### What was the top ranked model that performed?
The top ranked model was a `WeightedEnsemble_L3` model. 

## Exploratory data analysis and feature creation
### What did the exploratory analysis find and how did you add additional features?
Exploratory data analysis showed that:

  1. Variables in the `train` and `test` data sets were similarly distributed. This was not going to cause problems with modeling, with the exception described below;
  2. The exception was that the `test` data occurred at the end of the month whereas `train` data were from the beginning of the month. The training would not capture changes in customer spending habits, if any, that occurred at the end of the month;
  3. `season` and `weather` were initially read as integers from the `.csv` file. It made more sense for them to be used as categorical variables;
  4. It also made sense for `hour`, `day` and `month` to be included as feature variables, as `count` showed definite cyclicality according to `day` and `month`.

### How much better did your model preform after adding additional features and why do you think that is?
The model validation score (root mead ssquared error) improved from -114.74 to -36.42, while the Kaggle score of the test data predictions improved from 1.39412 to 0.50192. This was primarily because:
  1. `weather` and `season` were changed to categorical variables. For example, `season`=1 and `season`=2 just means that the two seasons are different. It does not imply that one season is "better than" or "greater than" the other. Same for weather;
  2. Including `hour`, `day` and `month` captured more of the cyclicality.

## Hyper parameter tuning
### How much better did your model preform after trying different hyper parameters?
Hyperparameter tuning did not have any significant impact on model performance. This bears out the [advice](https://auto.gluon.ai/dev/tutorials/tabular_prediction/tabular-kaggle.html?highlight=hyperparameter_tune_kwargs) to focus on feature-engineering and just specifying `presets='best_quality'` rather than tuning the hyperparameters.

### If you were given more time with this dataset, where do you think you would spend more time?
It would be best to spend more time on trying out more features:

  1. There is a upward trend of rentals over time. This cannot be ascribed to changing weather, but is probably owing to growing popularity of bike rentals. Including time as a feature may improve the model; ![img/count-vs-datetime.png](img/count-vs-datetime.png)
  2. The previous day's `count` is probably an excellent predictor f the next day's `count`. It could be worthwhile making the previous day's `count` into a feature;
  3. Since a time series is involved, an RNN-based model may give better performance;
  4. It would also be interesting to using the root-mean-squared-logarithmic-error used by Kaggle.

### Create a table with the models you ran, the hyperparameters modified, and the kaggle score.
|model|num_trials|searcher|time_limit|score|
|--|--|--|--|--|
|initial|default|auto|600|1.39412|
|add_features|default|auto|600|0.50192|
|hpo_2|20|random|900|0.50145|

### Create a line plot showing the top model score for the three (or more) training runs during the project.

![model_train_score.png](img/model_train_score.png)

### Create a line plot showing the top kaggle score for the three (or more) prediction submissions during the project.

![model_test_score.png](img/model_test_score.png)

## Summary
  1. AWS SageMaker and the Autogluon package provide a powerful way to develop machine learning models;
  2. The burden of trying out and tuning a number of different model types is removed from the user;
  3. The user is allowed to focus on feature engineering, thus bringing the user's domain knowledge to be used to maximum effect;
  4. It is worthwhile trying different evaluation metrics. In this example, `mean_absolute_error` and `median_absolute_error` gave better Kaggle scores than `root_mean_squared_error`. This was likely because the distribution of `count` was overwhelmingly towards smaller values. Using `root_mean_squared_error` gave slightly higher levarage to the samples with high values of `count`.