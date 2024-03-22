# Revealing Patterns in Power Outages to Better Identify Areas for Improvement
by Aakash Davasam

## Introduction
Access to electricity and power is becoming more vital than ever as we live in a tech-centric world. With the rise in the implementation of bleeding-edge machine technology in numerous applications, having adequate access to electricity is necessary to make the proper advancements. Understanding the causes of power outages and how they vary in different regions can help us develop new and more advanced infrastructure that would decrease the number of power outages in a given area.

Question: What are the main causes of power outages, and how do they vary across different regions/climates?

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
  height="420"
  frameborder="0"
></iframe>

As expected, severe weather was the main cause of a power outage. However, it was interesting that intentional attacks came second, suggesting that a good portion of power outages are intentional.

Additionally, the relationship between the average monthly electricity price (TOTAL.PRICE) was plotted against the respective U.S. state.

<iframe
  src="assets/state-vs-price.html"
  width="800"
  height="420"
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

#### Test 1
A permutation test was developed to compare the missingness of CUSTOMERS.AFFECTED and how it affected whether the CLIMATE.CATEGORY was 'warm' or 'cold'. Rows with the 'normal' label were omitted for this test. The initial distribution of these two columns is shown below.

<iframe
  src="assets/mis1-distribution.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>
It can be noted that the proportions are relatively similar for both the 'warm' and 'cold' distributions. A permutation test was run, and the results are shown below. The TVD was used as the test statistic.

**Null Hypothesis:** The distribution of CLIMATE.CATEGORY when CUSTOMERS.AFFECTED is missing is the same as the distribution of CLIMATE.CATEGORY when CUSTOMERS.AFFECTED is not missing.

**Alternative Hypothesis:** The missingness of CUSTOMERS.AFFECTED affects the distribution of CLIMATE.CATEGORY.
<iframe
  src="assets/mis1-emp-distribution.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>
The vertical line in red represents the observed TVD statistic, with a p-value of 0.128. Since this p-value is greater than the alpha=0.05 threshold, we fail to reject the null hypothesis. However, even though we fail to reject the null, it should still be noted that an argument can be made that the missingness of CUSTOMERS.AFFECTED is MAR and can be dependent on CLIMATE.CATEGORY. This could be due to how the weather patterns can differently affect the number of customers. More data is required to better conclude if the null hypothesis can be rejected.

#### Test 2
Another permutation test was carried out to assess the missingness of the ANOMALY.LEVEL on the POPULATION column. These two columns are not dependent with each other, as the state population has no affect on the anomaly level at all.

<iframe
  src="assets/mis1-distribution.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

Even though the distribution may appear similar, the K-S statistic was not implemented as the test statistic since the shapes are still different. The difference in group means was used as the test statistic instead.

**Null Hypothesis:** The distribution of POPULATION when ANOMALY.LEVEL is missing is the same as the distribution of POPULATION when ANOMALY.LEVEL is not missing.

**Alternative Hypothesis:** The missingness of ANOMALY.LEVEL affects the distribution of POPULATION.

<iframe
  src="assets/mis2-emp-distribution.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

With a p-value of 0.124, we fail to reject the null. It cannot be concluded that these two columns affect each other, which makes sense because the state population is a number derived from the Census.

## Hypothesis Testing
I ran a permutation test to determine whether the population distributions vary between a 'warm' and 'cold' climate category. Determining the answer to this can help justify whether the cause of a power outage can also be influenced by the population of a cold or warm area.

**Null Hypothesis:** Alternative Hypothesis: In the entire dataset, the population of people in 'cold' and 'warm' climate categories have the same distribution, and the observed differences in our samples are due to random chance.

**Alternative Hypothesis:** A 'cold' climate category has a larger population than the 'warm' climate category.

**Test Statistic and Significance Level:** Difference in group means (warm - cold), with alpha=0.05.

After permuting the group means, the following distribution below was created:

<iframe
  src="assets/permutation-test.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

The observed difference is shown in the vertical red line. With the p-value being 0.001, which is less than the significance threshold of 0.05, we reject the null hypothesis. It appears to be the case that a 'cold' climate category generally has higher populations than a 'warm' climate category.

## Framing a Prediction Problem
I will predict whether the cause of a major power outage is weather-related using a binary classifier. The response variable is a derivation of the CAUSE.CATEGORY column, but rather than predicting multiple possible scenarios, I will discretize the data into severe weather and other (not weather-related) prior to making the predictions. I chose this prediction problem because it nicely ties in with the main focus of this project - which is to ultimately determine the main cause of a power outage, given other information such as the region and climate. I will utilize the F1-score to evaluate my model rather than other metrics such as accuracy because the distribution of this dataset is uneven, and the F1-score takes both precision and accuracy into account.

## Baseline Model
In the baseline model, I used a decision tree classifier. First, I binarized the CAUSE.CATEGORY column so it only shows whether or not the cause was weather-related. For the prediction, I used two columns from the power outage dataset: CLIMATE.REGION and ANOMALY.LEVEL. The CLIMATE.REGION column is a nominal categorical variable, and this was one-hot encoded (with the first variable dropped) during the preprocessing state. The ANOMALY.LEVEL is a continuous numerical variable, and no transformations were performed on this column.

The performance of the model was decent, but better than expected considering that only two columns were used. After splitting the dataset into train/test data and training the model on the training data, the accuracy of the training set was 0.756 and 0.668 for the test set. However, as mentioned above, accuracy is not the best metric so the F1-score was also calculated. When compared to a series of predicted outcomes, the F1-score came out to be 0.691. Since the score is larger than 0.5, this suggests that there's a little bit of a balance between precision and recall, but it is not perfect since not all the false positives and false negatives are minimized.

## Final Model
In the final model, I added the POPULATION column (standardized during processing) as well as the TOTAL.PRICE column (quantile transformed during processing). This is because power outages can be affected by the number of people on that grid and a higher average monthly price may indicate that it would cost more to maintain the infrastructure. Therefore, I believed these two columns are good for the prediction task.

I decided to choose the random forest classifer model, with the n_estimators being 200 and max_depth equaling 10. These hyperparameters were optimzed using the GridSearchCV method. The final model's F1-score is 0.769, which is quite an improvement from the baseline model. This shows how the model is doing decently well in minimizing both the false positives and false negatives. The accuracies also increased compared to the baseline model, but those values are not reported as they pose a false reflection on the quality of the model due to an uneven distribution of data in the dataset.

## Fairness Analysis
I chose to compare the model distribution of low and high populations and see if that affected the fairness of the model in any way. I used a threshold of 8000000 to binarize the POPULATION column. The test statistic used was the TVD with a significance level of 0.05.

**Null Hypothesis:** The model is fair. The TVD for smaller and larger populations are about the same, and any differences can be explained by random chance.

**Alternative Hypothesis:** The model is unfair, as the TVD is much larger than the permuted distributions.

After running the permutation test, we can reject the null, since the p-value was 0.0. This shows how this model can be unfair to a lower population.