# Web Scraping 

## Overview
This document describes how I extracted structured data from a webpage and saved it into a CSV file for further analysis. The goal was to scrape the **name of the programming language** and the **average annual salary** from a given HTML table.

---

## Steps I Completed

### 1. **Analyzed the target webpage**
I first examined the page structure of the provided URL to understand the HTML layout and locate the target table containing the data.

**URL used is the part of the study capstone project:**
```python
url = "https://***/***/Programming_Languages.html"
```

---

### 2. **Imported the required libraries**
These libraries helped with HTTP requests, parsing HTML, and storing the data.
```python
from bs4 import BeautifulSoup
import requests
import pandas as pd
```

---

### 3. **Downloaded the webpage content**
I sent an HTTP GET request to retrieve the HTML content.
```python
data = requests.get(url).text
```

---

### 4. **Created a BeautifulSoup object**
This object allowed me to parse and navigate the HTML document.
```python
soup = BeautifulSoup(data, "html.parser")
```

---

### 5. **Scraped the target table data**
I located the table, extracted the rows, and captured only the required columns: language name and annual salary.
```python
table = soup.find('table')
rows = table.find_all("tr")
table_data = []
headers = [th.text.strip() for th in rows[0].find_all("td")]
headers1 = [headers[1], headers[3]]

for row in rows[1:]:
    cols = row.find_all('td')
    row_data = [cols[1].getText().strip(), cols[3].getText().strip()]
    table_data.append(row_data)

df = pd.DataFrame(table_data, columns=headers1)
```

---

### 6. **Saved the data to CSV**
The scraped data was saved in a CSV file for easy sharing and analysis.
```python
df.to_csv("popular-languages.csv", index=False, encoding="utf-8")
print("The data has been successfully saved to the file popular-languages.csv!")
```

---

## Output
The resulting `popular-languages.csv` file contains two columns:

- **Language name**
- **Average annual salary**

