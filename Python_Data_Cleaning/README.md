# Data Cleaning

This repository serves as a quick-reference guide for Python data analysis concepts, focusing on data exploration, datetime manipulation, data cleaning, and outlier detection. All examples utilize standard datasets from the Seaborn library.

## Table of Contents
* [Environment Setup](#environment-setup)
* [Data Exploration & Inspection](#data-exploration--inspection)
    * [View first 10 rows](#view-first-10-rows)
    * [Data types and non-null counts](#data-types-and-non-null-counts)
    * [Summary statistics for numerical columns](#summary-statistics-for-numerical-columns)
    * [Display rows and columns](#display-rows-and-columns)
    * [List of all column names](#list-of-all-column-names)
* [Datetime Manipulation](#datetime-manipulation)
    * [Non-pandas conversion](#non-pandas-conversion)
    * [Pandas conversion](#pandas-conversion)
    * [Extracting components](#extracting-components)
    * [Formatting date as a string](#formatting-date-as-a-string)
* [Joining & Aggregating Data](#joining--aggregating-data)
    * [Merging DataFrames (Left Join)](#merging-dataframes-left-join)
    * [Concatenating side-by-side](#concatenating-side-by-side)
    * [Aggregation](#aggregation)
        * [Count occurrences](#count-occurrences)
        * [Total bill per day](#total-bill-per-day)
* [Data Cleaning: Duplicates & Missing Values](#data-cleaning-duplicates--missing-values)
    * [Identify duplicate rows](#identify-duplicate-rows)
    * [Keep only the last occurrence per species](#keep-only-the-last-occurrence-per-species)
    * [Count missing values per column](#count-missing-values-per-column)
    * [Show rows with any missing values](#show-rows-with-any-missing-values)
    * [Remove rows with missing values](#remove-rows-with-missing-values)
    * [Fill with specific value](#fill-with-specific-value)
    * [Forward fill from previous row](#forward-fill-from-previous-row)
    * [Backward fill from next row](#backward-fill-from-next-row)
* [Data Transformation & Filtering](#data-transformation--filtering)
    * [Custom transformation using apply()](#custom-transformation-using-apply)
    * [Define a custom function for removing the $ and B characters](#define-a-custom-function-for-removing-the--and-b-characters)
    * [Filtering with isin()](#filtering-with-isin)
        * [Using isin() to check for misspelled month names](#using-isin-to-check-for-misspelled-month-names)
        * [Using sets to identify misspelled month names](#using-sets-to-identify-misspelled-month-names)
    * [Type conversion](#type-conversion)
* [Outlier Analysis & Handling](#outlier-analysis--handling)
    * [IQR Detection Method](#iqr-detection-method)
    * [The clip() Method](#the-clip-method)

---

## Environment Setup

Standard libraries used for data manipulation, statistical analysis, and visualization.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

---

## Data Exploration & Inspection

Initial steps to understand the structure and summary statistics of a dataset using the `penguins` dataset.


```python
# Load Seaborn dataset
penguins = sns.load_dataset('penguins')
```

### View first 10 rows


```python
penguins.head(10)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.1</td>
      <td>18.7</td>
      <td>181.0</td>
      <td>3750.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.5</td>
      <td>17.4</td>
      <td>186.0</td>
      <td>3800.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>40.3</td>
      <td>18.0</td>
      <td>195.0</td>
      <td>3250.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>36.7</td>
      <td>19.3</td>
      <td>193.0</td>
      <td>3450.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.3</td>
      <td>20.6</td>
      <td>190.0</td>
      <td>3650.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>38.9</td>
      <td>17.8</td>
      <td>181.0</td>
      <td>3625.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.2</td>
      <td>19.6</td>
      <td>195.0</td>
      <td>4675.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>34.1</td>
      <td>18.1</td>
      <td>193.0</td>
      <td>3475.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>42.0</td>
      <td>20.2</td>
      <td>190.0</td>
      <td>4250.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>




### Data types and non-null counts


```python
penguins.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 344 entries, 0 to 343
    Data columns (total 7 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   species            344 non-null    object 
     1   island             344 non-null    object 
     2   bill_length_mm     342 non-null    float64
     3   bill_depth_mm      342 non-null    float64
     4   flipper_length_mm  342 non-null    float64
     5   body_mass_g        342 non-null    float64
     6   sex                333 non-null    object 
    dtypes: float64(4), object(3)
    memory usage: 18.9+ KB


### Summary statistics for numerical columns


```python
penguins.describe()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>342.000000</td>
      <td>342.000000</td>
      <td>342.000000</td>
      <td>342.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>43.921930</td>
      <td>17.151170</td>
      <td>200.915205</td>
      <td>4201.754386</td>
    </tr>
    <tr>
      <th>std</th>
      <td>5.459584</td>
      <td>1.974793</td>
      <td>14.061714</td>
      <td>801.954536</td>
    </tr>
    <tr>
      <th>min</th>
      <td>32.100000</td>
      <td>13.100000</td>
      <td>172.000000</td>
      <td>2700.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>39.225000</td>
      <td>15.600000</td>
      <td>190.000000</td>
      <td>3550.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>44.450000</td>
      <td>17.300000</td>
      <td>197.000000</td>
      <td>4050.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>48.500000</td>
      <td>18.700000</td>
      <td>213.000000</td>
      <td>4750.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>59.600000</td>
      <td>21.500000</td>
      <td>231.000000</td>
      <td>6300.000000</td>
    </tr>
  </tbody>
</table>




### Display rows and columns


```python
penguins.shape
```




    (344, 7)



### List of all column names


```python
penguins.columns
```




    Index(['species', 'island', 'bill_length_mm', 'bill_depth_mm',
           'flipper_length_mm', 'body_mass_g', 'sex'],
          dtype='object')



---

## Datetime Manipulation

Converting strings to datetime objects and extracting time-based components using the `flights` dataset.

### Non-pandas conversion


```python
from datetime import datetime
dt_obj = datetime.strptime('September 4, 2025', '%B %d, %Y')
```

### Pandas conversion


```python
df = sns.load_dataset('flights')

# Create a date string and convert
df['date'] = pd.to_datetime(df['year'].astype(str) + '-' + df['month'].astype(str) + '-01')
```


```python
# to_datetime will recognize this non-standard date syntax
(df['year'].astype(str) + '-' + df['month'].astype(str) + '-01')[:5]
```




    0    1949-Jan-01
    1    1949-Feb-01
    2    1949-Mar-01
    3    1949-Apr-01
    4    1949-May-01
    dtype: object



### Extracting components


```python
df['year_val'] = df['date'].dt.year
df['month_name'] = df['date'].dt.month_name()
df['quarter'] = df['date'].dt.to_period('Q').dt.strftime('Q%q')
df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>month</th>
      <th>passengers</th>
      <th>date</th>
      <th>year_val</th>
      <th>month_name</th>
      <th>quarter</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1949</td>
      <td>Jan</td>
      <td>112</td>
      <td>1949-01-01</td>
      <td>1949</td>
      <td>January</td>
      <td>Q1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1949</td>
      <td>Feb</td>
      <td>118</td>
      <td>1949-02-01</td>
      <td>1949</td>
      <td>February</td>
      <td>Q1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1949</td>
      <td>Mar</td>
      <td>132</td>
      <td>1949-03-01</td>
      <td>1949</td>
      <td>March</td>
      <td>Q1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1949</td>
      <td>Apr</td>
      <td>129</td>
      <td>1949-04-01</td>
      <td>1949</td>
      <td>April</td>
      <td>Q2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1949</td>
      <td>May</td>
      <td>121</td>
      <td>1949-05-01</td>
      <td>1949</td>
      <td>May</td>
      <td>Q2</td>
    </tr>
  </tbody>
</table>




### Formatting date as a string


```python
formatted_date = df['date'].dt.strftime('%Y-%m-%d')
```

---

## Joining & Aggregating Data

Combining DataFrames and summarizing categorical or numerical data using `tips` and `penguins`.


```python
island_lookup = pd.DataFrame({'island': ['Torgersen', 'Biscoe', 'Dream'], 'region': ['Region A', 'Region B', 'Region C']})
island_lookup.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>island</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Torgersen</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Biscoe</td>
      <td>Region B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dream</td>
      <td>Region C</td>
    </tr>
  </tbody>
</table>




### Merging DataFrames (Left Join)


```python
penguins_merged = penguins.merge(island_lookup, how='left', on='island')
penguins_merged.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.1</td>
      <td>18.7</td>
      <td>181.0</td>
      <td>3750.0</td>
      <td>Male</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.5</td>
      <td>17.4</td>
      <td>186.0</td>
      <td>3800.0</td>
      <td>Female</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>40.3</td>
      <td>18.0</td>
      <td>195.0</td>
      <td>3250.0</td>
      <td>Female</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>36.7</td>
      <td>19.3</td>
      <td>193.0</td>
      <td>3450.0</td>
      <td>Female</td>
      <td>Region A</td>
    </tr>
  </tbody>
</table>




### Concatenating side-by-side


```python
df_concat = pd.concat([penguins.head(), island_lookup.head()], axis=1)
df_concat.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
      <th>island</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.1</td>
      <td>18.7</td>
      <td>181.0</td>
      <td>3750.0</td>
      <td>Male</td>
      <td>Torgersen</td>
      <td>Region A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.5</td>
      <td>17.4</td>
      <td>186.0</td>
      <td>3800.0</td>
      <td>Female</td>
      <td>Biscoe</td>
      <td>Region B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>40.3</td>
      <td>18.0</td>
      <td>195.0</td>
      <td>3250.0</td>
      <td>Female</td>
      <td>Dream</td>
      <td>Region C</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>36.7</td>
      <td>19.3</td>
      <td>193.0</td>
      <td>3450.0</td>
      <td>Female</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>




### Aggregation


```python
tips = sns.load_dataset('tips')
```

#### Count occurrences


```python
tips['day'].value_counts()
```




    day
    Sat     87
    Sun     76
    Thur    62
    Fri     19
    Name: count, dtype: int64



#### Total bill per day


```python
tips.groupby(by = ['day'], observed = True)['total_bill'].sum()
```




    day
    Thur    1096.33
    Fri      325.88
    Sat     1778.40
    Sun     1627.16
    Name: total_bill, dtype: float64



---

## Data Cleaning: Duplicates & Missing Values

Identifying and handling redundant or null entries within the `penguins` dataset.

### Identify duplicate rows


```python
penguins[penguins.duplicated()]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>




### Keep only the last occurrence per species


```python
penguins.drop_duplicates(subset=['species'], keep='last')
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>151</th>
      <td>Adelie</td>
      <td>Dream</td>
      <td>41.5</td>
      <td>18.5</td>
      <td>201.0</td>
      <td>4000.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>219</th>
      <td>Chinstrap</td>
      <td>Dream</td>
      <td>50.2</td>
      <td>18.7</td>
      <td>198.0</td>
      <td>3775.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>343</th>
      <td>Gentoo</td>
      <td>Biscoe</td>
      <td>49.9</td>
      <td>16.1</td>
      <td>213.0</td>
      <td>5400.0</td>
      <td>Male</td>
    </tr>
  </tbody>
</table>




### Count missing values per column


```python
penguins.isna().sum()
```




    species               0
    island                0
    bill_length_mm        2
    bill_depth_mm         2
    flipper_length_mm     2
    body_mass_g           2
    sex                  11
    dtype: int64



### Show rows with any missing values


```python
penguins[penguins.isna().any(axis=1)][:5]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>34.1</td>
      <td>18.1</td>
      <td>193.0</td>
      <td>3475.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>42.0</td>
      <td>20.2</td>
      <td>190.0</td>
      <td>4250.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>37.8</td>
      <td>17.1</td>
      <td>186.0</td>
      <td>3300.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>37.8</td>
      <td>17.3</td>
      <td>180.0</td>
      <td>3700.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>




### Remove rows with missing values


```python
penguins.dropna(inplace=True)
```

### Fill with specific value


```python
penguins['sex'] = penguins['sex'].fillna('Unknown')
```

### Forward fill from previous row


```python
penguins.ffill()[:5]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.1</td>
      <td>18.7</td>
      <td>181.0</td>
      <td>3750.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.5</td>
      <td>17.4</td>
      <td>186.0</td>
      <td>3800.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>40.3</td>
      <td>18.0</td>
      <td>195.0</td>
      <td>3250.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>36.7</td>
      <td>19.3</td>
      <td>193.0</td>
      <td>3450.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.3</td>
      <td>20.6</td>
      <td>190.0</td>
      <td>3650.0</td>
      <td>Male</td>
    </tr>
  </tbody>
</table>




### Backward fill from next row


```python
penguins.bfill()[:5]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>species</th>
      <th>island</th>
      <th>bill_length_mm</th>
      <th>bill_depth_mm</th>
      <th>flipper_length_mm</th>
      <th>body_mass_g</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.1</td>
      <td>18.7</td>
      <td>181.0</td>
      <td>3750.0</td>
      <td>Male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.5</td>
      <td>17.4</td>
      <td>186.0</td>
      <td>3800.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>40.3</td>
      <td>18.0</td>
      <td>195.0</td>
      <td>3250.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>36.7</td>
      <td>19.3</td>
      <td>193.0</td>
      <td>3450.0</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Adelie</td>
      <td>Torgersen</td>
      <td>39.3</td>
      <td>20.6</td>
      <td>190.0</td>
      <td>3650.0</td>
      <td>Male</td>
    </tr>
  </tbody>
</table>




---

## Data Transformation & Filtering

Applying custom logic, membership filtering, and type conversion.

### Custom transformation using `apply()`


```python
tips['total_bill'] = '$' + tips['total_bill'].astype(str) + 'B'
tips.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_bill</th>
      <th>tip</th>
      <th>sex</th>
      <th>smoker</th>
      <th>day</th>
      <th>time</th>
      <th>size</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$16.99B</td>
      <td>1.01</td>
      <td>Female</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>$10.34B</td>
      <td>1.66</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>$21.01B</td>
      <td>3.50</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>$23.68B</td>
      <td>3.31</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>$24.59B</td>
      <td>3.61</td>
      <td>Female</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>4</td>
    </tr>
  </tbody>
</table>




### Define a custom function for removing the $ and B characters


```python
def clean_val(val):
    if isinstance(val, str):
        return_value = float(val.strip('$B'))
    else:
        return_value = val
    return return_value
```


```python
tips['total_bill'].apply(clean_val)[:5]
```




    0    16.99
    1    10.34
    2    21.01
    3    23.68
    4    24.59
    Name: total_bill, dtype: float64



### Filtering with `isin()`


```python
islands_to_check = ['Torgersen', 'Dream']
filtered_penguins = penguins[penguins['island'].isin(islands_to_check)]

# Negation
excluded_penguins = penguins[~penguins['island'].isin(islands_to_check)]
```

#### Using `isin()` to check for mispelled month names


```python
date_list = ['January', 'Febuary', 'March', 'Apryl', 'May', 
             'Junne', 'July', 'August', 'Septembir', 'October']

date_df = pd.DataFrame({'date' : date_list})
                       
month_spellings = ['January', 'February', 'March', 'April', 'May', 'June', 
                   'July', 'August', 'September', 'October', 'November', 'December']

date_df[~date_df['date'].isin(month_spellings)]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Febuary</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Apryl</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Junne</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Septembir</td>
    </tr>
  </tbody>
</table>




#### Using sets to identify mispelled month names


```python
set(date_list) - set(month_spellings)
```




    {'Apryl', 'Febuary', 'Junne', 'Septembir'}



### Type conversion


```python
penguins['species'] = penguins['species'].astype('category')
```

---

## Outlier Analysis & Handling

Methods for detecting and managing statistical outliers using the `diamonds` dataset.

### IQR Detection Method

Using the Interquartile Range (IQR) to identify values outside the typical distribution.


```python
diamonds = sns.load_dataset('diamonds')

# IQR Calculation
q1 = diamonds['price'].quantile(0.25)
q3 = diamonds['price'].quantile(0.75)
iqr = q3 - q1

# Define bounds
lower_limit = q1 - (1.5 * iqr)
upper_limit = q3 + (1.5 * iqr)

# Identify Outliers
diamonds[(diamonds['price'] < lower_limit) | (diamonds['price'] > upper_limit)]
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>carat</th>
      <th>cut</th>
      <th>color</th>
      <th>clarity</th>
      <th>depth</th>
      <th>table</th>
      <th>price</th>
      <th>x</th>
      <th>y</th>
      <th>z</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>23820</th>
      <td>1.17</td>
      <td>Ideal</td>
      <td>F</td>
      <td>VVS1</td>
      <td>62.1</td>
      <td>57.0</td>
      <td>11886</td>
      <td>6.82</td>
      <td>6.73</td>
      <td>4.21</td>
    </tr>
    <tr>
      <th>23821</th>
      <td>2.08</td>
      <td>Ideal</td>
      <td>I</td>
      <td>SI2</td>
      <td>62.0</td>
      <td>56.0</td>
      <td>11886</td>
      <td>8.21</td>
      <td>8.10</td>
      <td>5.06</td>
    </tr>
    <tr>
      <th>23822</th>
      <td>1.70</td>
      <td>Premium</td>
      <td>I</td>
      <td>VS2</td>
      <td>62.2</td>
      <td>58.0</td>
      <td>11888</td>
      <td>7.65</td>
      <td>7.60</td>
      <td>4.74</td>
    </tr>
    <tr>
      <th>23823</th>
      <td>1.09</td>
      <td>Ideal</td>
      <td>F</td>
      <td>IF</td>
      <td>61.6</td>
      <td>55.0</td>
      <td>11888</td>
      <td>6.59</td>
      <td>6.65</td>
      <td>4.08</td>
    </tr>
    <tr>
      <th>23824</th>
      <td>1.68</td>
      <td>Ideal</td>
      <td>E</td>
      <td>SI2</td>
      <td>60.4</td>
      <td>55.0</td>
      <td>11888</td>
      <td>7.79</td>
      <td>7.70</td>
      <td>4.68</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>27745</th>
      <td>2.00</td>
      <td>Very Good</td>
      <td>H</td>
      <td>SI1</td>
      <td>62.8</td>
      <td>57.0</td>
      <td>18803</td>
      <td>7.95</td>
      <td>8.00</td>
      <td>5.01</td>
    </tr>
    <tr>
      <th>27746</th>
      <td>2.07</td>
      <td>Ideal</td>
      <td>G</td>
      <td>SI2</td>
      <td>62.5</td>
      <td>55.0</td>
      <td>18804</td>
      <td>8.20</td>
      <td>8.13</td>
      <td>5.11</td>
    </tr>
    <tr>
      <th>27747</th>
      <td>1.51</td>
      <td>Ideal</td>
      <td>G</td>
      <td>IF</td>
      <td>61.7</td>
      <td>55.0</td>
      <td>18806</td>
      <td>7.37</td>
      <td>7.41</td>
      <td>4.56</td>
    </tr>
    <tr>
      <th>27748</th>
      <td>2.00</td>
      <td>Very Good</td>
      <td>G</td>
      <td>SI1</td>
      <td>63.5</td>
      <td>56.0</td>
      <td>18818</td>
      <td>7.90</td>
      <td>7.97</td>
      <td>5.04</td>
    </tr>
    <tr>
      <th>27749</th>
      <td>2.29</td>
      <td>Premium</td>
      <td>I</td>
      <td>VS2</td>
      <td>60.8</td>
      <td>60.0</td>
      <td>18823</td>
      <td>8.50</td>
      <td>8.47</td>
      <td>5.16</td>
    </tr>
  </tbody>
</table>
<p>3540 rows × 10 columns</p>




### The `clip()` Method

Instead of removing outliers, the `clip()` method caps them at the threshold values, preserving the data point while minimizing its impact.


```python
# Any value above upper_limit becomes upper_limit; any below lower_limit becomes lower_limit
diamonds['price_clipped'] = diamonds['price'].clip(lower=lower_limit, upper=upper_limit)

# Verify the result
print(diamonds['price_clipped'].max() <= upper_limit)
print(diamonds['price_clipped'].min() >= lower_limit)
```

    True
    True
