# Power Outages 🔋 Project

This is the [Project 3](https://ylesia-wu.github.io/power-outage-analysis/) under course DSC80.

By Ylesia Wu & Junyue Lin

## Introduction

TODO: some introduction about outages!

The dataset is [sourced](https://engineering.purdue.edu/LASCI/research-data/outages) from an article entitled “A Multi-Hazard Approach to Assess Severe Weather-Induced Major Power Outage Risks in the U.S.” (Mukherjee et al., 2018). The data pertains to the major outages witnessed by different states in the continental U.S. from January 2000 to July 2016. Moreover, this data also presents on geographical location of the outages, date and time of the outages, regional climatic information, land-use characteristics, electricity consumption patterns, and economic characteristics of the states affected by the outages.

The original dataframe contains 1535 rows and 57 columns. For further exploratory data analysis, we created two new columns for start and restoration respectively, representing the start/restoration datetime of each outage by combining the existing date and time columns. 

In this project, first, we first cleaned the dataset and conducted exploratory data analysis to obtain some basic information about the data set and the relationship between columns. Then we explored the potential reasons for missingness for certain columns to see whether they are missing by random (MAR) or not missing by random (NMAR). In the MAR analysis, by conducting permutation tests, we can analyze the dependency of the missingness of a column on other columns. Thirdly, we would focus on the research question: are the geographical distributions of the number of outages caused by severe weather different in summer vs. in winter? 

TODO: the significance of the hypothesis testing! 

## Data Cleaning and EDA

Before conducting analysis related to the datasets, we would first conduct data cleaning to make the data more convenient for analysis.

> Remove Unnecessary Rows/Cols & Sort the Dataframe

First, we removed the first row because it is unnecessary, containing the unit of each column. Next, we removed the first column because it only contains the null values. And then, we sorted the dataframe by columns `YEAR` and `MONTH`.
 
> Convert the Data Types of Each Column as Appropriate 

We converted the data type of `YEAR` column to `int`. Next, we attempted to convert the data type of columns `DEMAND.LOSS.MW` and `OUTAGE.DURATION` to `float`, but because they contain null values, they are still presented as `object` type in the dataframe. 

> Create New `pd.Timestamp` columns by Combining Existing Columns

Combined `OUTAGE.START.DATE` and `OUTAGE.START.TIME` into a new `pd.Timestamp` column called `OUTAGE.START`. Did the same for 'OUTAGE.RESTORATION.DATE' and 'OUTAGE.RESTORATION.TIME'

> Cleaning Result

The cleaned dataframe's datatype for each column is listed below.

```
#   Column                 Non-Null Count  Dtype         
---  ------                 --------------  -----         
 0   YEAR                   1534 non-null   int64         
 1   MONTH                  1525 non-null   float64       
 2   U.S._STATE             1534 non-null   object        
 3   POSTAL.CODE            1534 non-null   object        
 4   NERC.REGION            1534 non-null   object        
 5   CLIMATE.REGION         1528 non-null   object        
 6   ANOMALY.LEVEL          1525 non-null   object        
 7   CLIMATE.CATEGORY       1525 non-null   object        
 8   CAUSE.CATEGORY         1534 non-null   object        
 9   CAUSE.CATEGORY.DETAIL  1063 non-null   object        
 10  HURRICANE.NAMES        72 non-null     object        
 11  OUTAGE.DURATION        1476 non-null   object        
 12  DEMAND.LOSS.MW         829 non-null    object        
 13  CUSTOMERS.AFFECTED     1091 non-null   float64       
 14  RES.PRICE              1512 non-null   object        
 15  COM.PRICE              1512 non-null   object        
 16  IND.PRICE              1512 non-null   object        
 17  TOTAL.PRICE            1512 non-null   object        
 18  RES.SALES              1512 non-null   object        
 19  COM.SALES              1512 non-null   object        
 20  IND.SALES              1512 non-null   object        
 21  TOTAL.SALES            1512 non-null   object        
 22  RES.PERCEN             1512 non-null   object        
 23  COM.PERCEN             1512 non-null   object        
 24  IND.PERCEN             1512 non-null   object        
 25  RES.CUSTOMERS          1534 non-null   float64       
 26  COM.CUSTOMERS          1534 non-null   float64       
 27  IND.CUSTOMERS          1534 non-null   float64       
 28  TOTAL.CUSTOMERS        1534 non-null   float64       
 29  RES.CUST.PCT           1534 non-null   object        
 30  COM.CUST.PCT           1534 non-null   object        
 31  IND.CUST.PCT           1534 non-null   object        
 32  PC.REALGSP.STATE       1534 non-null   object        
 33  PC.REALGSP.USA         1534 non-null   object        
 34  PC.REALGSP.REL         1534 non-null   object        
 35  PC.REALGSP.CHANGE      1534 non-null   object        
 36  UTIL.REALGSP           1534 non-null   object        
 37  TOTAL.REALGSP          1534 non-null   object        
 38  UTIL.CONTRI            1534 non-null   object        
 39  PI.UTIL.OFUSA          1534 non-null   object        
 40  POPULATION             1534 non-null   float64       
 41  POPPCT_URBAN           1534 non-null   object        
 42  POPPCT_UC              1534 non-null   object        
 43  POPDEN_URBAN           1534 non-null   object        
 44  POPDEN_UC              1524 non-null   object        
 45  POPDEN_RURAL           1524 non-null   object        
 46  AREAPCT_URBAN          1534 non-null   object        
 47  AREAPCT_UC             1534 non-null   object        
 48  PCT_LAND               1534 non-null   object        
 49  PCT_WATER_TOT          1534 non-null   object        
 50  PCT_WATER_INLAND       1534 non-null   object        
 51  OUTAGE.START           1525 non-null   datetime64[ns]
 52  OUTAGE.RESTORATION     1476 non-null   datetime64[ns]
```

The cleaned dataframe is shown below (with only important columns selected for display).

| YEAR | MONTH | U.S._STATE       | CLIMATE.REGION | CAUSE.CATEGORY         | CAUSE.CATEGORY.DETAIL         | OUTAGE.START           | OUTAGE.RESTORATION      | OUTAGE.DURATION | DEMAND.LOSS.MW |
|------|-------|------------------|----------------|------------------------|-------------------------------|------------------------|-------------------------|------------------|-----------------|
| 2000 | 1.0   | South Carolina   | Southeast      | severe weather         | winter storm                  | 2000-01-23 08:00:00   | 2000-01-28 12:00:00    | 7440.0           | 450.0           |
| 2000 | 1.0   | South Carolina   | Southeast      | severe weather         | winter storm                  | 2000-01-29 22:00:00   | 2000-02-03 12:00:00    | 6600.0           | 300.0           |
| 2000 | 3.0   | Texas            | South          | system operability disruption | transmission interruption | 2000-03-18 16:00:00   | 2000-03-18 17:10:00    | 70.0            | 400.0           |
| 2000 | 3.0   | New Mexico       | Southwest      | system operability disruption | transmission interruption | 2000-03-18 19:08:00   | 2000-03-18 19:08:00    | 0.0             | 1040.0          |
| 2000 | 3.0   | Maine            | Northeast      | intentional attack      | vandalism                    | 2000-03-14 21:06:00   | NaT                     | NaN             | NaN             |



### Exploratory Data Analysis

### Univariate Analysis

In the univariate analysis, we would analyze the distribution of the outages by `MONTH` and the distribution of outages by `CAUSE.CATEGORY`. 

<iframe src="assets/univariate_month.html" width=800 height=600 frameBorder=0></iframe>

The plot above shows the number of outages that happened in each specific month. It appears that June is the month when outages happened the most for this dataset with a count of 195. And November is the month when outages happened the least for this dataset with a count of 72. 

<iframe src="assets/univariate_cause_category.html" width=800 height=600 frameBorder=0></iframe>

The plot above shows the distribution of `CAUSE.CATEGORY`. It appears that outages caused by severe weather accounted for the highest proportion with a count of 765 and the outages caused by Islanding accounted for the lowest proportion with a count of 46 in this dataset. 

### Bivariate Analysis

Then, we do a bivariate analysis between TODO!


<iframe src="assets/TODO" width=800 height=600 frameBorder=0></iframe>


### Interesting Aggregates

In the aggregates analysis, we will study the relationship between `CAUSE.CATEGORY` and `YEAR`. Specifically, we want to see the number of outages for each `CAUSE.CATEGORY` by 
`YEAR`.

| YEAR | equipment failure | fuel supply emergency | intentional attack | islanding | public appeal | severe weather | system operability disruption |
|------|-------------------|-----------------------|---------------------|-----------|----------------|-----------------|------------------------------|
| 2000 | 5.0               | NaN                   | 2.0                 | NaN       | NaN            | 13.0            | 6.0                          |
| 2001 | 1.0               | NaN                   | NaN                 | NaN       | 3.0            | 1.0             | 10.0                         |
| 2002 | NaN               | NaN                   | 1.0                 | NaN       | NaN            | 13.0            | 3.0                          |
| 2003 | 6.0               | NaN                   | 2.0                 | NaN       | 1.0            | 30.0            | 7.0                          |
| 2004 | 5.0               | NaN                   | NaN                 | NaN       | 7.0            | 56.0            | 3.0                          |
| 2005 | 3.0               | 1.0                   | NaN                 | NaN       | NaN            | 47.0            | 4.0                          |
| 2006 | 1.0               | 1.0                   | NaN                 | NaN       | 2.0            | 54.0            | 9.0                          |
| 2007 | 6.0               | 3.0                   | NaN                 | 1.0       | 2.0            | 40.0            | 4.0                          |
| 2008 | 9.0               | 4.0                   | NaN                 | 6.0       | 2.0            | 76.0            | 14.0                         |
| 2009 | 10.0              | 3.0                   | NaN                 | 4.0       | 10.0           | 45.0            | 6.0                          |
| 2010 | 5.0               | 3.0                   | NaN                 | 9.0       | 14.0           | 62.0            | 13.0                         |
| 2011 | 4.0               | 6.0                   | 121.0               | 3.0       | 16.0           | 107.0           | 12.0                         |
| 2012 | 1.0               | 5.0                   | 89.0                | 4.0       | 3.0            | 65.0            | 7.0                          |
| 2013 | 4.0               | 7.0                   | 80.0                | 7.0       | NaN            | 49.0            | 6.0                          |
| 2014 | NaN               | 13.0                  | 47.0                | 1.0       | 5.0            | 45.0            | 1.0                          |
| 2015 | NaN               | 2.0                   | 43.0                | 9.0       | 4.0            | 48.0            | 13.0                         |
| 2016 | NaN               | 3.0                   | 33.0                | 2.0       | NaN            | 12.0            | 9.0                          |


<iframe src="assets/bivariate_category_year.html" width=800 height=600 frameBorder=0></iframe>

One interesting result that we find in the aggregates data is that there is a peek in the year 2011 for `intentional attack`, `public appeal` and `severe weather`, with a count of 121, 16, 107, respectively. And there was no record of outages caused by intentional attack in year 2001, 2004-2010. But starting from the year 2011, outages caused by intentional attack appeared again with a peak of 121. It might be interesting to dig into the reason behind this phenomenon. 

## Assessment of Missingness

In this part, we will be conducting an assessment of missingness. 

### NMAR Analysis

The `HURRICANE.NAMES` column is NMAR because it is only missing data in a row when the hurricane name does not exist for this outage event. In other words, during the data-generating process, we only include a hurricane name if it applies in the context of the outage event, and we leave the hurricane name missing if it doesn't apply.

### Missingness Dependency

Now, we focus on the missingness of `DEMAND.LOSS.MW` in the dataset and test the dependency of the missingness on two other columns: `CLIMATE.REGION` and `RES.PERCEN`. 

> the missingness of `DEMAND.LOSS.MW` and `CLIMATE.REGION`

Null hypothesis: the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing.
Alternative hypothesis: the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is different from the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing.
Test Statistics: Since `CLIMATE.REGION` is a categorical variable. We used Total variation distance (TVD) as the test statistics.
Observed Statistics: 0.23159749848146532

We drew distribution plots about these two distributions.

<iframe src="assets/region_demand_missing.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/region_demand_not_missing.html" width=800 height=600 frameBorder=0></iframe>

We use permutation tests to shuffle the missingness of `DEMAND.LOSS.MW` 10000 times and get 10000 simulating results of TVD. The red line marked the observed stat. 

<iframe src="assets/mar_depend.html" width=800 height=600 frameBorder=0></iframe>

Finally, we calculate the p-value 0. when we use 0.05 as a significance threshold, since 0.0 <= 0.05, we reject the null hypothesis that the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing. In other words, the missingness of `DEMAND.LOSS.MW` is MAR because it depends on the column `CLIMATE.REGION`. TODO!

> the missingness of `DEMAND.LOSS.MW` and `RES.PERCEN`

Null hypothesis: the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing.
Alternative hypothesis: the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is different from the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing.
Test Statistics: Since `RES.PERCEN` is a numeric variable. We used Kolmogorov-Smirnov statistic as the test statistics.
Observed Statistics: 0.05247542540358801

We also drew distribution plots for these two distributions. 

<iframe src="assets/res_percen_demand_missing.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/res_percen_demand_not_missing.html" width=800 height=600 frameBorder=0></iframe>

Under the hood, ks_2samp used permutation tests to shuffle the missingness of `DEMAND.LOSS.MW` and got the p-value of 0.23392391909317375. 

<iframe src="assets/TODO" width=800 height=600 frameBorder=0></iframe>

We use 0.05 as a significance threshold. Since the p-value 0.23 > 0.05, we fail to reject the null hypothesis that the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing. In other words, the missingness of `DEMAND.LOSS.MW` is not dependent on `RES.PERCEN`.

## Hypothesis Testing

The question we are going to research on is that: are the geographical distributions of the number of outages caused by severe weather different in summer vs. in winter? 

First, we created a new column called `season`, dividing the `MONTH` values into four groups: spring for month 3,4,5; summer for month 6,7,8; fall for month 9,10,11; winter for month 12,1,2. 

We will conduct a permutation test.

### Setting Up the Testing

Null Hypothesis H0: The distribution of climate regions for outages caused by severe weather is the same in summer and in winter.

Alternative Hypothesis H1: The distribution of climate regions for outages caused by severe weather is the same in summer and in winter.

We would select only the useful column, including n_steps and average rating, and also create new column named `complex`, which is true if it has steps more than 10 and false if has steps less or equal to 10.

|     id |   n_steps |   ave_rating | complex   |
|-------:|----------:|-------------:|:----------|
| 333281 |        10 |            4 | False     |
| 453467 |        12 |            5 | True      |
| 306168 |         6 |            5 | False     |
| 286009 |         7 |            5 | False     |
| 475785 |        17 |            5 | True      |

The reason for choosing one-sided test is that we might assume people could feel frustrated when cooking complex recipes, and also recipes with more steps are harder to cook

| complex   |   n_steps |   ave_rating |
|:----------|----------:|-------------:|
| False     |   6.35718 |      4.50184 |
| True      |  16.1414  |      4.48441 |

Since ave_rating is numerical data, so it is proper to use the difference in mean as test statistics. In the part of research, the significant level we choose is `0.05`

The observed difference in mean is `0.017428379224658563`

### Permutation Test

<iframe src="assets/TODO" width=800 height=600 frameBorder=0></iframe>

We ran permutation tests for 10000 times and the graph shows the distribution of permutation test results. The red line marks the observed value.

### Hypothesis Testing Conclusion

The P-value for the testing is 0.0312, which means that at significant level of 0.05, we are able to reject the null hypothesis.

This result could be reasonable since first, high level complexity of a recipes could mean difficulties in cooking the dish and increasing probability in failing. If people fail to cook the dish, they might give low rating to the recipe. Also, people might have higher expectation on the dish if it is complex and hard to make. Then, people might have a more strict rating scale for complex recipes. 

