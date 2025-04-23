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

|   YEAR |   MONTH | STATE   | NERC.REGION   | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   POPULATION |   Outage Duration |   OUTAGE.START.HOUR |
|-------:|--------:|:--------|:--------------|:-------------------|:-------------------|-------------:|------------------:|--------------------:|
|   2011 |       7 | MN      | MRO           | normal             | severe weather     |      5348119 |        51         |                  17 |
|   2014 |       5 | MN      | MRO           | normal             | intentional attack |      5457125 |         0.0166667 |                  18 |
|   2010 |      10 | MN      | MRO           | cold               | severe weather     |      5310903 |        50         |                  20 |
|   2012 |       6 | MN      | MRO           | normal             | severe weather     |      5380443 |        42.5       |                   4 |
|   2015 |       7 | MN      | MRO           | warm               | severe weather     |      5489594 |        29         |                   2 |

