# PHASE 1 PROJECT - Aviation Risk Analysis & Recommendations

## Introduction
As the company is seeking to diversify its portfolio through expansion into commercial and private aviation, it is impotrant to have a deep understanding of operational risks. Using the accident data from the National Transportation Safety Board, this project evaluates the safety records of various aircraft manufacturesrs and models from 1962 to 2023. I will explore the data to determine the lowest risk options available. This will inform actionable recommendations to stakeholders to acquire safe and reliable fleet.

## Business Understanding
1. Problem Statement
The primary challenge is that the company lacks internal expertise regarding the safety profiles and potential risks associated with different types of aircraft. Investing in high-risk aircraft could lead to financial loss, legal liability, reputational damage, and loss of life. The company needs to know which aircraft present the lowest risk to ensure the safety of operations and the viability of the new business unit.

2. Main Objective
The goal of this analysis is to determine which aircraft manufacturers and models have the lowest risk profile to support the company's new business endeavor.

- Analyzing historical aviation accident data to identify trends in safety.
- Evaluating risk based on key factors such as aircraft make, engine type, and phase of flight.
- Providing three concrete, actionable recommendations to the head of the new aviation division to guide their purchasing decisions.

4. Stakeholders
Primary Stakeholder: Head of the new Aviation Division.
Secondary Stakeholders: Executive Board, Investors, and potential future passengers/clients who rely on the company's commitment to safety.

5. Data Source
The analysis utilizes the National Transportation Safety Board (NTSB) Aviation Accident Dataset, which contains information on civil aviation accidents and selected incidents within the United States and international waters from 1962 to 2023. This comprehensive dataset allows for a robust assessment of long-term safety trends and specific incident causes.

---

## Data Understanding
#### Data source
The dataset for this analysis is sourved from **National Transportation Safety Board, that covers aviation accidents and incidents involving aircrafts in the United States and international waters from 1962 to 2023.
This data serves as a reliable sourve to determine aircraft safety records.

#### Data Schema
The dataset includes tens of thousands of records, where each row represents a single aviation accident or incident.
Each record contains information about:
- The aircraft (type, category, manufacturer, model)
- The event (date, location, purpose of flight)
- The environment (weather, light conditions)
- The outcome (injuries, fatalities, damage level)
This makes the dataset comprehensive enough to evaluate both accident frequency and accident severity across aircraft types.

#### Data Strategy
The relevant columns include:
- Make
- Model
- Aircraft.Category
- Broad.Phase.of.Flight
- Weather.Condition
- Number.of.Engines
- Total.Fatal.Injuries
- Total.Serious.Injuries
- Total.Minor.Injuries
- Total_Uninjured
- Aircraft.Damage
- Purpose.of.flight
- Event.Date

#### Data Quality
##### Missing Values
- Some older records (1960s–1980s) may lack detail
- Certain columns may have missing values (None) that may need to be filled or droped.
- Weather and light condition fields frequently contain “Unknown”
##### Inconsistent Values
Since the data set is based on a large period, certain values may have format changes or spelling errors.
- Manufacturers may appear in multiple forms
- Aircraft models may use different formatting or spacing
##### Outliers
- Extremely old or rare aircraft types
- Occasional data-entry errors
- Records with zero injuries but aircraft recorded as “Destroyed”
---

## Data Preparation
Loading data and importing libraries


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

df = pd.read_csv('AviationData.csv', encoding='utf-8', encoding_errors='replace', low_memory=False)
df.head() #
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
      <th>Event.Id</th>
      <th>Investigation.Type</th>
      <th>Accident.Number</th>
      <th>Event.Date</th>
      <th>Location</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Airport.Code</th>
      <th>Airport.Name</th>
      <th>...</th>
      <th>Purpose.of.flight</th>
      <th>Air.carrier</th>
      <th>Total.Fatal.Injuries</th>
      <th>Total.Serious.Injuries</th>
      <th>Total.Minor.Injuries</th>
      <th>Total.Uninjured</th>
      <th>Weather.Condition</th>
      <th>Broad.phase.of.flight</th>
      <th>Report.Status</th>
      <th>Publication.Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20001218X45444</td>
      <td>Accident</td>
      <td>SEA87LA080</td>
      <td>1948-10-24</td>
      <td>MOOSE CREEK, ID</td>
      <td>United States</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Personal</td>
      <td>NaN</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>UNK</td>
      <td>Cruise</td>
      <td>Probable Cause</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20001218X45447</td>
      <td>Accident</td>
      <td>LAX94LA336</td>
      <td>1962-07-19</td>
      <td>BRIDGEPORT, CA</td>
      <td>United States</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Personal</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>UNK</td>
      <td>Unknown</td>
      <td>Probable Cause</td>
      <td>19-09-1996</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20061025X01555</td>
      <td>Accident</td>
      <td>NYC07LA005</td>
      <td>1974-08-30</td>
      <td>Saltville, VA</td>
      <td>United States</td>
      <td>36.922223</td>
      <td>-81.878056</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Personal</td>
      <td>NaN</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>IMC</td>
      <td>Cruise</td>
      <td>Probable Cause</td>
      <td>26-02-2007</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20001218X45448</td>
      <td>Accident</td>
      <td>LAX96LA321</td>
      <td>1977-06-19</td>
      <td>EUREKA, CA</td>
      <td>United States</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Personal</td>
      <td>NaN</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>IMC</td>
      <td>Cruise</td>
      <td>Probable Cause</td>
      <td>12-09-2000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20041105X01764</td>
      <td>Accident</td>
      <td>CHI79FA064</td>
      <td>1979-08-02</td>
      <td>Canton, OH</td>
      <td>United States</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Personal</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>VMC</td>
      <td>Approach</td>
      <td>Probable Cause</td>
      <td>16-04-1980</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 31 columns</p>
</div>



### Dataset Overview
Basic data information


```python
df.shape
```




    (88889, 31)



The dataset consists of 88,889 records (rows) and 31 features (columns)


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 88889 entries, 0 to 88888
    Data columns (total 31 columns):
     #   Column                  Non-Null Count  Dtype  
    ---  ------                  --------------  -----  
     0   Event.Id                88889 non-null  object 
     1   Investigation.Type      88889 non-null  object 
     2   Accident.Number         88889 non-null  object 
     3   Event.Date              88889 non-null  object 
     4   Location                88837 non-null  object 
     5   Country                 88663 non-null  object 
     6   Latitude                34382 non-null  object 
     7   Longitude               34373 non-null  object 
     8   Airport.Code            50132 non-null  object 
     9   Airport.Name            52704 non-null  object 
     10  Injury.Severity         87889 non-null  object 
     11  Aircraft.damage         85695 non-null  object 
     12  Aircraft.Category       32287 non-null  object 
     13  Registration.Number     87507 non-null  object 
     14  Make                    88826 non-null  object 
     15  Model                   88797 non-null  object 
     16  Amateur.Built           88787 non-null  object 
     17  Number.of.Engines       82805 non-null  float64
     18  Engine.Type             81793 non-null  object 
     19  FAR.Description         32023 non-null  object 
     20  Schedule                12582 non-null  object 
     21  Purpose.of.flight       82697 non-null  object 
     22  Air.carrier             16648 non-null  object 
     23  Total.Fatal.Injuries    77488 non-null  float64
     24  Total.Serious.Injuries  76379 non-null  float64
     25  Total.Minor.Injuries    76956 non-null  float64
     26  Total.Uninjured         82977 non-null  float64
     27  Weather.Condition       84397 non-null  object 
     28  Broad.phase.of.flight   61724 non-null  object 
     29  Report.Status           82505 non-null  object 
     30  Publication.Date        75118 non-null  object 
    dtypes: float64(5), object(26)
    memory usage: 21.0+ MB
    

It is primarily composed of categorical data (26 object columns) and some numerical data (5 float64 columns) representing injury counts and engine numbers.
There is significant missing data in several key columns that will require handling

## Data cleaning
The original dataset contained significant inconsistencies, missing values, and irrelevant records. To prepare the data for analysis, we perform a targeted cleaning process on specific columns to ensure the insights derived are accurate and relevant to a commercial business context.


```python
# Create copy for cleaning
df_clean = df.copy() 
```

Standardize category variables


```python
df_clean['Make'] = df_clean['Make'].str.strip().str.upper()
df_clean['Model'] = df_clean['Model'].str.strip()
df_clean['Make_Model'] = df_clean['Make'] + ' ' + df_clean['Model']
```

Event Date
Converted the column to datetime format. We then filtered the dataset to include only records from 1995 to present. This ensures our recommendations are based on relevant, modern aviation history.


```python
df_clean['Event.Date'] = pd.to_datetime(df_clean['Event.Date'])
df_clean['Year'] = df_clean['Event.Date'].dt.year
df_clean.drop
df_clean = df_clean[df_clean['Year'] >= 1990] #ocus on data from 1990 onwards for better relevance to 2025
```

Missing values in injury column


```python
# injury Columns: Fill NaNs with 0
injury_cols = ['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries', 'Total.Uninjured']
df_clean[injury_cols] = df_clean[injury_cols].fillna(0)
```

#### Weather Conditions
Weather conditions play a role in accident severity. Missing values are preserved as “UNKNOWN” rather than dropped.


```python
#converts all values to uppercase and removes any space
df_clean['Weather.Condition'] = df_clean['Weather.Condition'].str.upper().str.strip() 
# replace missing values with 'UNKNOWN'
df_clean['Weather.Condition'] = df_clean['Weather.Condition'].fillna('UNKNOWN')
#Replace 'UNK' with 'UNKNOWN' for consistency
df_clean['Weather.Condition'] = df_clean['Weather.Condition'].replace('UNK', 'UNKNOWN')
```


```python
df_clean['Weather.Condition'].value_counts()
```




    Weather.Condition
    VMC        55043
    UNKNOWN     5241
    IMC         3914
    Name: count, dtype: int64



Engine types


```python
df_clean['Engine.Type'] = df_clean['Engine.Type'].fillna('Unknown') # Fill NaNs with 'Unknown'
df_clean['Engine.Type'] = df_clean['Engine.Type'].replace('UNK', 'Unknown') # Replace 'UNK' with 'Unknown'
```


```python
df_clean['Purpose.of.flight'] = df_clean['Purpose.of.flight'].fillna('Unknown') # Fill NaNs with 'Unknown'
df_clean['Purpose.of.flight'] = df_clean['Purpose.of.flight'].replace('UNK', 'Unknown') # Replace 'UNK' with 'Unknown'
```

There are several categories that mean the same thing but are spelled differently. To get an accurate count, I merge them


```python
# Create a dictionary to merge the names
cleanup_map = {
    'Air Race show': 'Air Race/Show',
    'ASHO': 'Air Race/Show',
    'PUBL': 'Public Aircraft',
    'PUBS': 'Public Aircraft',
    'Public Aircraft - Federal': 'Public Aircraft',
    'Public Aircraft - State': 'Public Aircraft',
    'Public Aircraft - Local': 'Public Aircraft'
} 

# Apply the mapping
df_clean['Purpose.of.flight'] = df_clean['Purpose.of.flight'].replace(cleanup_map)
```

#### Aircraft Category
Remove rows without this information


```python
df_clean = df_clean.dropna(subset=['Aircraft.Category']) # Drop rows where 'Aircraft.Category' is NaN
```


```python
# Total Injuries combined metric to make comparison numerically
df['Total.Injuries'] = (df['Total.Fatal.Injuries'] + df['Total.Serious.Injuries'] + df['Total.Minor.Injuries']) # Calculate total injuries
```

## Data Analysis
