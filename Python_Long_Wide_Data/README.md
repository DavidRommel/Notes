## Long and Wide Data


```python
import numpy as np
import pandas as pd
```

### Wide-format data set
---


```python
df = pd.DataFrame({'team' : ['A', 'B', 'C', 'D'],
                   'points' : [88, 91, 99, 94],
                   'assists' : [12, 17, 24, 28],
                   'rebounds' : [22, 28, 30, 31]})
df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>points</th>
      <th>assists</th>
      <th>rebounds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>88</td>
      <td>12</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>91</td>
      <td>17</td>
      <td>28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>99</td>
      <td>24</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>94</td>
      <td>28</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


### Convert Data From Wide To Long Format
---


```python
df_long = pd.melt(frame = df, 
                  id_vars = 'team', # Column(s) to use as identifier variables
                  value_vars = ['points', 'assists', 'rebounds'], # Column(s) to unpivot
                  var_name = 'metric', # Name to use for the ‘variable’ column
                  value_name = 'amount', # Name to use for the ‘value’ column 
                  ignore_index = True # If True, original index is ignored. If False, the original index is retained
                 )
df_long
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>metric</th>
      <th>amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>points</td>
      <td>88</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>points</td>
      <td>91</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>points</td>
      <td>99</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>points</td>
      <td>94</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A</td>
      <td>assists</td>
      <td>12</td>
    </tr>
    <tr>
      <th>5</th>
      <td>B</td>
      <td>assists</td>
      <td>17</td>
    </tr>
    <tr>
      <th>6</th>
      <td>C</td>
      <td>assists</td>
      <td>24</td>
    </tr>
    <tr>
      <th>7</th>
      <td>D</td>
      <td>assists</td>
      <td>28</td>
    </tr>
    <tr>
      <th>8</th>
      <td>A</td>
      <td>rebounds</td>
      <td>22</td>
    </tr>
    <tr>
      <th>9</th>
      <td>B</td>
      <td>rebounds</td>
      <td>28</td>
    </tr>
    <tr>
      <th>10</th>
      <td>C</td>
      <td>rebounds</td>
      <td>30</td>
    </tr>
    <tr>
      <th>11</th>
      <td>D</td>
      <td>rebounds</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


### Convert Data from Long to Wide Format
---


```python
df_wide = pd.pivot(data = df_long, 
                   columns = 'metric', # Column to use to make new frame’s columns 
                   index = 'team', # Column to use to make new frame’s index
                   values = 'amount' # Column(s) to use for populating new frame’s values
                  ) 
df_wide
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>metric</th>
      <th>assists</th>
      <th>points</th>
      <th>rebounds</th>
    </tr>
    <tr>
      <th>team</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>12</td>
      <td>88</td>
      <td>22</td>
    </tr>
    <tr>
      <th>B</th>
      <td>17</td>
      <td>91</td>
      <td>28</td>
    </tr>
    <tr>
      <th>C</th>
      <td>24</td>
      <td>99</td>
      <td>30</td>
    </tr>
    <tr>
      <th>D</th>
      <td>28</td>
      <td>94</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


```python
df_wide.reset_index(inplace = True)
df_wide
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>metric</th>
      <th>team</th>
      <th>assists</th>
      <th>points</th>
      <th>rebounds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>12</td>
      <td>88</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>17</td>
      <td>91</td>
      <td>28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>24</td>
      <td>99</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>28</td>
      <td>94</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


```python
df_wide.rename_axis(None, axis = 1, inplace = True) # remove axis name
df_wide
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>assists</th>
      <th>points</th>
      <th>rebounds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>12</td>
      <td>88</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>17</td>
      <td>91</td>
      <td>28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>24</td>
      <td>99</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>28</td>
      <td>94</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


```python
df_wide[['team', 'points', 'assists', 'rebounds']] # reorder columns to original order
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>team</th>
      <th>points</th>
      <th>assists</th>
      <th>rebounds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>88</td>
      <td>12</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>91</td>
      <td>17</td>
      <td>28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>99</td>
      <td>24</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>94</td>
      <td>28</td>
      <td>31</td>
    </tr>
  </tbody>
</table>


### Alternate method to pivot tables
---
* Supports additional features, such as being able to aggregate data
* [Documentation](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pivot_table.html#pandas.DataFrame.pivot_table)


```python
pd.pivot_table(data = df_long, 
               values = 'amount', 
               index = 'team', 
               columns = 'metric')
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>metric</th>
      <th>assists</th>
      <th>points</th>
      <th>rebounds</th>
    </tr>
    <tr>
      <th>team</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>12.0</td>
      <td>88.0</td>
      <td>22.0</td>
    </tr>
    <tr>
      <th>B</th>
      <td>17.0</td>
      <td>91.0</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>C</th>
      <td>24.0</td>
      <td>99.0</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>D</th>
      <td>28.0</td>
      <td>94.0</td>
      <td>31.0</td>
    </tr>
  </tbody>
</table>


### Survey data example
---
* The aggregated data would likely be in a wide format similar to below
* Convert from wide to long format in order to create a slope graph


```python
df1 = pd.DataFrame({'category' : ['Peers', 'Culture', 'Work Environment', 'Leadership', 
                                  'Career development', 'Rewards & recognition', 'Perf management'],
                   'year_2014' : [85, 80, 76, 59, 49, 41, 33],
                   'year_2015' : [91, 96, 75, 62, 33, 45, 42]})
df1
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>year_2014</th>
      <th>year_2015</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peers</td>
      <td>85</td>
      <td>91</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Culture</td>
      <td>80</td>
      <td>96</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Work Environment</td>
      <td>76</td>
      <td>75</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Leadership</td>
      <td>59</td>
      <td>62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Career development</td>
      <td>49</td>
      <td>33</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Rewards &amp; recognition</td>
      <td>41</td>
      <td>45</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Perf management</td>
      <td>33</td>
      <td>42</td>
    </tr>
  </tbody>
</table>


```python
df1_long = pd.melt(frame = df1, 
                   id_vars = 'category', 
                   value_vars = ['year_2014', 'year_2015'], 
                   var_name = 'year', 
                   value_name = 'percent_favorable')
```


```python
df1_long['year'] = df1_long['year'].str.strip('year_').astype('int') # remove the year_ prefix from the years
df1_long
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>year</th>
      <th>percent_favorable</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peers</td>
      <td>2014</td>
      <td>85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Culture</td>
      <td>2014</td>
      <td>80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Work Environment</td>
      <td>2014</td>
      <td>76</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Leadership</td>
      <td>2014</td>
      <td>59</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Career development</td>
      <td>2014</td>
      <td>49</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Rewards &amp; recognition</td>
      <td>2014</td>
      <td>41</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Perf management</td>
      <td>2014</td>
      <td>33</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Peers</td>
      <td>2015</td>
      <td>91</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Culture</td>
      <td>2015</td>
      <td>96</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Work Environment</td>
      <td>2015</td>
      <td>75</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Leadership</td>
      <td>2015</td>
      <td>62</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Career development</td>
      <td>2015</td>
      <td>33</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Rewards &amp; recognition</td>
      <td>2015</td>
      <td>45</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Perf management</td>
      <td>2015</td>
      <td>42</td>
    </tr>
  </tbody>
</table>


### Rearrange the rows
---


```python
# Create a list with the category for each year on successive rows 
# 0,7,1,8,2,9,3,10,4,11,5,12,6,13
index_list = []
for i in range(7): # 0 - 6
    index_list.append(i)
    index_list.append(i+7)
```


```python
df1_long.reindex(index = index_list).reset_index(drop = True) # reindex() can reorder columns as well as rows
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>year</th>
      <th>percent_favorable</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peers</td>
      <td>2014</td>
      <td>85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Peers</td>
      <td>2015</td>
      <td>91</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Culture</td>
      <td>2014</td>
      <td>80</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Culture</td>
      <td>2015</td>
      <td>96</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Work Environment</td>
      <td>2014</td>
      <td>76</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Work Environment</td>
      <td>2015</td>
      <td>75</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Leadership</td>
      <td>2014</td>
      <td>59</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Leadership</td>
      <td>2015</td>
      <td>62</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Career development</td>
      <td>2014</td>
      <td>49</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Career development</td>
      <td>2015</td>
      <td>33</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rewards &amp; recognition</td>
      <td>2014</td>
      <td>41</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rewards &amp; recognition</td>
      <td>2015</td>
      <td>45</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Perf management</td>
      <td>2014</td>
      <td>33</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Perf management</td>
      <td>2015</td>
      <td>42</td>
    </tr>
  </tbody>
</table>
