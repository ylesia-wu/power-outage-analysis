# Power Outages 🔋 Project

This is the Project 3 under course DSC80

By Ylesia Wu & Junyue Lin

## Introduction

The dataset is [sourced](https://engineering.purdue.edu/LASCI/research-data/outages) from an article entitled “A Multi-Hazard Approach to Assess Severe Weather-Induced Major Power Outage Risks in the U.S.” (Mukherjee et al., 2018). The data pertains to the major outages witnessed by different states in the continental U.S. from January 2000 to July 2016. Moreover, this data also presents on geographical location of the outages, date and time of the outages, regional climatic information, land-use characteristics, electricity consumption patterns, and economic characteristics of the states affected by the outages.

The original dataframe contains 1535 rows and 57 columns. For further exploratory data analysis, we created two new columns for start and restoration respectively, representing the start/restoration datetime of each outage by combining the existing date and time columns. 

In this project, first, we first cleaned the dataset and conducted exploratory data analysis to obtain some basic information about the data set and the relationship between columns. Then we explored the potential reasons for missingness for certain columns to see whether they are missing by random (MAR) or not missing by random (NMAR). In the MAR analysis, by conducting permutation tests, we can analyze the dependency of the missingness of a column on other columns. Thirdly, we would focus on the research question: are the geographical distributions of the number of outages caused by severe weather different in summer vs. in winter? 

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

|     id | name                                 |   minutes | submitted           |   n_steps |   n_ingredients |   ave_rating |
|-------:|:-------------------------------------|----------:|:--------------------|----------:|----------------:|-------------:|
| 333281 | 1 brownies in the world    best ever |        40 | 2008-10-27 00:00:00 |        10 |               9 |            4 |
| 453467 | 1 in canada chocolate chip cookies   |        45 | 2011-04-11 00:00:00 |        12 |              11 |            5 |
| 306168 | 412 broccoli casserole               |        40 | 2008-05-30 00:00:00 |         6 |               9 |            5 |
| 286009 | millionaire pound cake               |       120 | 2008-02-12 00:00:00 |         7 |               7 |            5 |
| 475785 | 2000 meatloaf                        |        90 | 2012-03-06 00:00:00 |        17 |              13 |            5 |


### Exploratory Data Analysis

### Univariate Analysis

In the univariate analysis, we would analyze the distribution of number of ingredients and the distribution of number of steps

<iframe src="assets/univariate_month.html" width=800 height=600 frameBorder=0></iframe>

This shows that the distribution could be approximate as a gaussian distribution but skewed right. We would say that the graph centered around 8, meaning that most recipes have 8 ingredients.

<iframe src="assets/fig2.html" width=800 height=600 frameBorder=0></iframe>

The distrubution also show similar trend in the number of steps, which is a right skewed gaussian distribution. By comparing at the two graph, the graph for the number of distribution is more centered. The center for the graph is around 7, meaning most recipes have 7 steps. Also, we could see the graph have a lot outliers that have very big step numbers. After observing the dataset and also consider together with the `minutes` column and real life situation, we decided to choose steps greater than 40 and minutes greater than 200 as outlier and not faithful data.

### Bivariate Analysis

Then, we do bivariate analysis between the number of steps and the number of ingredients


<iframe src="assets/fig3.html" width=800 height=600 frameBorder=0></iframe>


When the individual distritbution for number of steps and number of ingredients seems very similar, the scatter plot does not show very strong correlation between the number of steps and number of ingredient. We could say that there is weak positive relationship between the number of steps and the number of ingredients.


<iframe src="assets/fig4.html" width=800 height=600 frameBorder=0></iframe>


We could see that the average rating and the number of ingredients in the recipes do not have much relationship with each other. Especially with number of ingredients smaller than 15, it is almost a horizontal line, showing no relationship between the two variables. The large fluctuate with number of ingredients larger than 15 could be due to relatively small data size collected within that range.

### Interesting Aggregates

In the aggregates analysis, we will study the total fat with the cooking minutes


|   ('minutes', '') |   ('mean', 'total fat (PDV)') |   ('median', 'total fat (PDV)') |   ('min', 'total fat (PDV)') |   ('max', 'total fat (PDV)') |
|------------------:|------------------------------:|--------------------------------:|-----------------------------:|-----------------------------:|
|                 0 |                      46       |                              46 |                           46 |                           46 |
|                 1 |                       7.78603 |                               0 |                            0 |                          159 |
|                 2 |                       9.69053 |                               0 |                            0 |                          419 |
|                 3 |                      12.5794  |                               2 |                            0 |                          411 |
|                 4 |                      20.4719  |                               7 |                            0 |                          258 |

This is the pivot table for the `total fat` and `minutes`


<iframe src="assets/fig6.html" width=800 height=600 frameBorder=0></iframe>


<iframe src="assets/fig7.html" width=800 height=600 frameBorder=0></iframe>

One interesting result that we find in the aggregates data is that there is a peek for total fat in the recipe around 60 minutes of cooking time. Otherwise the recipes' total fat is fluctuate around 50 PDV, which is around 1000 calories. This shows that most recipes collected are recipes for health food.

## Assessment of Missingness

In this part, we will be conducting assessment of missingness on the merged dataframe.

### NMAR Analysis

In the NMAR, we focus on the missingness of the review in the merged dataframe. The missingness of the review is probably because someone think that the recipe is easy and there is nothing worth talking about. Thus, they just skip the review. If we want additional data to prove it (making it MAR), we could add a personal difficulty evaluation for every person who use this recipe.

### Missingness Dependency

Now, we focus on the missingness of rating in the merged dataframe and test the dependency of this missingness. 
We are preparing to test the dependency of the missingness on the minutes, the time to finish the recipe, and the calories, the energy of the recipe.

> Minutes and Rating

Null hypothesis: the distribution of the minutes when rating is missing is the same as the distribution of the minutes when rating is not missing
Alternative hypothesis: the distribution of the minutes when rating is missing is different from the distribution of the minutes when rating is not missing
Observed Statistics: the absolute difference between minutes mean of these two distributions. We also draw distribution plots about these two distributions.

<iframe src="assets/fig11.html" width=800 height=600 frameBorder=0></iframe>

We use permutation test to shuffle the missingness of rating 1000 times and get 1000 simulating results about the absolute difference. 

<iframe src="assets/fig12.html" width=800 height=600 frameBorder=0></iframe>

Finally, we calculate the p-value 0.127. when we use 0.05 as a significance threshold, since 0.127 > 0.05, we fail to reject the null hypothesis that the rating is not dependent on the minutes. Based on our test result, we can see that the missingness of the rating is MCAR because the missingness of rating is not correlated with the minutes.

> Calories and Rating

Null hypothesis: the distribution of the calories when rating is missing is the same as the distribution of the calories when rating is not missing
Alternative hypothesis: the distribution of the calories when rating is missing is different from the distribution of the calories when rating is not missing
Observed Statistics: the absolute difference between calories mean of these two distributions. We also draw distribution plots about these two distributions.

<iframe src="assets/fig13.html" width=800 height=600 frameBorder=0></iframe>

We use permutation test to shuffle the missingness of rating 1000 times and get 1000 simulating results about the absolute difference. 

<iframe src="assets/fig14.html" width=800 height=600 frameBorder=0></iframe>

Finally, we calculate the p-value approximately 0.0. when we use 0.05 as a significance threshold, since 0.0 <= 0.05, we reject the null hypothesis that the rating is not dependent on the calories. Based on the result, we can see that the missingness of rating is the MAR because the rating is dependent on the calories. Probably if some recipes with relatively high or low calories will be more likely to have the missingness in the rating.

## Hypothesis Testing

The question we are going to research on is that: are regular recipes and complex recipes are rated in the same scale?

In this part, we will define a complex recipes as recipes have greater than 10 steps. We will conduct a permutation test.

### Setting Up the Testing

Null Hypothesis H0: People are rating all the recipes in the same scale.

Alternative Hypothesis H1: People are giving complex recipe lower rating

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

<iframe src="assets/fig10.html" width=800 height=600 frameBorder=0></iframe>

We ran permutation test for 10000 times and the graph shows the distribution of permutation test result. The red line marks the observed value.

### Hypothesis Testing Conclusion

The P-value for the testing is 0.009, which means that at significant level of 0.05, we are able to reject the null hypothesis.

This result could be reasonable since first, high level complexity of a recipes could mean difficulties in cooking the dish and increasing probability in failing. If people fail to cook the dish, they might give low rating to the recipe. Also, people might have higher expectation on the dish if it is complex and hard to make. Then, people might have a more strict rating scale for complex recipes. 

