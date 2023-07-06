---
title: U.S. Cities (Data Exploration)
date: 2023-07-06 10:20:00
layout: post
categories: [social, census] 
tags: [python,eda,data analysis,usa,census,population ]
---

## Introduction
This is the second part of the U.S. Cities project. [In the first part](https://denislsad.github.io/posts/us-cities-part-one/), I created a dataset of metropolitan areas in the United States (called "cities" for simplicity). The dataset contains 14 variables. I will explore the cities data through visualization and testing, and draw some conclusions.

## Loading Data

```python
# Import libraries used in this project
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
import seaborn as sns
import plotly.express as px
import scikit_posthocs as sp
```


```python
# Load the US Cities dataset
cities = pd.read_csv('us_cities.csv')
cities.head(10)
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
      <th>City</th>
      <th>State</th>
      <th>Region</th>
      <th>Size</th>
      <th>Population</th>
      <th>AvgRent</th>
      <th>MedianRent</th>
      <th>UnempRate</th>
      <th>AvgIncome</th>
      <th>CostOfLiving</th>
      <th>PriceParity</th>
      <th>CommuteTime</th>
      <th>MedianAQI</th>
      <th>WalkScore</th>
      <th>BikeScore</th>
      <th>TransitScore</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>New York</td>
      <td>New York</td>
      <td>Northeast</td>
      <td>Large</td>
      <td>20140470.0</td>
      <td>3272</td>
      <td>2323.0</td>
      <td>3.8</td>
      <td>85136.0</td>
      <td>128.0</td>
      <td>114.58</td>
      <td>36.7</td>
      <td>50.0</td>
      <td>88.0</td>
      <td>69.3</td>
      <td>6.9</td>
      <td>40.6943</td>
      <td>-73.9249</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Los Angeles</td>
      <td>California</td>
      <td>West</td>
      <td>Large</td>
      <td>13200998.0</td>
      <td>2857</td>
      <td>1925.0</td>
      <td>3.9</td>
      <td>75821.0</td>
      <td>140.6</td>
      <td>113.82</td>
      <td>30.7</td>
      <td>70.0</td>
      <td>68.6</td>
      <td>58.7</td>
      <td>6.2</td>
      <td>34.1141</td>
      <td>-118.4068</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Chicago</td>
      <td>Illinois</td>
      <td>Midwest</td>
      <td>Large</td>
      <td>9618502.0</td>
      <td>1975</td>
      <td>1364.0</td>
      <td>4.2</td>
      <td>71992.0</td>
      <td>100.1</td>
      <td>105.42</td>
      <td>31.8</td>
      <td>50.0</td>
      <td>77.2</td>
      <td>72.2</td>
      <td>5.1</td>
      <td>41.8375</td>
      <td>-87.6866</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Dallas</td>
      <td>Texas</td>
      <td>South</td>
      <td>Large</td>
      <td>7637387.0</td>
      <td>1754</td>
      <td>1440.0</td>
      <td>3.2</td>
      <td>66727.0</td>
      <td>98.5</td>
      <td>103.85</td>
      <td>28.6</td>
      <td>51.0</td>
      <td>46.0</td>
      <td>49.3</td>
      <td>2.8</td>
      <td>32.7935</td>
      <td>-96.7667</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Houston</td>
      <td>Texas</td>
      <td>South</td>
      <td>Large</td>
      <td>7122240.0</td>
      <td>1620</td>
      <td>1216.0</td>
      <td>3.9</td>
      <td>64837.0</td>
      <td>95.8</td>
      <td>99.74</td>
      <td>30.0</td>
      <td>57.0</td>
      <td>47.5</td>
      <td>48.6</td>
      <td>2.8</td>
      <td>29.7860</td>
      <td>-95.3885</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Washington</td>
      <td>District of Columbia</td>
      <td>South</td>
      <td>Large</td>
      <td>6385162.0</td>
      <td>2421</td>
      <td>1740.0</td>
      <td>2.8</td>
      <td>80822.0</td>
      <td>120.1</td>
      <td>111.34</td>
      <td>34.8</td>
      <td>45.0</td>
      <td>76.7</td>
      <td>69.5</td>
      <td>5.5</td>
      <td>38.9047</td>
      <td>-77.0163</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Philadelphia</td>
      <td>Pennsylvania</td>
      <td>Northeast</td>
      <td>Large</td>
      <td>6245051.0</td>
      <td>1663</td>
      <td>1314.0</td>
      <td>3.4</td>
      <td>72379.0</td>
      <td>103.4</td>
      <td>99.21</td>
      <td>29.8</td>
      <td>48.0</td>
      <td>74.8</td>
      <td>66.7</td>
      <td>5.3</td>
      <td>40.0077</td>
      <td>-75.1339</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Miami</td>
      <td>Florida</td>
      <td>South</td>
      <td>Large</td>
      <td>6138333.0</td>
      <td>3201</td>
      <td>1670.0</td>
      <td>1.9</td>
      <td>73522.0</td>
      <td>110.1</td>
      <td>109.92</td>
      <td>29.6</td>
      <td>45.0</td>
      <td>76.6</td>
      <td>64.0</td>
      <td>5.2</td>
      <td>25.7840</td>
      <td>-80.2101</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Atlanta</td>
      <td>Georgia</td>
      <td>South</td>
      <td>Large</td>
      <td>6089815.0</td>
      <td>1991</td>
      <td>1489.0</td>
      <td>2.6</td>
      <td>63219.0</td>
      <td>100.3</td>
      <td>99.12</td>
      <td>32.1</td>
      <td>48.0</td>
      <td>47.7</td>
      <td>41.7</td>
      <td>2.5</td>
      <td>33.7628</td>
      <td>-84.4220</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Boston</td>
      <td>Massachusetts</td>
      <td>Northeast</td>
      <td>Large</td>
      <td>4941632.0</td>
      <td>3419</td>
      <td>2368.0</td>
      <td>2.9</td>
      <td>92290.0</td>
      <td>132.6</td>
      <td>109.69</td>
      <td>31.2</td>
      <td>44.0</td>
      <td>82.8</td>
      <td>69.4</td>
      <td>5.0</td>
      <td>42.3188</td>
      <td>-71.0852</td>
    </tr>
  </tbody>
</table>
</div>

Now we have all variables of the same data type and all rows with at least half of the values not missing. For the rest of this project, I will cover only a fraction of what can be done, as the possibilities for visualizing this data are endless. I will focus on some of the variables that I found most interesting.

## Map
Display our final data on the map with Plotly Express. Note


```python
fig = px.scatter_geo(cities,
                    lat='Latitude',
                    lon='Longitude',
                    size='Population',
                    color='Size',
                    hover_data=cities.columns[:13],
                    size_max=30,
                    width=1000,
                    height=600)


fig.update_layout(
    title_text = 'US Cities',
    geo_scope='usa',
)
fig.show()
```
![Map](/assets/leb/cities0.png)


## All Variables Heat Map
First, look at the correlation between all variables in the data set with a heat map.


```python
# Full names of variables for better readability
columns_full_names = ['Population', 'Average Rent', 'Median Rent', 'Unemployment Rate', 'Average Income', 
                      'Cost of Living', 'Price Parity', 'Commute Time', 'Median AQI', 'Walk Score', 'Bike Score', 'Transit Score']

# Set theme for all the future graphs
sns.set_theme(style="white", rc={'figure.figsize':(9,6)})

sns.heatmap(cities.corr(), cmap="coolwarm", annot=True, vmin=-1, vmax=1, annot_kws={"size": 9}, xticklabels=columns_full_names, yticklabels=columns_full_names)
plt.show()
```

![Heatmap](/assets/leb/cities1.png)

    


From this map, we can see that:

* There are a number of fairly obvious observations. High cost of living is accompanied by high rental prices and high incomes (and vice versa). It makes sense since rental prices contribute a lot to cost of living. Also, cities with good walkability are good at public transportation and bike construction.
* Another obvious observation is that cities with large populations are, on average, more expensive, have higher incomes, and have worse air quality.
* A correlation between air quality (<i>note that this is the only metric where the higher means the worse</i>) and commute time can indicate either that air quality is affected by traffic congestion and transportation emissions, or that both parameters are caused by high population.
* The unemployment rate has a near-zero correlation with all other variables. However, the only significant correlation is with average income, meaning that higher unemployment rates tend to be associated with lower average incomes.
* The median rent has a stronger correlation with other factors than the average rent, meaning it could be a more important factor in determining a city's livability.

## Summary Statistics by Size

Use the df.describe() method to get summary statistics about the DataFrame. But to better understand the situation, it's better to divide our table into three by metropolitan area size.


```python
# Summary statistics for large cities
large_cities = cities.loc[cities.Size == 'Large']
large_cities.describe()
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
      <th>Population</th>
      <th>AvgRent</th>
      <th>MedianRent</th>
      <th>UnempRate</th>
      <th>AvgIncome</th>
      <th>CostOfLiving</th>
      <th>PriceParity</th>
      <th>CommuteTime</th>
      <th>MedianAQI</th>
      <th>WalkScore</th>
      <th>BikeScore</th>
      <th>TransitScore</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>5.600000e+01</td>
      <td>56.000000</td>
      <td>56.000000</td>
      <td>55.000000</td>
      <td>56.00000</td>
      <td>55.000000</td>
      <td>56.000000</td>
      <td>56.000000</td>
      <td>56.000000</td>
      <td>51.000000</td>
      <td>51.000000</td>
      <td>56.000000</td>
      <td>56.000000</td>
      <td>56.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>3.377466e+06</td>
      <td>1833.964286</td>
      <td>1376.803571</td>
      <td>3.040000</td>
      <td>67278.37500</td>
      <td>105.452727</td>
      <td>100.760893</td>
      <td>26.883929</td>
      <td>48.321429</td>
      <td>52.929412</td>
      <td>54.570588</td>
      <td>3.801786</td>
      <td>36.992770</td>
      <td>-93.794825</td>
    </tr>
    <tr>
      <th>std</th>
      <td>3.296962e+06</td>
      <td>608.939554</td>
      <td>467.829472</td>
      <td>0.783771</td>
      <td>15070.67341</td>
      <td>18.644450</td>
      <td>7.328437</td>
      <td>3.494764</td>
      <td>9.309052</td>
      <td>16.642335</td>
      <td>12.757355</td>
      <td>1.514475</td>
      <td>5.281255</td>
      <td>18.151173</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.008654e+06</td>
      <td>1130.000000</td>
      <td>830.000000</td>
      <td>1.900000</td>
      <td>50384.00000</td>
      <td>86.700000</td>
      <td>90.620000</td>
      <td>21.200000</td>
      <td>29.000000</td>
      <td>25.600000</td>
      <td>30.700000</td>
      <td>0.100000</td>
      <td>21.329400</td>
      <td>-157.846000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.394931e+06</td>
      <td>1382.000000</td>
      <td>1020.500000</td>
      <td>2.550000</td>
      <td>58955.25000</td>
      <td>93.450000</td>
      <td>95.435000</td>
      <td>24.475000</td>
      <td>44.000000</td>
      <td>41.150000</td>
      <td>43.900000</td>
      <td>2.600000</td>
      <td>33.715150</td>
      <td>-106.376525</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.274416e+06</td>
      <td>1717.000000</td>
      <td>1287.500000</td>
      <td>2.900000</td>
      <td>64239.00000</td>
      <td>100.100000</td>
      <td>98.850000</td>
      <td>26.450000</td>
      <td>46.000000</td>
      <td>49.500000</td>
      <td>54.000000</td>
      <td>3.600000</td>
      <td>37.642650</td>
      <td>-87.241850</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.112082e+06</td>
      <td>1979.000000</td>
      <td>1533.750000</td>
      <td>3.400000</td>
      <td>70710.50000</td>
      <td>108.550000</td>
      <td>105.382500</td>
      <td>29.000000</td>
      <td>51.000000</td>
      <td>65.700000</td>
      <td>64.850000</td>
      <td>5.025000</td>
      <td>40.715125</td>
      <td>-80.675250</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.014047e+07</td>
      <td>3422.000000</td>
      <td>3000.000000</td>
      <td>6.200000</td>
      <td>136338.00000</td>
      <td>178.600000</td>
      <td>119.830000</td>
      <td>36.700000</td>
      <td>84.000000</td>
      <td>88.700000</td>
      <td>83.500000</td>
      <td>6.900000</td>
      <td>47.621100</td>
      <td>-71.085200</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Summary statistics for mid-sized cities
mid_cities = cities.loc[cities.Size == 'Mid-Sized']
mid_cities.describe()
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
      <th>Population</th>
      <th>AvgRent</th>
      <th>MedianRent</th>
      <th>UnempRate</th>
      <th>AvgIncome</th>
      <th>CostOfLiving</th>
      <th>PriceParity</th>
      <th>CommuteTime</th>
      <th>MedianAQI</th>
      <th>WalkScore</th>
      <th>BikeScore</th>
      <th>TransitScore</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>129.000000</td>
      <td>129.000000</td>
      <td>129.000000</td>
      <td>125.000000</td>
      <td>129.000000</td>
      <td>122.000000</td>
      <td>129.000000</td>
      <td>126.000000</td>
      <td>129.000000</td>
      <td>23.000000</td>
      <td>23.000000</td>
      <td>129.000000</td>
      <td>129.000000</td>
      <td>129.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>503160.472868</td>
      <td>1521.248062</td>
      <td>1068.031008</td>
      <td>3.446400</td>
      <td>57256.813953</td>
      <td>97.607377</td>
      <td>95.566589</td>
      <td>23.334921</td>
      <td>41.193798</td>
      <td>38.365217</td>
      <td>45.047826</td>
      <td>2.334884</td>
      <td>37.386640</td>
      <td>-92.338268</td>
    </tr>
    <tr>
      <th>std</th>
      <td>206013.220412</td>
      <td>479.419620</td>
      <td>342.103236</td>
      <td>1.329294</td>
      <td>12252.266043</td>
      <td>13.052837</td>
      <td>5.534128</td>
      <td>2.894141</td>
      <td>6.788176</td>
      <td>7.553904</td>
      <td>8.968828</td>
      <td>1.124490</td>
      <td>5.666978</td>
      <td>16.017321</td>
    </tr>
    <tr>
      <th>min</th>
      <td>256728.000000</td>
      <td>781.000000</td>
      <td>663.000000</td>
      <td>1.600000</td>
      <td>34503.000000</td>
      <td>83.900000</td>
      <td>85.880000</td>
      <td>17.300000</td>
      <td>18.000000</td>
      <td>21.400000</td>
      <td>29.700000</td>
      <td>0.400000</td>
      <td>25.997500</td>
      <td>-149.109100</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>328883.000000</td>
      <td>1217.000000</td>
      <td>854.000000</td>
      <td>2.500000</td>
      <td>50616.000000</td>
      <td>88.625000</td>
      <td>91.870000</td>
      <td>21.425000</td>
      <td>39.000000</td>
      <td>33.250000</td>
      <td>40.300000</td>
      <td>1.500000</td>
      <td>33.364500</td>
      <td>-98.246700</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>433353.000000</td>
      <td>1420.000000</td>
      <td>959.000000</td>
      <td>3.100000</td>
      <td>54623.000000</td>
      <td>93.250000</td>
      <td>93.960000</td>
      <td>22.900000</td>
      <td>42.000000</td>
      <td>39.100000</td>
      <td>43.700000</td>
      <td>2.100000</td>
      <td>37.689500</td>
      <td>-87.191100</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>649903.000000</td>
      <td>1784.000000</td>
      <td>1188.000000</td>
      <td>3.900000</td>
      <td>61547.000000</td>
      <td>102.075000</td>
      <td>98.960000</td>
      <td>24.800000</td>
      <td>44.000000</td>
      <td>43.900000</td>
      <td>51.900000</td>
      <td>3.000000</td>
      <td>41.311300</td>
      <td>-81.322300</td>
    </tr>
    <tr>
      <th>max</th>
      <td>978529.000000</td>
      <td>3350.000000</td>
      <td>2602.000000</td>
      <td>8.800000</td>
      <td>127391.000000</td>
      <td>163.900000</td>
      <td>111.810000</td>
      <td>33.300000</td>
      <td>67.000000</td>
      <td>49.700000</td>
      <td>65.500000</td>
      <td>5.100000</td>
      <td>61.150800</td>
      <td>-70.271500</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Summary statistics for small cities
small_cities = cities.loc[cities.Size == 'Small']
small_cities.describe()
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
      <th>Population</th>
      <th>AvgRent</th>
      <th>MedianRent</th>
      <th>UnempRate</th>
      <th>AvgIncome</th>
      <th>CostOfLiving</th>
      <th>PriceParity</th>
      <th>CommuteTime</th>
      <th>MedianAQI</th>
      <th>WalkScore</th>
      <th>BikeScore</th>
      <th>TransitScore</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>159.000000</td>
      <td>159.000000</td>
      <td>153.000000</td>
      <td>155.000000</td>
      <td>159.000000</td>
      <td>158.000000</td>
      <td>158.000000</td>
      <td>107.00000</td>
      <td>117.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>159.000000</td>
      <td>159.000000</td>
      <td>159.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>152830.031447</td>
      <td>1350.805031</td>
      <td>903.183007</td>
      <td>3.329032</td>
      <td>53693.761006</td>
      <td>93.772785</td>
      <td>92.706582</td>
      <td>21.31215</td>
      <td>37.324786</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.508805</td>
      <td>38.796727</td>
      <td>-93.214609</td>
    </tr>
    <tr>
      <th>std</th>
      <td>44979.209688</td>
      <td>405.689107</td>
      <td>192.709334</td>
      <td>1.368807</td>
      <td>9761.348362</td>
      <td>8.804079</td>
      <td>4.695104</td>
      <td>3.61012</td>
      <td>8.521604</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.104042</td>
      <td>5.438363</td>
      <td>14.843885</td>
    </tr>
    <tr>
      <th>min</th>
      <td>58639.000000</td>
      <td>657.000000</td>
      <td>648.000000</td>
      <td>1.700000</td>
      <td>41012.000000</td>
      <td>82.600000</td>
      <td>85.480000</td>
      <td>15.70000</td>
      <td>10.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>26.894100</td>
      <td>-147.653300</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>121836.500000</td>
      <td>1057.000000</td>
      <td>772.000000</td>
      <td>2.400000</td>
      <td>47868.000000</td>
      <td>88.025000</td>
      <td>89.232500</td>
      <td>19.00000</td>
      <td>36.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.700000</td>
      <td>34.673200</td>
      <td>-100.609900</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>150309.000000</td>
      <td>1293.000000</td>
      <td>852.000000</td>
      <td>3.100000</td>
      <td>51965.000000</td>
      <td>91.050000</td>
      <td>92.025000</td>
      <td>20.70000</td>
      <td>39.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.400000</td>
      <td>39.188600</td>
      <td>-88.970300</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>181630.000000</td>
      <td>1590.000000</td>
      <td>1011.000000</td>
      <td>3.850000</td>
      <td>56899.000000</td>
      <td>97.275000</td>
      <td>94.875000</td>
      <td>23.45000</td>
      <td>42.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2.300000</td>
      <td>42.471750</td>
      <td>-82.215950</td>
    </tr>
    <tr>
      <th>max</th>
      <td>249843.000000</td>
      <td>2898.000000</td>
      <td>1959.000000</td>
      <td>13.700000</td>
      <td>125455.000000</td>
      <td>149.600000</td>
      <td>112.070000</td>
      <td>39.40000</td>
      <td>67.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4.700000</td>
      <td>64.835300</td>
      <td>-68.790600</td>
    </tr>
  </tbody>
</table>
</div>



From tables above, we can see that regional price parity in cities with population more than one million is almost equal to the national average of 100. Meanwhile, living cost in mid-sized and small cities is 95.6% and 92.7% of the U.S. average. <br>
Just as we saw on the heat map, larger cities have worse air quality and longer commutes, but have higher incomes and better transportation.

## Commute Time vs Transit Score by Size
The heatmap shows some correlation (0.3) between the median commute time and the transit score. It doesn't necessarily means that the better the public transportation in a city, the longer it takes to get to work, but this correlation seems counterintuitive. Let's explore it further by plotting it and using the price parity and size as third and forth variables.<br>
The most likely explanation is that both longer commutes and good public transportation are attributes of large cities with high costs of living.


```python
palette = sns.color_palette("coolwarm", as_cmap=True)
sns.scatterplot(data=cities, x='CommuteTime', y='TransitScore', hue='PriceParity', palette=palette, 
                size='Size', size_order=['Large', 'Mid-Sized', 'Small'], sizes=[80, 40, 20])
plt.title('Median Commute Time vs Transit Score', fontsize=16)
plt.xlabel('Median Travel Time to Work')
plt.ylabel('Transit Score')
plt.show()
```


    
![Commute Time vs Transit Score by Size](/assets/leb/cities2.png)

    


The larger and more expensive cities occupy the upper right corner of the graph, while smaller and cheaper cities are associated with poorer public transportation and shorter commutes.

## Average Income and Rent by Size and Region


```python
ax = sns.scatterplot(data=cities, x='AvgIncome', y='AvgRent', hue='Region', 
                size='Size', size_order=['Large', 'Mid-Sized', 'Small'], sizes=[80, 40, 20])
ax.set_xlim(left=25000)
plt.title('Average Incomes vs Average Rental Prices', fontsize=16)
plt.xlabel('Average Income')
plt.ylabel('Average Rental Price')
plt.show()
```


    
![Average Income and Rent by Size and Region](/assets/leb/cities3.png)
    


From the graph, we can see that the South has lower incomes than the Midwest (with a number of outliers), but more expensive rents. The western cities have higher rents, but also higher incomes. Let's run the Tukey's test to see if the average incomes and rental prices are significantly different between the US regions.
- A value of 0 indicates that there is no significant difference in average income between the corresponding regions.
- A value of 1 indicates that there is a significant difference in average income between the corresponding regions. 


```python
# Differences in the means of average incomes
sp.posthoc_tukey_hsd(x=cities.AvgIncome, g=cities.Region)
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
      <th>Midwest</th>
      <th>Northeast</th>
      <th>South</th>
      <th>West</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Midwest</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Northeast</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>South</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>West</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



The table shows some interesting results. There's no significant difference in average incomes in the Midwest-South and Midwest-West pairs, but there is a difference in West-South. The Northeast and South incomes are the most "different" from the rest of the group. From the scatterplot, we can conclude that the Northeast has the highest, while the South has the lowest. The Western cities are second in terms of income, and the Midwest is third.

Let's check this assumption by looking at the actual means.


```python
# Income means across regions
region_list = ['Midwest', 'Northeast', 'South', 'West']
for region in region_list:
    print(f"Average income in {region} is {cities[cities['Region'] == region]['AvgIncome'].mean()}")
```

    Average income in Midwest is 56058.39759036145
    Average income in Northeast is 62188.065217391304
    Average income in South is 54122.794117647056
    Average income in West is 60972.50632911392
    


```python
# Differences in means of average rental prices
sp.posthoc_tukey_hsd(x=cities.AvgRent, g=cities.Region)
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
      <th>Midwest</th>
      <th>Northeast</th>
      <th>South</th>
      <th>West</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Midwest</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Northeast</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>South</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>West</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
for region in ['Midwest', 'Northeast', 'South', 'West']:
    print(f"Average rental in {region} is {cities[cities['Region'] == region]['AvgRent'].mean()}")
```

    Average rental in Midwest is 1135.3975903614457
    Average rental in Northeast is 1548.5652173913043
    Average rental in South is 1476.2426470588234
    Average rental in West is 1866.8354430379748
    

The only pair with no significant difference is Northeast-South. From these two tables and the scatterplot, we can see that a region with the highest rents is not the region with the highest incomes, and the region with the lowest rents is not the region with the lowest incomes. This could be explained by outliers in one of these two variables (for example, a city in the West with very high rents but moderate incomes).

## Median Rental Prices by Region and Size
Explore the rental prices by region further with regard to the size.


```python
fig, axes = plt.subplots(1, 4, figsize=(16, 6))

sns.set_palette("Set3")
ax1, ax2, ax3, ax4 = axes
ylim = (600, 2800)
fontsize = 12

for ax, region in zip(axes, region_list):
    sns.boxplot(y='MedianRent', x='Size', data=cities[cities['Region'] == region], ax=ax,
                showfliers=False, palette="Set3")
    
    ax.set_ylim(ylim)
    ax.set_title(f"Median Rental Price in {region}", fontsize=fontsize)
    ax.xaxis.set_label_position('top')
    ax.set_xlabel('')
    ax.set_ylabel('')
    
    # Adjust tick label font size and add grid lines
    ax.tick_params(axis='both', which='major', labelsize=fontsize)
    ax.grid(True, linestyle='--', axis='y')

plt.tight_layout()
plt.show()
```


    
![Median Rental Prices by Region and Size](/assets/leb/cities4.png)
    


Large cities are more expensive to rent, but there are important differences between regions. For example, large cities in the Midwest are still cheaper than small cities in the Northeast. Midsize Western cities can be as expensive or more than large Southern and Northeastern cities.

## Unemployment Rate by Region and Size
Cities in the West have the highest unemployment rate, while the Northeast is the only region where small cities have a higher unemployment rate than medium-sized cities.


```python
fig, ax = plt.subplots()
sns.set_palette("Set2")

with sns.axes_style("whitegrid"):
    sns.barplot(y='UnempRate', x='Region', hue='Size', data=cities)
    ax.set_ylabel('Unemployment Rate')

    # To show percetage
    ax.yaxis.set_major_formatter(mtick.PercentFormatter())
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)
    ax.grid(True, linestyle='-', axis='y')

plt.title('Unemployment by Region and City Size', fontsize=14)
plt.show()
```


    
![Unemployment by Region and Size](/assets/leb/cities5.png)

    


## Commute Times by Region and Size
The bigger a city, the longer it takes to get to work. But there are some differences between regions.


```python
fig, ax = plt.subplots(figsize=(12, 7))

sns.boxplot(y='CommuteTime', x='Size', hue='Region', data=cities, showfliers=False, ax=ax)

ax.set_ylabel('Median Commute Time')
ax.set_title('Median Commute Time in US Cities by City Size', fontsize=14)

ax.legend(loc='upper right')
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.show()
```


    
![Median Commute Time by Region and Size](/assets/leb/cities6.png)

    


## Median AQI by Size


```python
fig, ax = plt.subplots(figsize=(8, 6))
sns.set_palette("Set1")

sns.kdeplot(data=small_cities['MedianAQI'], shade=True, label='Small Cities')
sns.kdeplot(data=mid_cities['MedianAQI'], shade=True, label='Mid-Sized Cities')
sns.kdeplot(data=large_cities['MedianAQI'], shade=True, label='Large Cities')

plt.xlabel('Air Quality Index')
plt.ylabel('')
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
plt.title('Air Quality in United States Cities by City Size')
plt.legend()

plt.show()
```


    
![Air Quality Index Distribution Plot](/assets/leb/cities7.png)

    


Obviously, the smaller the city, the cleaner the air. The index from 0 to 50 is considered good, from 51 to 100 – moderate. In the long run, there is no city with unhealthy air. Still, some large cities are in the upper half of what is considered moderate. No large city has an AQI below 20, which makes sense since "large cities" in the US are really large.


```python
cities[cities['MedianAQI'] >= 75][['City', 'Population', 'MedianAQI']]
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
      <th>City</th>
      <th>Population</th>
      <th>MedianAQI</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>Phoenix</td>
      <td>4845832.0</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Riverside</td>
      <td>4599839.0</td>
      <td>84.0</td>
    </tr>
  </tbody>
</table>
</div>



## Conclusion
After collecting and cleaning the data, I used exploratory data analysis. There are many ways to explore this data, and I chose to do the following:
- Map of cities by size with all variable information in the toolkit;
- Heatmap to see the correlation between all variables and summary statistics tables;
- Scatterplot of correlation between commute times and transit score (by size);
- Scatterplot of correlation between average incomes and rents (by size and region);
- Boxplot of median rental prices (by region and size);
- Bar chart of unemployment rates (by region and size);
- Box plot of commute times (by region and size);
- Distribution plot of median air quality index (by size).


Some of the things we found during the analysis:
- The median rental price can be a better predictor of a city's livability than the average rental price – because it has a stronger correlation with other variables;
- The data confirms all the chronic problems of metro areas with large populations: poor air quality, longer commutes, high cost of living. But they have better public transportation and are more pedestrian friendly;
- Rents in the South are not significantly lower than in the Northeast, but incomes are much lower. Conversely, the Midwest has the same incomes as the South, but rents are much lower.
- Despite lower incomes, cities in the Midwest and South have lower unemployment rates;
- The Midwest also has the shortest commute times of any size category.
