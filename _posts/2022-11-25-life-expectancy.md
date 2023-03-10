---
title: Life Expectancy Analysis
date: 2022-11-25 13:20:00
layout: post
categories: [health, economy]
tags: [python,eda,data analysis,world bank]
---

# Life Expectanct at Birth
I used data from the World Bank on life expectancy along with other parameters (mortality, diseases, etc.) for every country every year from 2000 to 2015. This data is quite a mess. For example, the GDP column is measured in many different units, so it is completely unusable, or the column names are broken.

There are no analytical conclusions at the end, as all the findings are quite obvious. The sole goal of this project is to apply my knowledge in data cleaning and visualization, as well as try to write a good-looking and reader-friendly notebook.

## Data from the World Bank contains four variables
`- Country` <br>
 `- Year` – from 2000 to 2015 <br>
 `- Status` – developed / developing <br>
 `- Life Expectancy at Birth` – abbreviated as LEB for convenience <br>
 `- Adult Mortality, 'Infant deaths', 'Under Five Deaths' `(all per 1000 people)`, 'Alcohol', 'Hepatitis B', 'Measles', 'BMI', 'Polio', 'Diphtheria', 'HIV/AIDS'` – medical statistics for each country each year<br>
 `- Population, GDP`* – could be useful, but not for the scope of this project. <br>
 `- Income composition of resources` – how effeicient the resources are used (supposed to have the highest positive correlation with the LED variable) <br>
 `- Schooling` - years of schooling <br>
 
`*` If we take a look at the GDP column we'll find that the values are messed up. Some of them (regardless of the Year variable) has a unit of billion, others are in ten billion, a hundred million, etc.

## Steps of the project

- Clean up the dataset.

- Analyze changes in LEB for 15 years.

    - LEB changes for 15 years in developing and developed nations
    - Show correlation table and scatter plots with the LEB variable.
    
- Report what was done throughout the project

``` python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import scipy.stats as stats
```
``` python
df = pd.read_csv('Life Expectancy Data.csv')
```
## Processing
### Take a look at the columns
```python
# Display all the columns
pd.set_option('display.max_columns', len(df.columns))
```
```python
df.columns
```
```markdown
Index(['Country', 'Year', 'Status', 'Life expectancy ', 'Adult Mortality',
       'infant deaths', 'Alcohol', 'percentage expenditure', 'Hepatitis B',
       'Measles ', ' BMI ', 'under-five deaths ', 'Polio', 'Total expenditure',
       'Diphtheria ', ' HIV/AIDS', 'GDP', 'Population',
       ' thinness  1-19 years', ' thinness 5-9 years',
       'Income composition of resources', 'Schooling'],
      dtype='object')
```

```python
df.shape
```
```markdown
(2938, 22)
```

The column names need to be fixed as they have whitespaces at the string ends and different cases
### Fixing column names (using snake case)
```python
# Strip the whitespaces in the column names
df.columns = [x.strip() for x in df.columns]

# After getting rid of the NaN values, we can safely change the column names
df.columns = [x.lower() for x in df.columns]
df.columns = [x.replace(' ', '_').replace('-', '_').replace('/', '_') for x in df.columns]

# Displaying the column names once again
df.columns
```
```markdown
Index(['country', 'year', 'status', 'life_expectancy', 'adult_mortality',
       'infant_deaths', 'alcohol', 'percentage_expenditure', 'hepatitis_b',
       'measles', 'bmi', 'under_five_deaths', 'polio', 'total_expenditure',
       'diphtheria', 'hiv_aids', 'gdp', 'population', 'thinness__1_19_years',
       'thinness_5_9_years', 'income_composition_of_resources', 'schooling'],
      dtype='object')
```
### Drop unnecessary columns
```python
df = df.drop(columns=['percentage_expenditure', 'total_expenditure', 'thinness__1_19_years',
       'thinness_5_9_years', 'gdp', 'population'])
```
### Deal with missing values
```python
# Looking for NaN values
leb_nan = df['life_expectancy'].isna()
df.loc[leb_nan]
```

| country 	|                                              year 	| status 	| life_expectancy 	| adult_mortality 	| infant_deaths 	| measles 	|  bmi 	| under_five_deaths 	| polio 	| diphtheria 	| hiv_aids 	| alcohol 	| hepatitis_b 	| income_composition_of_resources 	| schooling 	|      	|
|--------:	|--------------------------------------------------:	|-------:	|----------------:	|----------------:	|--------------:	|--------:	|-----:	|------------------:	|------:	|-----------:	|---------:	|--------:	|------------:	|--------------------------------:	|----------:	|-----:	|
|     846 	|                                 Equatorial Guinea 	|   2000 	|      Developing 	|            52.7 	|         336.0 	|       3 	|    0 	|              18.3 	|     4 	|       41.0 	|     34.0 	|     1.9 	|         4.5 	|                            37.5 	|       0.0 	|  0.0 	|
|    1776 	|                                           Myanmar 	|   2001 	|      Developing 	|            62.5 	|         239.0 	|      72 	| 2519 	|              14.1 	|    98 	|       77.0 	|     73.0 	|     0.4 	|         0.4 	|                            71.9 	|       0.4 	|  7.6 	|
|    1719 	|                                          Mongolia 	|   2009 	|      Developing 	|            66.9 	|         235.0 	|       1 	|    8 	|              45.9 	|     2 	|       96.0 	|     95.0 	|     0.1 	|         4.6 	|                            97.0 	|       0.7 	| 13.8 	|
|    2540 	|                              Syrian Arab Republic 	|   2002 	|      Developing 	|            72.8 	|         135.0 	|       9 	|  538 	|              45.3 	|    11 	|       86.0 	|     84.0 	|     0.1 	|         1.2 	|                             8.0 	|       0.6 	| 10.2 	|
|    2013 	|                                              Peru 	|   2012 	|      Developing 	|            74.9 	|         129.0 	|       9 	|    0 	|              53.6 	|    11 	|       94.0 	|     95.0 	|     0.1 	|         5.1 	|                            95.0 	|       0.7 	| 13.4 	|
|     714 	|             Democratic People's Republic of Korea 	|   2011 	|      Developing 	|            69.4 	|         153.0 	|       8 	|    0 	|               3.8 	|    10 	|       99.0 	|     94.0 	|     0.1 	|         3.4 	|                            94.0 	|       0.5 	|  9.9 	|
|     704 	|             Democratic People's Republic of Korea 	|   2001 	|      Developing 	|            66.6 	|         177.0 	|      16 	|    0 	|              25.7 	|    21 	|       98.0 	|     62.0 	|     0.1 	|         2.5 	|                            70.0 	|       0.5 	| 10.2 	|
|    2160 	|                                       Saint Lucia 	|   2000 	|      Developing 	|            71.6 	|         183.0 	|       0 	|    0 	|              36.8 	|     0 	|        7.0 	|      7.0 	|     0.4 	|        11.7 	|                            15.3 	|       0.0 	| 12.8 	|
|    2028 	|                                       Philippines 	|   2012 	|      Developing 	|            68.1 	|         217.0 	|      56 	| 1536 	|              23.7 	|    71 	|       88.0 	|     88.0 	|     0.1 	|         5.0 	|                            88.0 	|       0.7 	| 11.6 	|
|    2760 	| United Kingdom of Great Britain and Northern I... 	|   2006 	|       Developed 	|            79.3 	|          82.0 	|       4 	|  764 	|              61.3 	|     4 	|       92.0 	|     92.0 	|     0.1 	|        11.6 	|                            87.4 	|       0.8 	| 15.8 	|

We'll delete all the rows with missing values in the Life Expectancy column, because this variable is crucial for the analysis.
```python
df = df.dropna(subset=['life_expectancy'])
```
For the rest of the columns we'll fill the NaN values with variable means depending on the Status parameter (whether a country is developed or developing according to the World Bank).
```python
df.isna().sum()
```
```markdown
country                              0
year                                 0
status                               0
life_expectancy                      0
adult_mortality                      0
infant_deaths                        0
alcohol                            193
hepatitis_b                        553
measles                              0
bmi                                 32
under_five_deaths                    0
polio                               19
diphtheria                          19
hiv_aids                             0
income_composition_of_resources    160
schooling                          160
dtype: int64
```
### Create two datesets for two types of nations (based on the Status column) to analyze them separetely (and to concatenate them later).
```python
developed = df.loc[df.status == 'Developed']
developing = df.loc[df.status == 'Developing']

# Split both dataset by those with and without missing values
developed_miss = developed.loc[:, developed.isnull().any()]
developing_miss = developing.loc[:, developing.isnull().any()]

# Drop the columns with missing values
developed = developed.drop(columns=developed_miss.columns)
developing = developing.drop(columns=developing_miss.columns)

# Fill in the missing values with multiple imputation
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
imp = IterativeImputer(max_iter=len(developed_miss), random_state=0)
imp.fit(developed_miss)
developed_miss_filled = pd.DataFrame(np.round(imp.transform(developed_miss),1), columns=developed_miss.columns)

imp = IterativeImputer(max_iter=len(developing_miss), random_state=0)
imp.fit(developing_miss)
developing_miss_filled = pd.DataFrame(np.round(imp.transform(developing_miss),1), columns=developing_miss.columns)

# Just make sure, the two yet-to-be-merged datasets have the same lenth
print(len(developed_miss_filled), len(developed))
print(len(developing_miss_filled), len(developing))
```
```markdown
512 512
2416 2416
```
### Concatenate/merge all four DataFrames
```python
# developed and developed_miss_filled
developed[developed_miss_filled.columns] = developed_miss_filled.values

# developing and developing_miss_filled
developing[developing_miss_filled.columns] = developing_miss_filled.values

# developed and developing
countries = pd.concat([developed, developing])
countries = countries.sort_values(by='country').reset_index(drop=True)

# Taking a look at the final dataset
countries.sample(10)
```

| country 	|                                              year 	| status 	| life_expectancy 	| adult_mortality 	| infant_deaths 	| measles 	|  bmi 	| under_five_deaths 	| polio 	| diphtheria 	| hiv_aids 	| alcohol 	| hepatitis_b 	| income_composition_of_resources 	| schooling 	|      	|
|--------:	|--------------------------------------------------:	|-------:	|----------------:	|----------------:	|--------------:	|--------:	|-----:	|------------------:	|------:	|-----------:	|---------:	|--------:	|------------:	|--------------------------------:	|----------:	|-----:	|
|     846 	|                                 Equatorial Guinea 	|   2000 	|      Developing 	|            52.7 	|         336.0 	|       3 	|    0 	|              18.3 	|     4 	|       41.0 	|     34.0 	|     1.9 	|         4.5 	|                            37.5 	|       0.0 	|  0.0 	|
|    1776 	|                                           Myanmar 	|   2001 	|      Developing 	|            62.5 	|         239.0 	|      72 	| 2519 	|              14.1 	|    98 	|       77.0 	|     73.0 	|     0.4 	|         0.4 	|                            71.9 	|       0.4 	|  7.6 	|
|    1719 	|                                          Mongolia 	|   2009 	|      Developing 	|            66.9 	|         235.0 	|       1 	|    8 	|              45.9 	|     2 	|       96.0 	|     95.0 	|     0.1 	|         4.6 	|                            97.0 	|       0.7 	| 13.8 	|
|    2540 	|                              Syrian Arab Republic 	|   2002 	|      Developing 	|            72.8 	|         135.0 	|       9 	|  538 	|              45.3 	|    11 	|       86.0 	|     84.0 	|     0.1 	|         1.2 	|                             8.0 	|       0.6 	| 10.2 	|
|    2013 	|                                              Peru 	|   2012 	|      Developing 	|            74.9 	|         129.0 	|       9 	|    0 	|              53.6 	|    11 	|       94.0 	|     95.0 	|     0.1 	|         5.1 	|                            95.0 	|       0.7 	| 13.4 	|
|     714 	|             Democratic People's Republic of Korea 	|   2011 	|      Developing 	|            69.4 	|         153.0 	|       8 	|    0 	|               3.8 	|    10 	|       99.0 	|     94.0 	|     0.1 	|         3.4 	|                            94.0 	|       0.5 	|  9.9 	|
|     704 	|             Democratic People's Republic of Korea 	|   2001 	|      Developing 	|            66.6 	|         177.0 	|      16 	|    0 	|              25.7 	|    21 	|       98.0 	|     62.0 	|     0.1 	|         2.5 	|                            70.0 	|       0.5 	| 10.2 	|
|    2160 	|                                       Saint Lucia 	|   2000 	|      Developing 	|            71.6 	|         183.0 	|       0 	|    0 	|              36.8 	|     0 	|        7.0 	|      7.0 	|     0.4 	|        11.7 	|                            15.3 	|       0.0 	| 12.8 	|
|    2028 	|                                       Philippines 	|   2012 	|      Developing 	|            68.1 	|         217.0 	|      56 	| 1536 	|              23.7 	|    71 	|       88.0 	|     88.0 	|     0.1 	|         5.0 	|                            88.0 	|       0.7 	| 11.6 	|
|    2760 	| United Kingdom of Great Britain and Northern I... 	|   2006 	|       Developed 	|            79.3 	|          82.0 	|       4 	|  764 	|              61.3 	|     4 	|       92.0 	|     92.0 	|     0.1 	|        11.6 	|                            87.4 	|       0.8 	| 15.8 	|


```python
# Making sure there are no NaN values left
countries.isna().sum()
```
```markdown
country                            0
year                               0
status                             0
life_expectancy                    0
adult_mortality                    0
infant_deaths                      0
measles                            0
bmi                                0
under_five_deaths                  0
polio                              0
diphtheria                         0
hiv_aids                           0
alcohol                            0
hepatitis_b                        0
income_composition_of_resources    0
schooling                          0
dtype: int64
```
```python
# Looking at the data types
countries['status'] = pd.Categorical(countries['status'], list(countries['status'].unique()))
countries.dtypes
```
```markdown
country                              object
year                                  int64
status                             category
life_expectancy                     float64
adult_mortality                     float64
infant_deaths                         int64
measles                               int64
bmi                                 float64
under_five_deaths                     int64
polio                               float64
diphtheria                          float64
hiv_aids                            float64
alcohol                             float64
hepatitis_b                         float64
income_composition_of_resources     float64
schooling                           float64
dtype: object
```
