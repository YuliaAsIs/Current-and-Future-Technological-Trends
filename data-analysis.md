# **Technology Usage Trends Analysis**

To identify current trends in technology usage, I conducted an analysis
and prepared datasets for visualization. I used survey data and explored
programming languages, databases, platforms, and web frameworks.

------------------------------------------------------------------------

## 1. Programming Languages (Current Trends)

``` python
# Keep rows with non-null programming language data
lang_current = df.dropna(subset=['LanguageHaveWorkedWith'])
print(lang.shape)

# Split multiple languages per respondent
lang_current.loc[:, 'LanguageHaveWorkedWith'] = lang_current['LanguageHaveWorkedWith'].str.split(';')

# Flatten into rows
lang_exploded = lang_current.explode('LanguageHaveWorkedWith')

# Get top 10 most common languages
top_10_lang = lang_exploded['LanguageHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save to CSV
top_10_lang_current = top_10_lang.to_frame(name='LanguageCount')
top_10_lang_current.to_csv("Dash1_top_10_lang.csv", index=False)
```

------------------------------------------------------------------------

## 2. Databases (Current Trends)

``` python
# Keep rows with non-null database info
db_current = df.dropna(subset=['DatabaseHaveWorkedWith'])

# Split multiple databases per respondent
db_current.loc[:,'DatabaseHaveWorkedWith'] = db_current['DatabaseHaveWorkedWith'].str.split(';')

# Flatten into rows
db_exploded = db_current.explode('DatabaseHaveWorkedWith')

# Get top 10 most common databases
top_10_databases = db_exploded['DatabaseHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save
top_10_databases_current = top_10_databases.to_frame(name='Count')
top_10_databases_current.to_csv("Dash1_top_10_DB.csv", index=False)
```

------------------------------------------------------------------------

## 3. Platforms (Current Trends)

``` python
# Keep rows with non-null platform info
platf_current = df.dropna(subset=['PlatformHaveWorkedWith'])

# Split multiple platforms per respondent
platf_current.loc[:,'PlatformHaveWorkedWith'] = platf_current['PlatformHaveWorkedWith'].str.split(';')

# Flatten into rows
platf_exploded = platf_current.explode('PlatformHaveWorkedWith')

# Get top 10 platforms
top_10_platf = platf_exploded['PlatformHaveWorkedWith'].value_counts().head(10)

# Convert to DataFrame and save
top_10_platf_current = top_10_platf.to_frame(name='Count')
top_10_platf_current.to_csv("Dash1_top_10_platf.csv", index=False)
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

``` python
# Fields to process
fields = [
    "LanguageWantToWorkWith",
    "DatabaseWantToWorkWith",
    "PlatformWantToWorkWith",
    "WebframeWantToWorkWith"
]

# Loop through each field
for field in fields:
    # Keep rows with non-null values
    temp = df.dropna(subset=[field]).copy()
    
    # Split multiple values (assuming ';' separator)
    temp[field] = temp[field].str.split(';')
    
    # Flatten into rows
    temp_exploded = temp.explode(field)
    
    # Get top 10 items
    top_10 = temp_exploded[field].value_counts().head(10)
    
    # Convert to DataFrame
    top_10_future = top_10.to_frame(name="Count").reset_index()
    top_10_future.rename(columns={"index": field}, inplace=True)
    
    # Save to CSV (dynamic filename)
    filename = f"Dash2_top_10_{field[:-14].lower()}.csv"
    top_10_future.to_csv(filename, index=False)
```
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
