# Identify Fraud from Enron Dataset

<p style="text-align: right;">Sanghyun Lee:</p>

## Goal
In this project I want to identify person of interest (POI) of Enron disaster from [Enron dataset](https://www.cs.cmu.edu/~./enron/).

## Dataset Overview
The dataset have 21 features (incloud ‘poi’) and 146 people incloud 18 POIs. The features are this.

### features list

| features   | have missed value | features   | have missed value|
|:-----------|------------:|:-----------|------------:|
| deferred_income       |        have |total_stock_value    |     have |
| restricted_stock     |      have |director_fees    |     have |
| total_payments       |        have |  other    |     have |
| loan_advances       |       have |   from_poi_to_this_person    |     have |
| email_address    |     have |  restricted_stock_deferred    |     have |
| bonus    |     have |  exercised_stock_options    |     have |  
| poi    |     haven't |  deferral_payments    |     have |
| salary    |     have |  expenses    |     have |  
| from_this_person_to_poi    |     have |  from_messages    |     have |
| to_messages    |     have |  long_term_incentive    |     have |
| shared_receipt_with_poi    |     have |


'email_address' is not number and I will identify 'poi', so
I can use 19 features of this dataset.

Every features which I can use have missed value mentioned 'NaN'. I will change 'NaN' to zero(0) to figure out.

## Data Cleaning

I tried remove outliers of some features.
I choose salary, bonus, exercised_stock_options, from_poi_to_this_person, and defer_rate_of_restrict_stock.

### 1.  remove a key named ‘TOTAL’ . It is not a person’s name

Before I remove 'Total':

<img src="https://github.com/crespo86/uda-ml/raw/master/uda_figure_1-1.png" alt="Drawing" style="width: 200px;"/>

I checked the red point and get the red is 'Total'.

After I remove 'Total':

<img src="https://github.com/crespo86/uda-ml/raw/master/uda_figure_1-2.png" alt="Drawing" style="width: 200px;"/>

It is better

### 2.  salary

| MAX | MIN | MEAN | STD | 1IQR | MEDIAN | 3IQR |
|:----|:----|:----|:----|:----|:----|:----|:----|
| 1111258 | 477 | 284087.54 | 176186.41 | 211802 | 258741 | 308606.5 |

I tried to remove outliers of salary, but 3 POIs are in the outliers so i cannot removed.
 - (LAY KENNETH L, FASTOW ANDREW S, SKILLING JEFFREY K)


 ### 3. bonus
 | MAX | MIN | MEAN | STD | 1IQR | MEDIAN | 3IQR |
 |:----|:----|:----|:----|:----|:----|:----|:----|
 | 8000000 | 70000 | 1201773.07 | 1432752.54 | 425000 | 750000 | 1200000 |
 I tried to remove outliers of bouns, but 4 POIs are in the outliers so I cannot removed.
 -  (BELDEN TIMOTHY N, LAY KENNETH L, DELAINEY DAVID W, SKILLING JEFFREY K)

### 4. exercised_stock_options
| MAX | MIN | MEAN | STD | 1IQR | MEDIAN | 3IQR |
|:----|:----|:----|:----|:----|:----|:----|:----|
| 34348384 | 3285 | 2959559.26 | 5472156.88 | 506765 | 1297049 | 2542813 |
I tried to remove outliers of exercised_stock_options, but 4 POIs are in the outliers so I cannot removed.
-  (HIRKO JOSEPH, LAY KENNETH L, SKILLING JEFFREY K, RICE KENNETH D)

### 5. from_poi_to_this_person
| MAX | MIN | MEAN | STD | 1IQR | MEDIAN | 3IQR |
|:----|:----|:----|:----|:----|:----|:----|:----|
| 528 | 0 | 64.90 | 86.47 | 10 | 72.25 | 35 |
the max number is too high (second is 305) and this people is not in POIs (LAVORATO JOHN J) so I removed.

Before I removed 'LAVARATO JOHN J':

<img src="https://github.com/crespo86/uda-ml/raw/master/uda_figure_1-3.png" alt="Drawing" style="width: 200px;"/>

After I removed 'LAVARATO JOHN J':

<img src="https://github.com/crespo86/uda-ml/raw/master/uda_figure_1-4.png" alt="Drawing" style="width: 200px;"/>

## Data Adding
Before I start, I added a feature named **'defer_rate_of_restrict_stock'** . This feature is calculated like _restricted_stock_deferred  /  restricted_stock_. I think POIs have more information about company so they might just have few restricted_stock_defered rate.

### scatter plot of 'defer_rate_of_restrict_stock' and 'deferred_income'

<img src="https://github.com/crespo86/uda-ml/raw/master/figure_1-8.png" alt="Drawing" style="width: 200px;"/>

I removed the red outliers

<img src="https://github.com/crespo86/uda-ml/raw/master/figure_1-7.png" alt="Drawing" style="width: 200px;"/>
