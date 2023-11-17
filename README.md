# Power Outages 🔋 Project

This is the [Project 3](https://ylesia-wu.github.io/power-outage-analysis/) under University of California San Diego course DSC80.

By Ylesia Wu & Junyue Lin

## Introduction

A power outage is defined as the loss of the electrical power network supply to an end user. This occurrence engenders a disruption in the provision of electricity, leading to an absence of power in residences, commercial establishments, and other facilities. The ramifications of power outages encompass a spectrum of effects, including the loss of lighting, disruption of appliances and electronic devices, deprivation of heating or cooling services, compromised communication, interruption of essential services, and the spoilage of perishable goods. Power outages might potentially impact individuals' lives and lead to financial loss. Consequently, it is necessary to examine the patterns associated with the cause of power outages to formulate effective responses to such events.

The dataset is [sourced](https://engineering.purdue.edu/LASCI/research-data/outages) from an article entitled “A Multi-Hazard Approach to Assess Severe Weather-Induced Major Power Outage Risks in the U.S.” (Mukherjee et al., 2018). The data pertains to the major outages witnessed by different states in the continental U.S. from January 2000 to July 2016. Moreover, this data also presents on geographical location of the outages, date and time of the outages, regional climatic information, land-use characteristics, electricity consumption patterns, and economic characteristics of the states affected by the outages.

The original dataframe contains 1535 rows and 57 columns. For further exploratory data analysis, we created two new columns for start and restoration respectively, representing the start/restoration datetime of each outage by combining the existing date and time columns. 

In this project, we first cleaned the dataset and conducted exploratory data analysis to obtain some basic information about the data set and the relationship between columns. Then we explored the potential reasons for missingness for certain columns to see whether they are missing by random (MAR) or not missing by random (NMAR). In the MAR analysis, by conducting permutation tests, we can analyze the dependency of the missingness of a column on other columns. Lastly, we focused on the research question: are the geographical distributions of the number of outages caused by severe weather different in summer vs. in winter? 

Our research question evolved gradually during the exploratory data analysis phase. Our initial observations highlighted severe weather as the predominant factor contributing to power outages nationwide. Subsequently, we delved into the intricacies of severe weather events, scrutinizing their nuances across diverse climate regions. Notably, distinct patterns of outage occurrences surfaced, exhibiting seasonal variations unique to each region. Intrigued by these findings, we sought to formalize our observations by subjecting them to a hypothesis test.

Beyond our intellectual curiosity, the practical implications of our research question are of paramount importance in the real world. Unplanned power outages can result in severe, if not catastrophic, consequences in the absence of emergency response plans. Considering the specificity of responses required for different severe weather events, understanding the patterns of outages arising from such events becomes crucial. By discerning these patterns, local authorities and organizations can devise more effective strategies to assist their communities during times of need.

## Data Cleaning and EDA

Before conducting analysis related to the datasets, we first conducted data cleaning to make the data more convenient for analysis.

> Remove Unnecessary Rows/Cols & Sort the Dataframe

First, we removed and stored separately the first row because it only contained the unit of each column. Next, we removed the first column because it only contained null values. And then, we sorted the dataframe by columns `YEAR` and `MONTH`.
 
> Convert the Data Types of Each Column as Appropriate 

We converted the data type of `YEAR` column to `int`. Next, we converted all other columns that contained numerical data to `float`. 

> Create New `pd.Timestamp` columns by Combining Existing Columns

We combined `OUTAGE.START.DATE` and `OUTAGE.START.TIME` into a new `pd.Timestamp` column called `OUTAGE.START`. We also did the same thing for `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME`, creating a new column called `OUTAGE.RESTORATION`.

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
 6   ANOMALY.LEVEL          1525 non-null   float64       
 7   CLIMATE.CATEGORY       1525 non-null   object        
 8   CAUSE.CATEGORY         1534 non-null   object        
 9   CAUSE.CATEGORY.DETAIL  1063 non-null   object        
 10  HURRICANE.NAMES        72 non-null     object        
 11  OUTAGE.DURATION        1476 non-null   float64       
 12  DEMAND.LOSS.MW         829 non-null    float64       
 13  CUSTOMERS.AFFECTED     1091 non-null   float64       
 14  RES.PRICE              1512 non-null   float64       
 15  COM.PRICE              1512 non-null   float64       
 16  IND.PRICE              1512 non-null   float64       
 17  TOTAL.PRICE            1512 non-null   float64       
 18  RES.SALES              1512 non-null   float64       
 19  COM.SALES              1512 non-null   float64       
 20  IND.SALES              1512 non-null   float64       
 21  TOTAL.SALES            1512 non-null   float64       
 22  RES.PERCEN             1512 non-null   float64       
 23  COM.PERCEN             1512 non-null   float64       
 24  IND.PERCEN             1512 non-null   float64       
 25  RES.CUSTOMERS          1534 non-null   float64       
 26  COM.CUSTOMERS          1534 non-null   float64       
 27  IND.CUSTOMERS          1534 non-null   float64       
 28  TOTAL.CUSTOMERS        1534 non-null   float64       
 29  RES.CUST.PCT           1534 non-null   float64       
 30  COM.CUST.PCT           1534 non-null   float64       
 31  IND.CUST.PCT           1534 non-null   float64       
 32  PC.REALGSP.STATE       1534 non-null   float64       
 33  PC.REALGSP.USA         1534 non-null   float64       
 34  PC.REALGSP.REL         1534 non-null   float64       
 35  PC.REALGSP.CHANGE      1534 non-null   float64       
 36  UTIL.REALGSP           1534 non-null   float64       
 37  TOTAL.REALGSP          1534 non-null   float64       
 38  UTIL.CONTRI            1534 non-null   float64       
 39  PI.UTIL.OFUSA          1534 non-null   float64       
 40  POPULATION             1534 non-null   float64       
 41  POPPCT_URBAN           1534 non-null   float64       
 42  POPPCT_UC              1534 non-null   float64       
 43  POPDEN_URBAN           1534 non-null   float64       
 44  POPDEN_UC              1524 non-null   float64       
 45  POPDEN_RURAL           1524 non-null   float64       
 46  AREAPCT_URBAN          1534 non-null   float64       
 47  AREAPCT_UC             1534 non-null   float64       
 48  PCT_LAND               1534 non-null   float64       
 49  PCT_WATER_TOT          1534 non-null   float64       
 50  PCT_WATER_INLAND       1534 non-null   float64       
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

In the univariate analysis, we analyzed the distribution of the outages by `MONTH` and the distribution of outages by `CAUSE.CATEGORY`. 

<iframe src="assets/univariate_month.html" width=800 height=600 frameBorder=0></iframe>

The plot above shows the number of outages that happened in each specific month. It appears that June is the month when outages happened the most for this dataset with a count of 195. November is the month when outages happened the least in this dataset with a count of 72. 

<iframe src="assets/univariate_cause_category.html" width=800 height=600 frameBorder=0></iframe>

The plot above shows the distribution of `CAUSE.CATEGORY`. It appears that outages caused by severe weather accounted for the highest proportion with a count of 765, and outages caused by Islanding accounted for the lowest proportion with a count of 46 in this dataset. 

### Bivariate Analysis

We did a bivariate analysis between `CAUSE.CATEGORY` and `OUTAGE.DURATION` and another bivariate analysis between `CAUSE.CATEGORY` and `U.S._STATE`.

<iframe src="assets/bivariate_cause_duration_box.html" width=800 height=600 frameBorder=0></iframe>

The plot above shows the distribution of `OUTAGE.DURATION` per `CAUSE.CATEGORY`. It appears that outages caused by fuel supply emergency tend to last longer, followed by outages caused by severe weather, both having relatively high 75th percentile and median.

<iframe src="assets/bivariate_state_category.html" width=800 height=600 frameBorder=0></iframe>

The plost above shows the distribution of outages per `U.S._STATE` and per `CAUSE.CATEGORY`. California appears to have a very high total number of outages, over 200. Severe weather seems to be the top one cause of power outage for a majority of the states because we can observe that the the bars of many states have a high blue proportion.

### Interesting Aggregates

As an aggregates analysis, we studied the relationship between `CAUSE.CATEGORY` and `YEAR`. Specifically, we wanted to see the number of outages for each `CAUSE.CATEGORY` and each 
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

One interesting result that we found in the aggregates data was that there was a peak in the year 2011 for `intentional attack`, `public appeal` and `severe weather`, with a count of 121, 16, 107, respectively. And there was no record of outages caused by intentional attack in years 2001 and 2004-2010. Starting from the year 2011, outages caused by intentional attack appeared again with a peak of 121. It might be interesting to dig into the reason behind this phenomenon. 

## Assessment of Missingness

In this part of our project, we will conduct an assessment of missingness. 

### NMAR Analysis

The `HURRICANE.NAMES` column is NMAR because it is only missing data in a row when the hurricane name does not exist for this outage event. In other words, during the data-generating process, they only included a hurricane name if it applied in the context of the outage event, leaving the hurricane name as missing if it didn't apply.

### Missingness Dependency

Now, we focus on the missingness of `DEMAND.LOSS.MW` in the dataset and test the dependency of the missingness on two other columns: `CLIMATE.REGION` and `RES.PERCEN`. 

> the missingness of `DEMAND.LOSS.MW` and `CLIMATE.REGION`

- Null hypothesis: the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing.
- Alternative hypothesis: the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is different from the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing.
- Test Statistics: Since `CLIMATE.REGION` is a categorical variable, we will use Total variation distance (TVD) as the test statistics.
- Observed Statistics: 0.23159749848146532

We draw distribution plots of these two distributions.

<iframe src="assets/region_demand_missing.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/region_demand_not_missing.html" width=800 height=600 frameBorder=0></iframe>

We use permutation tests to shuffle the missingness of `DEMAND.LOSS.MW` 10000 times and get 10000 simulated results of TVD. The red line marked the observed stat. 

<iframe src="assets/mar_depend.html" width=800 height=600 frameBorder=0></iframe>

Finally, we get a p-value of 0. When using 0.05 as the significance level, since 0.0 < 0.05, we reject the null hypothesis that the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `CLIMATE.REGION` when `DEMAND.LOSS.MW` is not missing. In other words, the missingness of `DEMAND.LOSS.MW` is MAR because it depends on the column `CLIMATE.REGION`. It is likely that certain states paid more attention to recording the demand loss whereas others found it less important. 

> the missingness of `DEMAND.LOSS.MW` and `RES.PERCEN`

- Null hypothesis: the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing.
- Alternative hypothesis: the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is different from the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing.
- Test Statistics: Since `RES.PERCEN` is a numeric variable, we will use Kolmogorov-Smirnov statistic as the test statistics.
- Observed Statistics: 0.052475425403588016

We also draw distribution plots of these two distributions. 

<iframe src="assets/res_percen_demand_missing.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/res_percen_demand_not_missing.html" width=800 height=600 frameBorder=0></iframe>

We use permutation tests to shuffle the missingness of `DEMAND.LOSS.MW` 10000 times and get 10000 simulated results of Kolmogorov-Smirnov statistic. The red line marked the observed stat.

<iframe src="assets/mar_not_depend.html" width=800 height=600 frameBorder=0></iframe>

We get a p-value of 0.2282. Using 0.05 as the significance level, since the p-value 0.2282 > 0.05, we fail to reject the null hypothesis that the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is missing is the same as the distribution of the `RES.PERCEN` when `DEMAND.LOSS.MW` is not missing. In other words, the missingness of `DEMAND.LOSS.MW` does not depend on `RES.PERCEN`.

## Hypothesis Testing

The question we are going to research on is: are the geographical distributions of the number of outages caused by severe weather different in summer vs. in winter? 

First, we create a new column called `season`, dividing the `MONTH` values into four groups: spring for months 3, 4, 5; summer for months 6, 7, 8; fall for months 9, 10, 11; winter for months 12, 1, 2. 

We will conduct a permutation test.

### Setting Up the Testing

Null Hypothesis $H_0$: The distribution of climate regions for outages caused by severe weather is the same in summer and in winter.

Alternative Hypothesis $H_1$: The distribution of climate regions for outages caused by severe weather is different in summer and in winter.

We select only the useful columns and rows that correspond to outages caused by severe weather in summer or winter. The first few rows of this subset of data are shown below.

| YEAR | MONTH | CLIMATE.REGION | CAUSE.CATEGORY | CAUSE.CATEGORY.DETAIL |
|------|-------|-----------------|----------------|------------------------|
| 2000 | 1.0   | Southeast       | severe weather | winter storm           |
| 2000 | 1.0   | Southeast       | severe weather | winter storm           |
| 2000 | 6.0   | Southwest       | severe weather | wildfire              |
| 2000 | 8.0   | Southeast       | severe weather | NaN                    |
| 2000 | 8.0   | Central         | severe weather | NaN                    |

Since we are concerned with categorical data, it is proper to use TVD as the test statistics.

We choose to perform an one-sided test because TVD only determines how different two distributions are. The greater the TVD, the greater the difference.

The significant level we choose is `0.05`.

The observed difference in mean is `0.14705565819447183`.

We draw distribution plots of these two distributions.

<iframe src="assets/summer_region.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/winter_region.html" width=800 height=600 frameBorder=0></iframe>

### Permutation Test

<iframe src="assets/hypo_test.html" width=800 height=600 frameBorder=0></iframe>

We run permutation tests for 10000 times and the graph shows the distribution of permutation test results. The red line marks the observed value.

### Hypothesis Testing Conclusion

The p-value we get is 0.0312, which means that at a significant level of 0.05, we are able to reject the null hypothesis that the distribution of climate regions for outages caused by severe weather is the same in summer and in winter.

This result is reasonable because different climate regions often experience very different weather, including severe weather, and certain severe weather events tend to happen in specific season/time of the year. For example, certain regions tend to get more winter storms, and consequently more outages due to winter storms, during winter. Certain regions tend to get more wildfire, and consequently more outages due to wildfire, during summer. Thus, it makes sense that we see different distributions for outages casued by severe weather in terms of the climate region in different seasons. 
