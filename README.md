# Revealing Patterns in Power Outages to Better Identify Areas for Improvement
by Aakash Davasam

## Introduction
Access to electricity and power is becoming more vital than ever as we live in a tech-centric world. With the rise in the implementation of bleeding-edge machine technology in applications that were once thought mundane, having adequate access to electricity is necessary to make the proper advancements. Understanding the causes of power outages and how they vary in different regions can help us develop new and more advanced infrastructure that would decrease the number of power outages in a given area.

Question: What are the main causes of power outages, and how do they vary across different regions?

The dataset used for this analysis was obtained from the [Purdue Power Outage dataset](https://engineering.purdue.edu/LASCI/research-data/outages). There are 1534 rows in this dataset, with the relevant columns and column descriptions (derived from [this study](https://doi.org/10.1016/j.dib.2018.06.067)) used for analysis:

| Column Name | Description |
| ----------- | ----------- |
| YEAR | The year when the outage occurred. |
| MONTH | The month when the outage occurred. |
| U.S._STATE | The state of the outage occurrence. |
| NERC.REGION | The North American Electric Reliability Corporation (NERC) region. |
| CLIMATE.REGION | U.S. Climate region, 9 regions specified by the National Centers for Environmental Information |
| ANOMALY.LEVEL | A 3-month running mean of the oceanic El Niño / La Niña index. |
| CLIMATE.CATEGORY | A categorical description of the climate episodes. Values can be "warm", "cold", or "normal". |
| CAUSE.CATEGORY | The cause of the power outage (limited description) |
| CAUSE.CATEGORY.DETAIL | A more detailed description of the power outage |
| OUTAGE.DURATION | The duration (in minutes) of the power outage. |
| TOTAL.PRICE | Average monthly electricity price in the given U.S. state (in cents/kilowatt-hour) |
| CUSTOMERS.AFFECTED | The number of customers that were affected by the outage. |
| POPULATION | The population of the given U.S. state in the year of when the power outage occurred. |

## Data Cleaning and Exploratory Data Analysis
The dataset originally contained 53 columns, however, only 13 columns were retained for further analysis. These columns, as detailed above, seem to give the most descriptive information in determining potential causes of power outages in a give region. These 13 columns did not undergo further cleaning as all of the columns were already in the desired datatype.

Rows that had Hawaii or Alaska were removed, as these states are not a part of the contiguous U.S. states, and may introduce data that is outside the scope of this analysis as these two states are located in very different regions. As a result, there are now 1528 rows in the modified dataset.

The first 5 rows of the cleaned DataFrame is shown below.

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   TOTAL.PRICE |   CUSTOMERS.AFFECTED |   POPULATION |
|-------:|--------:|:-------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|--------------:|---------------------:|-------------:|
|   2011 |       7 | Minnesota    | MRO           | East North Central |            -0.3 | normal             | severe weather     | nan                     |              3060 |          9.28 |                70000 |      5348119 |
|   2014 |       5 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | intentional attack | vandalism               |                 1 |          9.28 |                  nan |      5457125 |
|   2010 |      10 | Minnesota    | MRO           | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |              3000 |          8.15 |                70000 |      5310903 |
|   2012 |       6 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |              2550 |          9.19 |                68200 |      5380443 |
|   2015 |       7 | Minnesota    | MRO           | East North Central |             1.2 | warm               | severe weather     | nan                     |              1740 |         10.43 |               250000 |      5489594 |

As a form of preliminary analysis, a univariate distribution of the different events that led to a power outage is shown below.

<iframe
  src="assets/power-outage-causes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As expected, severe weather was the main cause of a power outage. However, it was interesting that intentional attacks came second, suggesting that a good portion of power outages are intentional.

Additionally, the relationship between the average monthly electricity price (TOTAL.PRICE) was plotted against the respective U.S. state.

<iframe
  src="assets/state-vs-price.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

It is interesting to note how states like California, New York, Vermont, and Connecticut tend to have higher monthly prices on average. This may suggest that it is more expensive to maintain the infrastructure in these states as they may be more susceptible to a power outage.

The dataframe was grouped by the climate region and climate category in an effort to look at the mean outage duration for each region categorized by the cold, warm, and normal labels of the climate.

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

It appears that the warm category generally tends to have higher mean outage durations, except for the Central and East North Central regions, where the cold category has higher averages. This may suggest how regions that are warm may lead to the power outage to last for a longer time.

## Assessment of Missingness
It appears that the CAUSE.CATEGORY.DETAIL column could be NMAR. This is because even though the missing values may have some dependence on the CAUSE.CATEGORY column, the missingness of this column are really dependent on the values themselves since the detail of why a power outage occurred can be explained without looking at data from the other columns. The missingness of this column can be MAR if we had info on the weather patterns at the time of the outage as well as access to the local news, especially if the outage occurred due to a public appeal.


