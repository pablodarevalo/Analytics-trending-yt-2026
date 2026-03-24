# 📊 YouTube Global Dynamics: Analytics Snapshot (Feb 26, 2026)
Opportunities for value creation on platforms like YouTube are more accessible than ever. However, in such a dynamic and multifaceted environment, the competitive advantage lies in the ability to adapt to shifting audience behaviors.

Today, video success is no longer a guessing game. We have precise metrics—views, likes, and comments—to measure real-time impact. In a highly saturated market, business strategy cannot be left to chance; data-driven insights are the line between virality and irrelevance.

This project analyzes a Kaggle dataset capturing a 24-hour snapshot of global YouTube interactions (February 26, 2026) across 11 countries. The study aims to decode critical trends: Which content drives the most impact? How does regional participation vary? When are the peak interaction windows?

To answer these questions, a comprehensive ETL (Extraction, Transformation, and Loading) pipeline was implemented, focusing on data cleaning and normalization to eliminate bias and ensure a statistically robust analysis.
<img width="1015" height="381" alt="image" src="https://github.com/user-attachments/assets/4546e4ba-d0a1-44dc-98cb-a59915cc0243" />

## 🛠️ Methods Used
* **ETL Pipeline:** Regional dataset unification using `glob`.
* **Inferential Statistics:** Dispersion analysis and variance audit (Coefficient of Variation - CV).
* **Data Cleaning:** Outlier treatment and metric normalization.
* **Data Visualization:** Segmented analysis by genre and region.

## 💻 Technologies
* **Python** (Pandas, Numpy)
* **Seaborn & Matplotlib**
* **Git/GitHub**
* **Jupyter Notebooks**

## 🔍 Development & Analysis (Results)

### 1. Data Extraction & Unification
To ensure reproducibility and handle large volumes of data, I implemented an automated extraction pipeline using the kagglehub library. This allows the project to fetch the latest 24-hour global snapshot directly from the source.
```python 
import kagglehub
import pandas as pd
import os

# Professional API-based dataset download
path = kagglehub.dataset_download("bsthere/youtube-trending-videos-stats-2026")

# Inventory of regional files extracted
files = [f for f in os.listdir(path) if f.endswith('.csv')]
print(f"✅ ¡Success! Data ready. Files: {files}")
``` 
### 1.2 Data quantity to analyze:
```python 
import glob

all_files = glob.glob(os.path.join(path, "*.csv"))

conteiner = []
for filename in all_files:
    country_code = os.path.basename(filename).split('_')[0]
    df_temp = pd.read_csv(filename, index_col=None, header=0)
    df_temp['region'] = country_code
    conteiner.append(df_temp)

df_world = pd.concat(conteiner, axis=0, ignore_index=True)

print(f"Total world records: {len(df_world)}")
``` 
Finally we have 178.412 world records to analyze.

### 1.3 Data Cleaning & Regional Distribution :
```python 
df_world = df_world[df_world['region'] != 'data']
print(df_world['region'].value_counts())
```
During the initial inspection, I identified 13 rows labeled as 'data' which corresponded to metadata files (e.g., data dictionaries) rather than actual video trends. These were filtered out to maintain the dataset's integrity.
After cleaning the data, the final record distribution per region is:
```
Region
GB    16400
US    16400
BR    16200
CA    16200
DE    16200
FR    16200
JP    16200
KR    16200
RU    16200
IN    16199
MX    16000
```

---
### 1.4 Column name´s to analyze 
```
print(df_world.columns)
```
As result we obtain  column names to analyze by country: 'video_id', 'trending_date', 'title', 'channel_title', 'views', 'likes','dislikes', 'publish_time', 'category_id', 'tags', 'comments', 'channel_id', 
'description', 'region', 'hour_published','engagement_rate', 'genre' .



### 1.5 Checking of null values
```
print("--- empty values by columns ---")
print(df_world.isnull().sum())

print(f"\nduplicate rows: {df_world.duplicated().sum()}")

print("\n--- kind of data ---")
print(df_world.dtypes)
```
The Check found 58 empty values from engagement rate column.
---

## 📬 Contact
* **Name:** Pablo Arévalo
* **Role:** Industrial Engineer | Data Analyst
* **LinkedIn:** www.linkedin.com/in/pablo-arevalo-8296b4199
