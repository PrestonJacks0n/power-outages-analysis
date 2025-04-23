# Duration of Power Outages Analysis

by Preston Jackson (prestoj@umich.edu)

---

## Introduction (GOT BEHIND ON ADDING STUFF TO WEBSITE, SO IF YOU LOOK AT THIS BEFORE 6 AM ON 4/23 HAVE MERCY, IM INCLUDING EVERYTHING SOON)

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


When I did my initial data cleaning I didn't narrow down to the columns above right away. But, I did drop some columns right away. Those being:

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

`<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>`
(REPLACE)

---

## Framing a Prediction Problem

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```

| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 |

---

## Baseline Model


---
## Final Model


---
