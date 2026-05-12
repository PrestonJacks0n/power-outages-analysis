# Duration of Power Outages Analysis

by Preston Jackson (prestoj@umich.edu)

---

## Introduction

In this project, I studied a dataset containing information about power outages in the continental U.S from 2000 to 2016. The data has information focused on the geographical location of the outages, regional climate information, land-use characteristics, electricity consumption patterns, and economic characteristics of the states affected by the outages. I spent much time with the data and pondered questions such as:
1. What is the relation between state populations and number of power outages?
2. What states are most affected?
3. What is the mean time a power outage lasts per cause category?

Finally, I came to focus on: **What is involved in the duration of power outages?**

I felt that this question in particular was important because as someone from California, the state with the most power outages between 2000 to 2016, it is very stressful not knowing when the power will come back. So I wanted to look into what features are most helpful in predicting duration of power outages!

The dataset of focus consists of 1533 rows. The columns that I felt would be relevant to my question are:

| Column | Description |
| ----------- | ----------- |
| MONTH                   | Indicates the month when the outage event occurred |
| POSTAL.CODE             | Represents the postal code of the U.S. states (ex: CA) |
| CLIMATE.REGION          | U.S. Climate regions as specified by National Centers for Environmental Information (9 regions in continental U.S) |
| ANOMALY.LEVEL           | The oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. |
| OUTAGE.START.TIME       | Time of day when the outage event started |
| OUTAGE.RESTORATION.TIME | Time of day when the outage event ended |
| CAUSE.CATEGORY          | General categories for all the events causing the power outages |
| CAUSE.CATEGORY.DETAIL   | Detailed categories causing the power outages |
| OUTAGE.DURATION         | **Trying to Predict** (time in minutes) |
| CUSTOMERS.AFFECTED      | Number of customers affected by the power outage event |
| POPULATION              | Population in the U.S. state in a year |


When I did my initial data processing I didn't narrow down to the columns above right away. But, I did drop some columns right away. Those being:

| Column | Reasoning |
| -----------              | ----------- |
| variables                | Empty column with no information, meant to clarify that row 6 was the column names |
| OBS                      | An indexing that was not need after formatting the data into a dataframe |
| U.S._STATE               | Not needed with POSTAL.CODE |
| YEAR                     | Redundant info that is in OUTAGE.START.DATE |
| OUTAGE.START.DATE        | Was not interested in year and day, and month had its own column |
| OUTAGE.RESTORATION.DATE  | Same as above |


---

## Data Cleaning and Exploratory Data Analysis

The data cleaning steps that I took started with turning the excel sheet into a dataframe, this involved skipping the empty rows. Next, I had to reset the row indices. After that I removed the initial columns I felt I didn't need as stated right above this section. Outside this, my main two data cleaning steps were 1. creating a new dataframe with just the columns I wanted, I did this after creating a few graphs and getting a better feel for the data. 2. imputing data for the customers affected column. 

### Imputing Data
I found that the column containing customers affected was missing 443/1533 = 0.29, so almost 29% of its values. Here are the distrubutions before and after:

<iframe src="assets/customers-affected-boxplot.html" width=800 height=600 frameBorder=0></iframe>

I used median imputation strategy based on state and causal groups. I did this assuming that the number of customers affected by a power outage is heavily influenced by location and cause.

1. Primary strategy: For each missing value, I tried to impute using the median number of customers affected for the combo of POSTAL.CODE and CAUSE.CATEGORY. The goal was to use reasonable numbers for each region and cause.

2. Fallback strategy: If no such comboexisted, I used the state median, assuming that region median was next best in filling a value for affected.

3. Final fallback: If neither of the imputations above worked due to lack of data, I used the global median across the dataset to fill the missing value.

My tiered method allowed for state and cause based imputations when possible, falling back to more general numbers when needed. It reduced the natural variation slightly, but aimed to minimize the introduction of bias and affects of outliers. 

### Visualizing Data

The following are relevant plots I generated while trying to grow my knowledge of the data and trying to understand what influences outage duration:

<iframe src="assets/count-cause.html" width=800 height=600 frameBorder=0></iframe>

The above plot is the distrubution of outages across the CAUSE.CATEGORY column. This helped me learn that the cause of outages was heavily dominated by severe weather and intentional attacks. I followed up on this graph by looking at the distrubution of outages across the CAUSE.CATEGORY.DETAIL column. This proved less fruitful, as the column had no consitency in labels: snow/ice storm vs. snow/ice vs. winter storm. For reference, here is the last couple items from looking at value counts of that column. 

| Detail | Count |
| -----------              | ----------- |
| HVSubstation interruption | 1 |
| cables                    | 1 |
| Petroleum                 | 1 |
| thunderstorm; islanding   | 1 |
| failure                   | 1 |

<iframe src="assets/statePop-count.html" width=800 height=600 frameBorder=0></iframe>

The above plot is the relation between number of outages and state population. This helped me learn that there was a trend that as population increased, so did the number of outages. This piece of info may not seem super attached to the question, but it helped lead me to the intuition that number of people affected might be important. 

<iframe src="assets/aggregate.html" width=800 height=600 frameBorder=0></iframe>

The above plot is the relation between number of outages and customers affected, with a grouping by cause. This helped me learn how the data was spread across the different causes, and that I wanted to focus on specifically the severe weather data for my prediction model. 

---

## Framing a Prediction Problem

**My Prediction Problem:** Can I predict the duration of weather caused power outages?

This is a regression problem!

The response variable is OUTAGE.DURATION and I chose it because as I said before, it can be crucial to have an idea of when you will get power back up and available. 

I am using RMSE as my metric to evaluate. I felt that this would be best because I need to know how much I am missing the duration time by. I want to minimize that value, meaning the duration I predict is close to actual duration.

The data I end up using is month, anomaly level, climate region, and customers affected. These would all be datapoints that should be somewhat easily attainable right away when a power outage is detected. 

---

## Baseline Model

I trained a linear regression model to predict the numeric outcome OUTAGE.DURATION.HOURS (duration of the power outage), using: MONTH and CLIMATE.REGION.
I implemented this using a scikit-learn pipeline with a ColumnTransformer to encode categorical variables, followed by a LinearRegression estimator.
Month is nominal and was one hot encoded. Climate Region is also nominal and one hot encoded. 

My model achieved RMSE: 95.35554416361052 and R² Score: -0.09404484757382425. This means that my model is objectively not good. I would be better off just predicting a mean value. A RMSE is really just not great. 

---
## Final Model

In the improved model, I added two new features: CUSTOMERS.AFFECTED (Quantitative) and ANOMALY.LEVEL (Quantitative).

My thoughts were that outages affecting more people may take longer to resolve due to coordination efforts, but also they could take less time due to priority. Next, I thought that higher anomaly levels would correspond to more intense weather conditions, causing outages with longer restoration times.

I used Ridge Regression, which is the regularized version of linear regression that aims to stop overfitting. This is especially useful when multicollinearity is present, which can be a concern since I one-hot encoded more than one categorical feature. My decicion stemmed from me knowing that regularization can improve coefficient estimates, especially when combining several categorical and numerical features.

So:

Categorical features (MONTH, CLIMATE.REGION) were one-hot encoded.

Numerical features (CUSTOMERS.AFFECTED, ANOMALY.LEVEL) were standardized using StandardScaler.

Hyperparameter selection:

I used GridSearchCV over the alpha parameter of Ridge. With  Negative RMSE (meaning low RMSE is better).

Possible alphas: [0.01, 0.1, 1.0, 10.0, 100.0]

My model achieved RMSE: 94.35 and R² Score: -0.071 with the best alpha being 10.0. Sadly, this was not much better than my baseline, but did see a slight improvement. Sadly my path of logic did not play out and this model was a bit of a let down. 

---
