# Collecting Job Data Using APIs

## Project Overview

In this project, I worked with a simulated Jobs API (as a part of a study capstone project) to collect data on job postings for various technologies and locations. The dataset comes from the source: https://www.kaggle.com/promptcloud/jobs-on-naukricom under a **Public Domain license**. The goal was to extract relevant information from a JSON API and store the results in an Excel spreadsheet using Python.

What I did:
- Collected job data using a public Jobs API.
- Parsed JSON responses to extract job counts.
- Created and saved an Excel file with the results using `openpyxl`.

---

## ⚙️ Steps Performed

### 1. Accessed the Jobs API
I used Python's `requests` module to fetch job data in JSON format from a provided API endpoint.

```python
import requests

api_url = "https://***/***/jobs.json"  # simulated API
response = requests.get(api_url)
data = response.json()
```

---

### 2. Parsed Job Data by Technology
I created a function to count the number of jobs for a given technology.

```python
def get_number_of_jobs_T(technology):
    response = requests.get(api_url)
    data = response.json()
    number_of_jobs = 0
    for job in data:
        tech_list = job.get('Key Skills')
        if technology.lower() in tech_list.lower():
            number_of_jobs += 1
    return technology, number_of_jobs
```

✅ *Tested with:*  
```python
get_number_of_jobs_T("Python")
```

---

### 3. Parsed Job Data by Location
I wrote a function to extract the number of job postings by location.

```python
def get_number_of_jobs_L(location):
    response = requests.get(api_url)
    if response.ok:
        data = response.json()
    number_of_jobs = 0
    for job in data:
        loc = job.get('Location')
        if location.lower() in loc.lower():
            number_of_jobs += 1
    return location, number_of_jobs
```

✅ *Tested with:*  
```python
get_number_of_jobs_L("Los Angeles")
```

---

### 4. Stored Results in Excel
Saved the collected job data into an Excel spreadsheet using `openpyxl`.

```python
!pip install openpyxl
import openpyxl
from openpyxl import Workbook

technologies = ["C", "C#", "C++", "Java", "JavaScript", "Python", "Scala", "Oracle", "SQL Server", "MySQL Server", "PostgreSQL", "MongoDB"]

wb = Workbook()
ws = wb.active
ws.title = "Job Data"
ws.append(['Technology', 'Number of jobs postings'])

for tech in technologies:
    technology, num_jobs = get_number_of_jobs_T(tech)
    ws.append([technology, num_jobs])

wb.save("job-postings.xlsx")
wb.close()
```

---

## 📁 Output

- **job-postings.xlsx**: Excel file containing job counts for each technology.
