# Can You Predict the Duration of an Outage Right When it Occurs?

### By: Biplav Kharel
### Contact:khbiplav@umich.edu


## Introdcution

The Power Outages dataset contains records of major electricity outages across the continental U.S. In addition to the outage details, it includes information about the location, local climate, land use, electricity consumption, and economic characteristics of the affected regions. This added context helps paint a fuller picture of the conditions surrounding each outage. With this data, it's possible to explore what factors might influence how long outages last.

Source: [Link to Dataset](https://engineering.purdue.edu/LASCI/research-data/outages)

This dataset will allow us to answer the question above. 

# Data Cleaning

### Missing Value Handling
The first step was to check if the dataset contained any missing values. This led to the identification of 25 columns that contained missing values. Out of these columns, the ones that needed to be handled since they would be used later in the analysis were:

- `Month: (9)`  
- `CLIMATE.REGION (6)`
- `CLIMATE.CATEGORY (9)`
- `OUTAGE.DURATION (136)`

This led to considering whether there were possibilities for imputing or dropping the missing values based on context. But since the amount of missing values was small and the dataset had a large number of observations, ultimately dropping was the decision that was made. Additionally, with Outage Duration, there was an attempt to compute the values using the OUTAGE.START.TIME column and the OUTAGE.RESTORATION.TIME column, but more analysis showed that the missing values for Duration were the same for those two columns as well.


### Data Type Converting

### Data Type Fixes

After the missing values were handled, there were a couple of columns of interest that also had data types that needed to be changed.

- `OUTAGE.START.TIME` was previously a timestamp but was turned into an `int` to keep track of just the start hour.
- State names were mapped to abbreviations for a later graph, where the abbreviations were needed.
- Months were initally doubles, and were mapped to actual month name.

### Here is look at the dataset with the relavant columns

|   YEAR | Month   | STATE   | NERC.REGION   | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   POPULATION |   Outage Duration |   OUTAGE.START.HOUR |
|-------:|:--------|:--------|:--------------|:-------------------|:-------------------|-------------:|------------------:|--------------------:|
|   2011 | July    | MN      | MRO           | normal             | severe weather     |      5348119 |        51         |                  17 |
|   2014 | May     | MN      | MRO           | normal             | intentional attack |      5457125 |         0.0166667 |                  18 |
|   2010 | October | MN      | MRO           | cold               | severe weather     |      5310903 |        50         |                  20 |
|   2012 | June    | MN      | MRO           | normal             | severe weather     |      5380443 |        42.5       |                   4 |
|   2015 | July    | MN      | MRO           | warm               | severe weather     |      5489594 |        29         |                   2 |


# Exploratory Data Analysis
With a cleaner data set, now we can explore it.

| Month     |   Outage Duration |
|:----------|------------------:|
| January   |           63.1088 |
| February  |           43.9497 |
| March     |           58.8111 |
| April     |           25.6159 |
| May       |           36.4599 |
| June      |           34.3185 |
| July      |           38.7338 |
| August    |           42.7389 |
| September |           76.569  |
| October   |           62.3239 |
| November  |           30.1119 |
| December  |           55.9322 |

The table shows the mean outage duration in each month. There are some clear patterns between the month and how long an outage lasts, with the maximum in September, which lines up with hurricane season. It also follows that the mean durations during that season are similar as well. This might be useful when discussing if months or season will be better for predicting the outage duration.



 <iframe
 src="assets/outagedist.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

The histogram shows a very right-skewed distribution, which is expected. Additionally, there are several large outliers that create a very long tail on the histogram. For example, the maximum outage duration was 1810 hours. This is much larger than the highest bin which had values from 0-24 hours.


 <iframe
 src="assets/causeDist.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

Overall, the result is expected as severe weather caused the most outages. But "intentional attack" being the second most common is very surprising. Intentional attack refers to a situation where officials determine that targeted human actions caused the outage. Additionally, the disribution of the bottom 5 categories are simliar so it will help during future model prediction.

 <iframe
 src="assets/heatmap.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

You can see that the Midwest gets hit the worst when it comes to outage length. Places like Wisconsin and West Virginia have way longer average durations than most other states. That kind of pattern makes me think state info could actually help when predicting outage time. If some states always take longer to fix things, it’s probably worth including that in the model.

 <iframe
 src="assets/durCause.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

 Even though severe weather was the largest category causing outages, it isn't first in duration. Fuel supply emergencies have a significantly longer average outage duration. This again could help in the model. Also, the categories that had mean outage durations lasting more than a day were public appeal, equipment failure, severe weather, and fuel supply. The rest were usually resolved sooner.


# Framing a Predicion Model

 ## The Question: Based on the factors that are known at the start of a outage, how long will the power outage last?

 What type of problem is this? 

 This is going a regression problem, as we predciting a continous variable.

How will this be tested?

First we will create a training set, and and independent testing set. This will allow use to truly measure the accuary of our model. Using both mean squared error and mean absolute error.



# A Basic Model

Using the dataframe from earlier, a simple model was created.

### Features Used in the Baseline Model

The following features were used:

- `YEAR`: The year the outage occurred.
- `STATE`: The U.S. state where the outage occurred. (OHE)
- `NERC.REGION`: The regional reliability entity associated with the outage. (OHE)
- `CLIMATE.CATEGORY`: General climate classification of the region. (OHE)
- `CAUSE.CATEGORY`: The main category of the outage cause. (OHE)
- `Month`: The month in which the outage began. (OHE)
- `POPULATION`: Population of the affected area.
- `OUTAGE.START.HOUR`: The hour of the day when the outage started, extracted from the start time.

(OHE) represents features that we One Hot Encoded

## The Results:

| Metric               |    Value |
|:---------------------|---------:|
| RMSE                 | 164.355  |
| MAE                  |  74.5021 |
| Mean Outage Duration |  46.198  |
| TMSE                 | 108.876  |


The model performed very badly, it was wrose than predicting the mean each time as the TMSE was better than the RMSE. This needs to be improved.


# Final Model


### Rethinking the Model:

The first thing that comes to mind is multicollinearity, as several features in the model are highly correlated—such as `CAUSE.CATEGORY`, `CLIMATE.CATEGORY`, and `NERC.REGION`. This can cause unstable predictions that are overly generalized to the test set. 

This means we either need to switch our features or use a different model. Ultimately, a different regression model was chosen. Instead of Linear Regression, a **Lasso Regression** was used. This decision was based on two reasons:

1. Its ability to "turn off" irrelevant features by assigning them zero weights.
2. Its ability to protect against multicollinearity by adding regularization to the coefficient matrix.

### Feature Engineering

In addition to the model being changed, two new features were created:

- **Seasons**: As we saw during the EDA, the mean duration of outages tended to be more similar season to season rather than month to month. So, a feature called `Seasons` was created by mapping the month number to a specific season in the year.

- **Time of Day**: Instead of relying on raw numeric predictions for the exact time of day an outage started, grouping the start hour into categories like `Morning`, `Afternoon`, and `Night` made more sense. The idea was that response time is likely more related to the general time of day than to the specific hour.

### K Folds CV:

Since the model was changed to Lasso, we needed to pick the optimal lambda so the model could perform its best. This led to using the GridSearchCV function, which allowed for k-fold cross-validation to find the best regularization strength.

## The Results:

Best alpha: 0.1

| Metric               |    Value |
|:---------------------|---------:|
| RMSE                 |  99.485  |
| MAE                  |  43.7279 |
| Mean Outage Duration |  46.198  |
| TMSE                 | 108.876  |


The final Lasso regression model performed noticeably better than simply predicting the mean outage duration. The model achieved a Root Mean Squared Error (RMSE) of approximately 164.36 and a Mean Absolute Error (MAE) of about 74.50. This compares favorably to the Total Mean Squared Error (TMSE) baseline of 108.88, which represents the error if we had predicted the average outage duration for every instance. This shows that the model is capturing some underlying structure in the data beyond the mean.
