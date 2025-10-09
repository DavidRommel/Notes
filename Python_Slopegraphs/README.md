# Slopegraphs


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## Create sample data set


```python
df = pd.DataFrame({'category' : ["Peers", "Peers", "Culture", "Culture", "Work Environment", 
                                 "Work Environment", "Leadership", "Leadership", "Career development",
                                 "Career development", "Rewards & recognition", "Rewards & recognition", 
                                 "Perf management", "Perf management"], 
                   'year' : [2014,2015,2014,2015,2014,2015,2014,2015,2014,2015,2014,2015,2014,2015],
                   'percent_favorable' : [85,91,80,96,76,75,59,62,49,33,41,45,33,42]
                  })

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
</div>



## Create the slope graph


```python
df['category'] = df['category'].astype('category')

# Reorder the categories so that the line for 'Career development' is drawn last
df['category'] = df['category'].cat.reorder_categories(['Peers', 'Culture', 'Work Environment', 
                                                        'Leadership', 'Rewards & recognition', 
                                                        'Perf management', 'Career development'], ordered = True)

df_2014 = df[df['year'] == 2014]
df_2015 = df[df['year'] == 2015]

my_palette = ['grey', 'grey', 'grey', 'grey', 'grey', 'grey', 'red'] # the last categorical value with be drawn last

plt.figure(figsize = (8,7))

sns.lineplot(data = df, 
             x = 'year', 
             y = 'percent_favorable', 
             hue = 'category',           # custom palette only uses two colors   
             linewidth = 2,              # make the line thicker
             palette = my_palette,       # use custom palette for emphasis
             legend = None               # hide legend
            )

sns.scatterplot(data = df, 
                x = 'year',  
                y = 'percent_favorable', 
                hue = 'category', 
                s = 50,                  # size of points
                palette = my_palette, 
                legend = None
               )

# Add 2014 category and percentages
for i in range(df_2014.shape[0]):
    plt.text(x = 2013.9, 
             y = df_2014.iloc[i, 2], 
             s = df_2014.iloc[i,0] + ' ' + str(df_2014.iloc[i,2]) + '%', horizontalalignment = 'right', verticalalignment = 'center')

# Add 2015 percentages
for i in range(df_2015.shape[0]):
    plt.text(x = 2015.1, 
             y = df_2015.iloc[i, 2], 
             s = str(df_2015.iloc[i,2]) + '%', horizontalalignment = 'left', verticalalignment = 'center')

# Draw horizontal line connecting dates on x-axis
plt.hlines(y = 30, xmin = 2013.9999, xmax = 2015.005, color = 'black')

# Customize plot
plt.yticks([])                             # remove y-axis tick marks
plt.ylabel("")                             # remove y-axis label
plt.xlabel("")                             # remove x-axis label
plt.xticks((2014,2015))                    # set the ticks to the two years being plotted
plt.xlim(2013,2016)                        # set limit to greater and less than the plotted data to leave room for text
plt.ylim(30,100)                           # percentages: should probably be 0 to 100 to avoid misleading
plt.tick_params(axis = 'x', length = 6)    # make tick size larger
plt.box(False)                             # remove box surrounding plot
plt.title('Employee Feedback Over Time')

plt.show()
```


    
![png](output_5_0.png)
    

