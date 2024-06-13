# PowerOutagesAnalysis

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
- Outage Duration
- Number of customers affected

There are many other features, 55 in total. Most features are  yearly aggregated statistics
about the particular State's energy usage and economic activity. The above columns
are the ones we will focus on. Most of the features are self explanatory, however,
I will give detail on some of them below.

#### Climate Region
 The climate region is the region of the 
US that the state belongs to, of which there are 9: 

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
The anomaly level is the sea surface temperature of an area in the Pacific Ocean. The
temperature of this region is indicated in the climate category as cool, normal,
or warm. The surface temperature in this region is recognized to cause extreme
weather patterns depending on whether it is warmer or cooler than average. When
it is warm it is Called El Nino and when it is cold it is called La Nina. The anomaly
level is the temperature in this region at the time of the outage.

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

|   MONTH | CLIMATE.REGION     | CAUSE.CATEGORY.DETAIL   | OUTAGE.START.PERIOD   |   ANOMALY.LEVEL |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION |   DEMAND.LOSS.MW |
|--------:|:-------------------|:------------------------|:----------------------|----------------:|---------------------:|------------------:|-----------------:|
|       6 | East North Central | thunderstorm            | Morning               |             0.2 |               300000 |              3960 |               75 |
|       3 | East North Central | sabotage                | Morning               |             0.6 |                 5941 |               155 |               20 |
|       7 | East North Central | vandalism               | Morning               |            -0.3 |                    0 |                 0 |                0 |
|       5 | East North Central | vandalism               | Afternoon             |            -0.4 |                    0 |              1322 |                0 |
|       5 | Central            | vandalism               | Night                 |             0.8 |                  215 |                95 |                1 |

#### Exploratory Analysis

To get an idea of the typical outage duration, I plotted the distribution with 
a histogram. As you can see, the outage duration follow a roughly exponential 
distribution, with most outages being very short, and progressively fewer of longer
duration. This is as expected. Most utility companies fix outages immediately, 
and only the most severe ones continue for longer than a few hours or days.


<iframe
  src="Assets/Outage Duration Distribution.html"
  width="800"
  height="600"
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
  width="800"
  height="600"
  frameborder="0"
></iframe>
