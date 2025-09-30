# Ex.No: 05  IMPLEMENTATION OF TIME SERIES ANALYSIS AND DECOMPOSITION


### AIM:
To Illustrates how to perform time series analysis and decomposition on the monthly average temperature of a city/country and for airline passengers.

### ALGORITHM:
1. Import the required packages like pandas and numpy
2. Read the data using the pandas
3. Perform the decomposition process for the required data.
4. Plot the data according to need, either seasonal_decomposition or trend plot.
5. Display the overall results.

### PROGRAM:
```

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load dataset
data = pd.read_csv("/content/Environment_Temperature_change_E_All_Data_NOFLAG.csv", encoding='latin1')

# Filter for specific months and create a simplified month representation
data_filtered = data[data['Months'].isin(['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])].copy()
data_filtered['month_num'] = data_filtered['Months'].apply(lambda x: pd.to_datetime(x, format='%B').month)

# Melt the DataFrame to have a single date column
melted_data = data_filtered.melt(
    id_vars=['Area', 'Months', 'month_num'],
    value_vars=[col for col in data_filtered.columns if col.startswith('Y')],
    var_name='Year',
    value_name='Temperature'
)

# Create a datetime column
melted_data['year_num'] = melted_data['Year'].str.replace('Y', '').astype(int)
melted_data['date'] = pd.to_datetime(melted_data['year_num'].astype(str) + '-' + melted_data['month_num'].astype(str) + '-01')

# Drop rows with missing temperature
melted_data = melted_data.dropna(subset=['Temperature'])

# Monthly average temperatures (assuming Area and Element don't need to be in the aggregation for now)
# You might want to group by Area and Element as well depending on your analysis goals
monthly_temperatures = melted_data.groupby(pd.Grouper(key='date', freq='M'))['Temperature'].mean()


# Trend (simple moving average)
# Increased window size as the data is yearly and monthly combined
trend = monthly_temperatures.rolling(window=12, center=True).mean()

# Fake seasonality (subtract trend, then repeat pattern)
# Recalculate seasonality based on the available monthly data
seasonal = monthly_temperatures - trend
# Calculate average seasonality for each month across all years
average_seasonal_pattern = seasonal.groupby(seasonal.index.month).mean()
# Tile the average seasonal pattern to match the length of the monthly_temperatures Series
seasonal = seasonal.index.map(lambda x: average_seasonal_pattern.loc[x.month])


# Residual = Temperature - Trend - Seasonal
residual = monthly_temperatures - trend - seasonal

# Plot 4 graphs
plt.figure(figsize=(10, 12))

plt.subplot(411)
plt.plot(monthly_temperatures, label='Average Temperature')
plt.legend(loc='upper left')
plt.title('Environment Temperatures (Monthly)')

plt.subplot(412)
plt.plot(trend, label='Trend (Moving Avg)', color='orange')
plt.legend(loc='upper left')
plt.title('Trend Plot')

plt.subplot(413)
plt.plot(seasonal, label='Seasonal (Approx)', color='green')
plt.legend(loc='upper left')
plt.title('Seasonality Plot')

plt.subplot(414)
plt.plot(residual, label='Residuals', color='red')
plt.legend(loc='upper left')
plt.title('Residual Plot')

plt.tight_layout()
plt.show()

```

### OUTPUT:

PLOTTING THE DATA:

<img width="1003" height="282" alt="image" src="https://github.com/user-attachments/assets/2085b0ce-0df6-460a-86f0-9e232d379c11" />


SEASONAL PLOT REPRESENTATION :

<img width="1072" height="278" alt="image" src="https://github.com/user-attachments/assets/1fa2760a-f7fe-48b7-8933-7516fe6945bf" />


TREND PLOT REPRESENTATION :

<img width="1026" height="283" alt="image" src="https://github.com/user-attachments/assets/c35c0bf3-9bb0-4be2-bd73-fdc05018c909" />


OVERAL REPRESENTATION:

<img width="1005" height="290" alt="image" src="https://github.com/user-attachments/assets/e7efee3d-d0ed-4e65-bb4b-94f275529200" />


### RESULT:
Thus we have created the python code for the time series analysis and decomposition.
