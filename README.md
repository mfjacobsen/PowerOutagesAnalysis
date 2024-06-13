# Power Outages Analysis

By: Matthew Jacobsen

## Introduction

This report will analyze power outage data in the US collected between 2000 and 2016. It 
is available here:

https://engineering.purdue.edu/LASCI/research-data/outages

A detailed explanation of the data is available here:

https://www.sciencedirect.com/science/article/pii/S2352340918307182

The dataset has a total of 1534 observations and 55 columns. Each observation is 
a single power outage in the US. Features of the dataset include the following:
- Year
- Month
- Start time and end time of the outage
- US State of occurence
- The Climate Region of the State
- Anomaly Level (El Nino/ La Nina)
- Climate Category (warm, normal, cool)
- Cause Category
- Outage Duration
- Number of customers affected

There are many other features, 55 in total. Most features are  yearly aggregated statistics
about the particular State's energy usage and economic activity. The above columns
are the ones we will focus on. Most of the features are self explanatory, however,
I will give detail on some of them below.

#### Climate Region
The climate region is the region of the US that the state belongs to, of which there are 9: 

- East North Central
- Central
- South
- Southeast
- Northwest
- Southwest
- Northeast
- West North Central
- West

#### Anomaly Level and Climate Category
The anomaly level is the sea surface temperature of an area in the ENSO region of thePacific Ocean. The
temperature of this region is indicated in the climate category as cool, normal,
or warm. The surface temperature in this region is recognized to cause extreme
weather patterns depending on whether it is warmer or cooler than average. When
it is warm it is Called El Nino and when it is cold it is called La Nina. The anomaly
level is the temperature in this region at the time of the outage.

#### Cause Category
The cause category is the reason for the outages. Some examples are Thuderstorm,
Snow, Vandalism, etc.


## Data Cleaning and Exploratory Analysis

#### Cleaning 

My first step in cleaning the data was to convert the outage start time into
timestamps. They original format was a string, so I took advantage of Pandas
timestamp functions to turn the start times into much for useable format. From
there, I immediately engineered a new feature called Outage Start Time. I split
the start times up into categories of morning, afternoon, and night. The time
cutoffs were:

- Morning: 04:00 - 12:00
- Afternoon: 12:00 - 20:00
- Night: 20:00 - 04:00

I then made a scond new column called customer-minutes, which was the product 
of the Outage Duration and Customers Affected columns. I believed that this would 
be an interesting measure of severity, that isn't quite captured by the other
severtiy columns. This is because there are quite a number of observations where
there are many customers affected, but the outage duration is zero minutes. The
customer-minutes column therefore will be most severe when a large number
of customers experienced a long outage. 

The dataset contained suprisinglfy few missing values in our columns of interest.
In obervations that did have missing data, often many of our columns were missing. 
We will ignore the missing values for now. Below is the first few rows of the dataset:

| MONTH | CLIMATE.REGION     | CAUSE.CATEGORY.DETAIL | OUTAGE.START.PERIOD | ANOMALY.LEVEL | CUSTOMERS.AFFECTED | OUTAGE.DURATION | DEMAND.LOSS.MW |
|------:|:-------------------|:----------------------|:--------------------|--------------:|-------------------:|----------------:|---------------:|
|     6 | East North Central | thunderstorm          | Morning             |           0.2 |             300000 |            3960 |             75 |
|     3 | East North Central | sabotage              | Morning             |           0.6 |               5941 |             155 |             20 |
|     7 | East North Central | vandalism             | Morning             |          -0.3 |                  0 |               0 |              0 |
|     5 | East North Central | vandalism             | Afternoon           |          -0.4 |                  0 |            1322 |              0 |
|     5 | Central            | vandalism             | Night               |           0.8 |                215 |              95 |              1 |

#### Exploratory Analysis

To get an idea of the typical outage duration, I plotted the distribution with 
a histogram. As you can see, the outage duration follow a roughly exponential 
distribution, with most outages being very short, and progressively fewer of longer
duration. This is as expected. Most utility companies fix outages immediately, 
and only the most severe ones continue for longer than a few hours or days.


<iframe
  src="Assets/Outage Duration Distribution.html"
  width="600"
  height="450"
  frameborder="0"
></iframe>

Then I wanted to see if there was any clear association between the duration of 
the outage and the anomaly level. I plotted a scatter plot showing the relationship.
There were a few outliers above 35000 minutes which I ommitted in order to better
see the majority of the data. We can see that the more extreme outages tend to
happen when the Anomaly Level is in the 'normal' Climate Category between -0.5
and 0.5.

<iframe
  src="Assets/Duration vs Level.html"
  width="600"
  height="450"
  frameborder="0"
></iframe>

Next, lets look at the average outage duration for each climate region, categorized
by the Climate category. Remember, the climate category is the temperature
level of the ENSO region that corresponds to the Anomaly Level. Note how much 
longer the average duration of outages are in the Northwest region when the 
Climate Category is 'warm'. We'll look at the significance of these values later.

| CLIMATE.REGION     |     cold |    normal |    warm |
|:-------------------|---------:|----------:|--------:|
| Central            | 2799.86  | 2708.7    | 2413.84 |
| East North Central | 6568.79  | 5271.22   | 3022.12 |
| Northeast          | 3657.25  | 2261.33   | 4175.91 |
| Northwest          |  874.681 |  733.612  | 3063.54 |
| South              | 2012.71  | 3753.06   | 1861.4  |
| Southeast          | 1707.07  | 2392.27   | 2528.94 |
| Southwest          |  544.591 |  296.136  | 5127.68 |
| West               | 1762.71  | 1249.84   | 2044.23 |
| West North Central |  200     |   28.4286 | 2486.5  |

## Assignment of Missingness

#### NMAR Analysis

The Outage Duration column has values likely missing as NMAR. It's probable that outages whose 
duration were less than a few seconds did not have their duration recorded 
during reporting. It may be that reporters did not understand the value of 
having an outage of zero minutes in duration. Additional information that could
have been collected to make this NMAR would be to include a column that 
categorizes the outage as an 'outage' or a 'hit', with a power hit being anything
and outage with a a duration less than a few seconds.

#### Missingness Dependency

Now we'll test if The Cause Category Detail is MAR with respect to a few differnets
columns using permutation testing with total variation distance as our test statistic. The 
null hypothesis is that there is no difference in variation for each column. The 
alternative is that there is a differnence. The p values for each column are below. We can see that 
the Cause Category Detail is MAR with respect to Month, State, NERC Region, 
Climate Region, and Climate Category, but not with respect to Outage Start Period.

|                     |     P Value |
|:--------------------|------------:|
| MONTH               | 0.000999001 |
| U.S._STATE          | 0.000999001 |
| NERC.REGION         | 0.000999001 |
| CLIMATE.REGION      | 0.000999001 |
| CLIMATE.CATEGORY    | 0.000999001 |
| CAUSE.CATEGORY      | 0.000999001 |
| OUTAGE.START.PERIOD | 0.305694    |

## Hypothesis Testing

#### Initial Test
I wanted to test the following hypothesis:

Null: The average outage duration is the same for each climate category (warm, normal, cool).
Alternative: The average outage duration is not the same for each climate category.

As a test statistic, I used the mean squared error of the average outage duration
in each category. Under the null, we expect no difference in the averages, so we
will reject the null for large values of our test statistic. I obtained the p
value by permutation and it was around 0.74. Therefore, I failed to reject the null.
It seems that there is no difference in the average outage duration for each climate
category. 

#### Blocking by Climate Region

I then blocked outages by climate region and re ran the hypothesis test for each
region. I wanted to know if in specific regions, average outage duration depeneded
on the climate category. With multiple hypothesis testing like this, it's good practice to control for the increased risk of a type one error rate by adjusting the p values. We will do so with the Bonferroni method. With 9 hypothesis tests, we will reject the
null at a significance level of 0.05 / 10 = 0.005. We can see that in the Northwest
Region, we reject the null, it seems the climate cateory seems to have an effect on outage duration.

|                    |     P Value |
|:-------------------|------------:|
| Northwest          | 0.000999001 |
| nan                | 0.000999001 |
| Southwest          | 0.01998     |
| South              | 0.0539461   |
| Northeast          | 0.0539461   |
| West North Central | 0.101898    |
| East North Central | 0.518482    |
| Southeast          | 0.558442    |
| West               | 0.686314    |
| Central            | 0.911089    |

## Framing a Prediction Problem

I wanted to predict the outage duration column. We have seen that the outage duration
is significantly affected by climate category for some climate regions, and is 
nearly significant for several others. I believe that when we add additional
features, we will be able to predict the outage duration with more accuracy.
Since this is a numerical prediciton, it is a regression problem. We'll 
evaluate our model with the r-squared, which will at summarize the proportion
of the variance in the outage duration that is explained by our chose features.
Essentially, an r squared that approaches one shows that we have a strong model.

## Baseline Model

I started my baseline model off with only a few features. I wanted to see how
good only a few features could be when predicting average duration. I chose three,
'Month', 'Climate Region', and 'Anomaly Level'. My idea is that the anomaly level
causes certain weather patterns in different months of the year. These 
weather patterns affect the climate regions in different ways, and maybe these
differences would be reflected in the outage durations. Month, and Climate 
region are both nominal cateorical variables which i one hot encoded. Anomaly level is quantitative variable that I left as is. I implemented a RandomForest Regressor with default hyperparameters.

The results of my intital model were quite poor. So poor in fact, that my r-squared
was -0.08 with a RMSE of 3741. This means that it would be better to just choose the mean outage duration over using my model. My baseline model is worse than guessing a constant value every time!

## Final Model

#### Feature Selection

It's clear that my model needed more features and tuning. I started by adding 
more features. I added the Cause Category Detail because it's likely that certain
types of causes cause longer durations than others. I thought there may have
been an association between the time of day that outages started and outage 
duration. For example, night time outages may be longer because utility companies
have few workers at those hours. I also added the customers affected column 
because I thought it's possible that big outages where lare numbers of customers
are affected may take longer to fix. 

#### Feature Engineering
I Binarized the number of customers affected column with a threshold
of 200,000; separating outages as mainor or major by  customers affected. I also
decided to transform the anomaly level into the absolute value of the anomaly level.
Based on the histogram shoqn previously, the distribtuion of anomaly level is 
roughly symmetric with respect to outage duration. I think that this transformation
better captures the association between these two variables.

#### Hyperparameter Selection
I continued with the Random Forest Regressor, but I used a Grid Search CV to
tune some hyperparameters. The following are the hyperparameters tuned, and the 
options iterated through by GridSearchCV:

- Max Depth: [4, 5, 6, 7, 8, 9, 10],
- Max Features: [3, 4, 5],
- Minimum Samples Split: [3, 4, 5, 6]

The results of the grid search that I used were the following:

- Max Depth: 8
- Max Features: 4
- Minimum Samples Split: 4

#### Results

My model improved significantly. It's r-squared improved to a positive 0.41, with
an RMSE of 2779. 

|       |    RMSE |   r Squared |
|:------|--------:|------------:|
| Base  | 3741.54 |   -0.077984 |
| Final | 2778.78 |    0.405406 |

## Fairness Analysis

I wanted to evaluate the fairness of my model for each climate region using a 
permutation test and the fullowing hypothesis.

Null: There is no difference in the RMSE of my model across climate regions.
Alternative: There is a difference in the RMSE of my model across climate regions. 
    
Below is the RMSE of my model by climate region. It seems that the Southwest Region
is significantly different than the rest, but we'll check if it's due to randomness
or not with the test.

|                    |     RMSE |
|:-------------------|---------:|
| Southwest          |  622.90 |
| Northeast          | 1521.67  |
| Northwest          | 1657.27  |
| West North Central | 2080.29  |
| West               | 2754.61  |
| Southeast          | 3010.53  |
| Central            | 3307.91  |
| South              | 4104.96  |
| East North Central | 6409.97  |

As a test statistic, I'll use the RMSE. To be clear, this the the RMSE of the above
table; the RMSE of the RMSE's for each climate category. RMSE is an apporpriate
test statistic here, it is just by chance that I am using RMSE as the evaluation
metric of the model. 

Under the null hypothesis, the test statistic will be small, so we will reject for 
large value of the test statistic. I obtained a p value of 0.1654 by permutation.
Therefore, it seems that our model is fair across all climate categories.

#### Final Thoughts

While the improvement of the final model over the base model is significant, it's still not a particularlyuseful model. An r-squared of 0.41 is rather low, and the RMSE of 2779 meansthat we are off by an average of 2779 minutes in our predictions, or 46 hours. Ideally, this model could be used utitlity provideds in climate regions to estimate the rough duration of outages. However, at it's current performance, it's not vapable of doing this. Further feature selection and engineering is required for this model to be of practical use. 