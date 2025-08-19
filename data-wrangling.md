
# **Data Wrangling**

This document describes the data wrangling steps I performed to prepare raw survey data for analysis. The process involved cleaning, standardizing, imputing missing values, and transforming the data to make it suitable for further exploration and modeling.

---

## 1. Loaded the Dataset

I started by importing the necessary libraries and loading the dataset. I used pandas for data manipulation and matplotlib for data visualization.
I loaded a public Stack Overflow developer survey dataset from a URL and previewed the first few rows to confirm it loaded correctly.

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load the dataset from local disk

import zipfile

# Path to your ZIP file
zip_path = "survey_data.zip"
csv_filename = "survey_data.csv"  # name of the CSV inside the ZIP

# Open the ZIP and read the CSV directly
with zipfile.ZipFile(zip_path) as z:
    with z.open(csv_filename) as f:
        df = pd.read_csv(f)

# Display the first few rows to understand the structure of the data
df.head()
```

---

## 2. Explored the Dataset

I explored the dataset by examining the shape, data types, and missing values  to understand its structure and quality of the dataset.
Then I generated summary statistics to learn more about the distributions of numerical features.

```python
print(df.shape)
print(df.dtypes)
missing_values = df.isnull().sum().sort_values(ascending=False)
print(missing_values)
df.describe()
```

---

## 3. Identified and Removed Inconsistencies

To ensure accuracy, I checked if there are duplicate rows and cleaned up inconsistent values in the Country column.
There were no duplicates.

```python
print(f"Number of duplicate rows: {df.duplicated().sum()}")

country_dict = {
    'United States of America': 'USA',
    'United Kingdom of Great Britain and Northern Ireland': 'Great Britain'
}
df['Country'] = df['Country'].replace(country_dict)
```

Then I standardized values in the `EdLevel` column.

```python
educ_dict = {
    'Bachelor’s degree (B.A., B.S., B.Eng., etc.)': 'Bachelor’s degree',
    'Master’s degree (M.A., M.S., M.Eng., MBA, etc.)': 'Master’s degree',
    'Some college/university study without earning a degree': 'college/university (no degree)',
    'Secondary school (e.g. American high school, German Realschule or Gymnasium, etc.)': 'Secondary school',
    'Professional degree (JD, MD, Ph.D, Ed.D, etc.)': 'Professional',
    'Associate degree (A.A., A.S., etc.)': 'Associate',
}
df['EdLevel'] = df['EdLevel'].replace(educ_dict)
```

---


## 4. Handled Missing Values

I identified the columns with the most missing values and applied appropriate imputation techniques.

```python
missing_values = df.isnull().sum().sort_values(ascending=False).head(10)
print(missing_values)
```

Then I imputed missing values in numerical and categorical columns.
- For numerical columns (e.g., WorkExp), I filled missing values with the mean.
- For categorical columns (e.g., RemoteWork), I used the mode.
  
```python
# Numeric (WorkExp)
mean_exp = round(df['WorkExp'].mean(), 2)
df['WorkExp'] = df['WorkExp'].fillna(mean_exp)

# Categorical (RemoteWork)
most_frequent_value = df['RemoteWork'].mode()[0]
df['RemoteWork'] = df['RemoteWork'].fillna(most_frequent_value)
```

---

## 5. Feature Scaling and Transformation

To normalize the ConvertedCompYearly column, I applied Min-Max scaling.
To reduce skewness, I log-transformed the same column:

```python
# Created a new dataset
df_normalazed_comp = df[df['ConvertedCompYearly'].notnull()].copy()

# Normalozing Min-Max
min_value = df_normalazed_comp['ConvertedCompYearly'].min()
max_value = df_normalazed_comp['ConvertedCompYearly'].max()

# Added a new column 'ConvertedCompYearly_MinMax'
df_normalazed_comp['ConvertedCompYearly_MinMax'] = (
    (df_normalazed_comp['ConvertedCompYearly'] - min_value) / (max_value - min_value) )

import numpy as np
# Added a new column 'ConvertedCompYearly_Log'
df_normalazed_comp['ConvertedCompYearly_Log'] = np.log(df_normalazed_comp['ConvertedCompYearly'])
```

I also visualized both versions to compare distributions:

```python
plt.figure(figsize=(12, 6))
plt.subplot(1, 2, 1)
df_normalazed_comp['ConvertedCompYearly'].plot(kind='hist', bins=10, title='Original Data')
plt.subplot(1, 2, 2)
df_normalazed_comp['ConvertedCompYearly_Log'].plot(kind='hist', bins=10, title='Log-Transformed Data')
plt.show()
```

---

## 6. Feature Engineering

I created a new column `ExperienceLevel` based on `YearsCodePro` to categorize professionals by experience.

```python
df_normalazed_comp['YearsCodePro'] = pd.to_numeric(df_normalazed_comp['YearsCodePro'], errors='coerce')
# Function to assign Experience Level based on YearsCodePro
def assign_experience_level(years):
    if pd.isna(years):  
        return 'Unknown'
    elif years <= 2:
        return 'Beginner'
    elif 3 <= years <= 5:
        return 'Intermediate'
    else:
        return 'Advanced'

df_normalazed_comp['ExperienceLevel'] = df_normalazed_comp['YearsCodePro'].apply(assign_experience_level)
```
