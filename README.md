# PowerOutagesAnalysis

## The Data

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

#### CLimate Region
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