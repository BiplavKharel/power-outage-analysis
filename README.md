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

