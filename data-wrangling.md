
<p align="center">
  <a href="https://skills.network" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo" />
  </a>
</p>

# **Data Wrangling Report**

This document describes the data wrangling steps I performed to prepare raw survey data for analysis. The process involved cleaning, standardizing, encoding, imputing missing values, and transforming the data to make it suitable for further exploration and modeling.

---

## 1. Loaded the Dataset

I started by importing the necessary libraries and loading the dataset using a direct URL.

```python
import pandas as pd
import matplotlib.pyplot as plt

dataset_url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/n01PQ9pSmiRX6520flujwQ/survey-data.csv"
df = pd.read_csv(dataset_url)
print(df.head())
```

---

## 2. Explored the Dataset

I explored the dataset to understand its structure and check for missing values.

```python
print(df.shape)
print(df.dtypes)
missing_values = df.isnull().sum().sort_values(ascending=False)
print(missing_values)
df.describe()
```

---

## 3. Identified and Removed Inconsistencies

I removed duplicates and handled inconsistencies in the `Country` column.

```python
print(f"Number of duplicate rows: {df.duplicated().sum()}")
df = df.dropna(subset=['Country'])

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

## 4. Encoded Categorical Variables

I encoded the `Employment` column using one-hot encoding.

```python
df_encoded = pd.get_dummies(df, columns=['Employment'], prefix='Employment')
```

---

## 5. Handled Missing Values

I identified the columns with the most missing values.

```python
missing_values = df.isnull().sum().sort_values(ascending=False).head(10)
print(missing_values)
```

Then I imputed missing values in numerical and categorical columns.

```python
# Numeric (WorkExp)
mean_exp = round(df['WorkExp'].mean(), 2)
df['WorkExp'] = df['WorkExp'].fillna(mean_exp)

# Categorical (RemoteWork)
most_frequent_value = df['RemoteWork'].mode()[0]
df['RemoteWork'] = df['RemoteWork'].fillna(most_frequent_value)
```

---

## 6. Feature Scaling and Transformation

I used Min-Max scaling and log transformation on the `ConvertedCompYearly` column.

```python
df_cleaned = df.dropna(subset=['ConvertedCompYearly'])
min_value = df_cleaned['ConvertedCompYearly'].min()
max_value = df_cleaned['ConvertedCompYearly'].max()

df_cleaned['ConvertedCompYearly_MinMax'] = (
    (df_cleaned['ConvertedCompYearly'] - min_value) / (max_value - min_value)
)

import numpy as np
df_cleaned['ConvertedCompYearly_Log'] = np.log(df_cleaned['ConvertedCompYearly'])
```

I visualized the effect of the log transformation.

```python
plt.figure(figsize=(12, 6))
plt.subplot(1, 2, 1)
df_cleaned['ConvertedCompYearly'].plot(kind='hist', bins=10, title='Original Data')
plt.subplot(1, 2, 2)
df_cleaned['ConvertedCompYearly_Log'].plot(kind='hist', bins=10, title='Log-Transformed Data')
plt.show()
```

---

## 7. Feature Engineering

I created a new column `ExperienceLevel` based on `YearsCodePro` to categorize professionals by experience.

```python
df_cleaned['YearsCodePro'] = pd.to_numeric(df_cleaned['YearsCodePro'], errors='coerce')

def assign_experience_level(years):
    if pd.isna(years):  
        return 'Unknown'
    elif years <= 2:
        return 'Beginner'
    elif 3 <= years <= 5:
        return 'Intermediate'
    else:
        return 'Advanced'

df_cleaned['ExperienceLevel'] = df_cleaned['YearsCodePro'].apply(assign_experience_level)
```

---

## ✅ Summary

- Loaded and explored survey data.
- Cleaned inconsistent and missing values.
- Encoded categorical features for analysis.
- Scaled and transformed numerical values.
- Engineered new features to extract more value from the dataset.

---

_This project demonstrates my ability to prepare messy real-world data for insightful analysis. Feel free to explore the code and reach out with questions or suggestions!_
