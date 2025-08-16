# **Technology Usage Trends Analysis**

To identify current trends in technology usage, I conducted an analysis
and prepared datasets for visualization. I used survey data and explored
programming languages, databases, platforms, and web frameworks.

------------------------------------------------------------------------

## Load the Survey Data

``` python
import pandas as pd

# Load the survey data from the provided URL
df = pd.read_csv(URL)
```

------------------------------------------------------------------------

## 1. Programming Languages (Current Trends)

``` python
# Keep rows with non-null programming language data
lang = df.dropna(subset=['LanguageHaveWorkedWith'])
lang.shape

# Split multiple languages per respondent
lang['LanguageHaveWorkedWith'] = lang['LanguageHaveWorkedWith'].str.split(';')

# Flatten into rows
lang_exploded = lang.explode('LanguageHaveWorkedWith')

# Get top 10 most common languages
top_10_lang = lang_exploded['LanguageHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save to CSV
top_10_lang_df = top_10_lang.to_frame(name='LanguageCount')
top_10_lang_df.to_csv("Dash1_top_10_lang.csv", index=False)
```

------------------------------------------------------------------------

## 2. Databases (Current Trends)

``` python
# Keep rows with non-null database info
db1 = df.dropna(subset=['DatabaseHaveWorkedWith'])

# Split multiple databases per respondent
db1['DatabaseHaveWorkedWith'] = db1['DatabaseHaveWorkedWith'].str.split(';')

# Flatten into rows
db1_exploded = db1.explode('DatabaseHaveWorkedWith')

# Get top 10 most common databases
top_10_databases = db1_exploded['DatabaseHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save
top_10_databases_df = top_10_databases.to_frame(name='Count')
top_10_databases_df.to_csv("Dash1_top_10_DB.csv", index=False)
```

------------------------------------------------------------------------

## 3. Platforms (Current Trends)

``` python
# Keep rows with non-null platform info
platf = df.dropna(subset=['PlatformHaveWorkedWith'])

# Split multiple platforms per respondent
platf['PlatformHaveWorkedWith'] = platf['PlatformHaveWorkedWith'].str.split(';')

# Flatten into rows
platf_exploded = platf.explode('PlatformHaveWorkedWith')

# Get top 10 platforms
top_10_platf = platf_exploded['PlatformHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save
top_10_platf_df = top_10_platf.to_frame(name='Count')
top_10_platf_df.to_csv("Dash1_top_10_platf.csv", index=False)
```

------------------------------------------------------------------------

## 4. Web Frameworks (Current Trends)

``` python
# Keep rows with non-null framework info
web_frame = df.dropna(subset=['WebframeHaveWorkedWith'])

# Split multiple frameworks per respondent
web_frame['WebframeHaveWorkedWith'] = web_frame['WebframeHaveWorkedWith'].str.split(';')

# Flatten into rows
web_frame_exploded = web_frame.explode('WebframeHaveWorkedWith')

# Get top 10 frameworks
top_10_web_frame = web_frame_exploded['WebframeHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save
top_10_web_frame_df = top_10_web_frame.to_frame(name='Count')
top_10_web_frame_df.to_csv("Dash1_top_10_web_frame.csv", index=False)
```

------------------------------------------------------------------------

## Identifying Future Trends

To identify **future trends**, I repeated the same analysis using the
following fields:

-   Programming languages → `LanguageWantToWorkWith`\
-   Databases → `DatabaseWantToWorkWith`\
-   Platforms → `PlatformWantToWorkWith`\
-   Web frameworks → `WebframeWantToWorkWith`

------------------------------------------------------------------------

## Comparing Current vs. Future Programming Languages

``` python
# Keep only the relevant columns
df_lang = df[['LanguageHaveWorkedWith', 'LanguageWantToWorkWith']].dropna()
df_lang.shape

# Separate current and desired languages
worked_languages = df['LanguageHaveWorkedWith'].dropna().str.split(';').explode()
wanted_languages = df['LanguageWantToWorkWith'].dropna().str.split(';').explode()

# Count frequencies
worked_counts = worked_languages.value_counts()
wanted_counts = wanted_languages.value_counts()

# Convert counts to DataFrames
worked_df = pd.DataFrame(worked_counts.items(), columns=['Language', 'Count_Worked'])
wanted_df = pd.DataFrame(wanted_counts.items(), columns=['Language', 'Count_Wanted'])

# Merge and compare
language_comparison = pd.merge(worked_df, wanted_df, on='Language', how='outer').fillna(0)
language_comparison.sort_values('Count_Worked', ascending=False, inplace=True)
```

------------------------------------------------------------------------

## Demographics

The data for demographics in the dashboard was taken from a **cleaned
and normalized survey dataset**.\
This helped ensure consistency across different categories of
respondents.

------------------------------------------------------------------------
