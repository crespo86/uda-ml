# Identify Fraud from Enron Dataset

<p style="text-align: right;">Sanghyun Lee:</p>

## Goal
In this project I wanted to identify person of interest (POI) of Enron disaster from [Enron dataset](https://www.cs.cmu.edu/~./enron/).

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

Every features which I can use have _missed value mentioned 'NaN'_. I will change 'NaN' to zero(0) to figure out.

## Data Cleaning

I tried remove outliers of some features.
I choose salary, bonus, exercised_stock_options, from_poi_to_this_person, and defer_rate_of_restrict_stock.

### 1.  remove a key named ‘TOTAL’ . It is not a person’s name

Before I removed 'Total':

<img src="https://github.com/crespo86/uda-ml/raw/master/uda_figure_1-1.png" alt="Drawing" style="width: 200px;"/>

I checked the red point and get the red is 'Total'.

After I removed 'Total':

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

# Feature selection

## Adding data
Before I started, I added a feature named **'defer_rate_of_restrict_stock'** . This feature is calculated like _restricted_stock_deferred  +  restricted_stock_. I thought POIs have more information about company so they might just have few restricted_stock_defered.

the Code is like this

``` python
for a in data_dict:
   if data_dict[a]['restricted_stock'] != 'NaN' and data_dict[a]['restricted_stock_deferred'] != 'NaN':
       data_dict[a]['defer_of_restrict_stock'] = (data_dict[a]['restricted_stock'])+(data_dict[a]['restricted_stock_deferred'])
   elif data_dict[a]['restricted_stock'] != 'NaN' and data_dict[a]['restricted_stock_deferred'] == 'NaN':
       data_dict[a]['defer_of_restrict_stock'] = (data_dict[a]['restricted_stock'])
   else:
       data_dict[a]['defer_of_restrict_stock'] = 'NaN'
```
- 'restricted_stock_deferred' is normally minus, so I make plus.
- Also, if 'restricted_stock_deferred' is 'NaN', it means 0, so In some case which is 'restricted_stock' is a number and 'restricted_stock_deferred' is 'NaN' I use just 'restricted_stock' because a number - 0 = a number.

## Find Important Features

to check the effectiveness of features, I used Validation Set and DecisionTreeClassifier
- the data is small, I used StratifiedShuffleSplit which is shuffled k-fold.

here is the code

``` python
clf = tree.DecisionTreeClassifier(random_state=42)
clf.fit(features_train, labels_train)
print(clf.feature_importances_)
pred = clf.predict(features_test)
print(classification_report(labels_test, pred, target_names=['nopoi', 'poi']))
```
### Check my new Feature's effectiveness

#### First, I checked my new feature

Before I put my new Feature

|| precision   | recall  | f1-score   | support |
|:--|-----------:|------------:|-----------:|------------:|
|nopoi| 0.92       |        0.92 |0.92    |     38 |
|poi| 0.40       |        0.40 |0.40    |     5 |
|avg / total| 0.86       |        0.86 |0.86    |     43 |

After I put my new Feature

|| precision   | recall  | f1-score   | support |
|:--|-----------:|------------:|-----------:|------------:|
|nopoi| 0.92       |        0.95 |0.94    |     38 |
|poi| 0.50       |        0.40 |0.44    |     5 |
|avg / total| 0.87       |        0.88 |0.88    |     43 |                           

Both precision, recall score, and f1-score of poi are increased

#### Second, discard irrelevant features   

to discard irrelevant features, used **clf.feature_importances_** and **SelectFromModel**

importance list

| Features   | Importance  | Features   | Importance |
|:-----------|------------:|:-----------|------------:|
| bonus      |       25.4% |restricted_stock   |     0.0% |
| total_payments      |       13.3% |restricted_stock_deferred    |     0.0% |
| deferred_income      |       12.2% |exercised_stock_options    |     0.0% |
| to_messages      |       11.0% |restricted_stock    |     0.0% |
| defer_of_restrict_stock      |       9.1% |loan_advances    |     0.0% |
| total_stock_value      |       7.9% |deferral_payments    |     0.0% |
| shared_receipt_with_poi      |       7.7% |salary    |     0.0% |
| expenses      |       7.6% |from_this_person_to_poi    |     0.0% |
| from_messages      |       5.9% |long_term_incentive    |     0.0% |
| director_fees      |       0.0% |other    |     0.0% |

I used SelectFromModel to get important Features

the features are this

``` python
features_list = ['poi', 'bonus', 'deferred_income', 'to_messages',
                 'defer_of_restrict_stock', 'shared_receipt_with_poi',
                 'expenses', 'exercised_stock_options', 'long_term_incentive']
```
The precision and recall score to use this features_list are below

|| precision   | recall  | f1-score   | support |
|:--|-----------:|------------:|-----------:|------------:|
|nopoi| 0.92       |        0.95 |0.93    |     37 |
|poi| 0.50       |        0.40 |0.44    |     5 |
|avg / total| 0.87       |        0.88 |0.88    |     42 |  

Even though I took off lots of features, the scores are almost same.
And I think the scores are not high to think overfit.


