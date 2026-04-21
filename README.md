# Ex.No: 1B                     CONVERSION OF NON STATIONARY TO STATIONARY DATA

# Date: 21-04-2026

### AIM:

To perform regular differncing,seasonal adjustment and log transformatio on international airline passenger data

### ALGORITHM:

1. Import the required packages like pandas and numpy
2. Read the data using the pandas
3. Perform the data preprocessing if needed and apply regular differncing,seasonal adjustment,log transformation.
4. Plot the data according to need, before and after regular differncing,seasonal adjustment,log transformation.
5. Display the overall results.
   
### PROGRAM:
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose

data = pd.read_excel('/content/temperature_data.xlsx')

# Fix 1: Change 'date' to 'Date' (case sensitivity issue)
# Fix the ValueError: day is out of range for month, at position 24 by coercing errors
data['Date'] = pd.to_datetime(data['Date'], errors='coerce')
data.set_index('Date', inplace=True)

# Fix 2: Change 'money' to 'Temperature' as 'money' column does not exist
data['Temperature_diff'] = data['Temperature'] - data['Temperature'].shift(1)

# Adapt seasonal decomposition to use 'Temperature' and new column names
# Note: Irregular dates and insufficient data might still cause issues for seasonal_decompose.
# Using .mean() for aggregation as Temperature is not a sum-like quantity.

daily_temp = data.groupby(level=0)['Temperature'].mean().dropna()

try:
    daily_result = seasonal_decompose(daily_temp, model='additive', period=12)
    daily_residuals = daily_result.resid.rename('temp_sea_diff')
except ValueError as e:
    print(f"Warning: Seasonal decomposition for daily_temp failed: {e}. Filling with NaNs.")
    daily_residuals = pd.Series(np.nan, index=daily_temp.index).rename('temp_sea_diff')

data = data.reset_index()
data = pd.merge(data, daily_residuals.reset_index(), on='Date', how='left')
data.set_index('Date', inplace=True)

data['Temperature_log'] = np.log(data['Temperature'])
data['Temperature_log_diff'] = data['Temperature_log'] - data['Temperature_log'].shift(1)

daily_temp_log_diff = data.groupby(level=0)['Temperature_log_diff'].mean().replace([np.inf, -np.inf], np.nan).dropna()

try:
    daily_log_result = seasonal_decompose(daily_temp_log_diff, model='additive', period=6)
    daily_log_residuals = daily_log_result.resid.rename('temp_log_seasonal_diff')
except ValueError as e:
    print(f"Warning: Seasonal decomposition for daily_temp_log_diff failed: {e}. Filling with NaNs.")
    daily_log_residuals = pd.Series(np.nan, index=daily_temp_log_diff.index).rename('temp_log_seasonal_diff')

data = data.reset_index()
data = pd.merge(data, daily_log_residuals.reset_index(), on='Date', how='left')
data.set_index('Date', inplace=True)

plt.figure(figsize=(16, 16))

plt.subplot(6, 1, 1)
plt.plot(data['Temperature'], label='Original')
plt.legend(loc='best')
plt.title('Original Temperature Data')
plt.xlabel('Date')
plt.ylabel('Temperature')

plt.subplot(6, 1, 2)
plt.plot(data['Temperature_diff'], label='Regular Difference')
plt.legend(loc='best')
plt.title('Regular Differencing of Temperature')
plt.xlabel('Date')
plt.ylabel('Differenced Temperature')

plt.subplot(6, 1, 3)
plt.plot(data['temp_sea_diff'], label='Seasonal Adjustment')
plt.legend(loc='best')
plt.title('Seasonal Adjustment of Temperature')
plt.xlabel('Date')
plt.ylabel('Seasonally Adjusted Temperature')

plt.subplot(6, 1, 4)
plt.plot(data['Temperature_log'], label='Log Transformation')
plt.legend(loc='best')
plt.title('Log Transformation of Temperature')
plt.xlabel('Date')
plt.ylabel('Log(Temperature)')

plt.subplot(6, 1, 5)
plt.plot(data['Temperature_log_diff'], label='Log Transformation and Regular Differencing')
plt.legend(loc='best')
plt.title('Log Transformation and Regular Differencing of Temperature')
plt.xlabel('Date')
plt.ylabel('RDiff(Log(Temperature))')

plt.subplot(6, 1, 6)
plt.plot(data['temp_log_seasonal_diff'], label='Log Transformation and Regular Differencing and Seasonal Differencing')
plt.legend(loc='best')
plt.title('Log Transformation, Regular, and Seasonal Differencing of Temperature')
plt.xlabel('Date')
plt.ylabel('SDiff(RDiff(Log(Temperature)))')

plt.tight_layout()
plt.show()

# This line will plot all numeric columns against the index (Date)
data.plot(kind='line')
```

### OUTPUT:

<img width="939" height="700" alt="image" src="https://github.com/user-attachments/assets/8b012bc7-3d43-4800-84c5-645b26ab5668" />



REGULAR DIFFERENCING:

<img width="1314" height="221" alt="image" src="https://github.com/user-attachments/assets/71dbd851-43e7-4e56-993e-802dfa986e85" />



SEASONAL ADJUSTMENT:

<img width="1308" height="220" alt="image" src="https://github.com/user-attachments/assets/00c43bd2-2a8e-4d8c-951c-fbc0df4ae855" />


LOG TRANSFORMATION:

<img width="1308" height="662" alt="image" src="https://github.com/user-attachments/assets/c20dfb71-c7f4-4d50-9858-98f4947ae226" />




### RESULT:
Thus we have created the python code for the conversion of non stationary to stationary data on international airline passenger
data.
