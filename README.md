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
| POPULATION | The population of the given U.S. state in the year of when the power outage occurred. |

## Data Cleaning and Exploratory Data Analysis