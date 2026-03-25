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
```python
print(df_world.columns)
```
As result we obtain  column names to analyze by country: **'video_id'**, **'trending_date'**, **'title'**, **'channel_title'**, **'views'**, **'likes'**,**'dislikes'**, **'publish_time'**, **'category_id'**, **'tags'**, **'comments'**, **'channel_id'**, **'description'**, **'region'**, **'hour_published'**,**'engagement_rate'**, **'genre'**.



### 1.5 Checking of null values
```python
print("--- empty values by columns ---")
print(df_world.isnull().sum())

print(f"\nduplicate rows: {df_world.duplicated().sum()}")

print("\n--- kind of data ---")
print(df_world.dtypes)
```
The check found 58 empty values from engagement rate column.

### 2. 🛠️ Data Transformation & Cleaning
```python

#Columns and rows clean:
df_world = df_world.drop(columns=['column'], errors='ignore')
df_world = df_world.dropna(subset=['video_id', 'publish_time', 'views'])

#Imputing null descriptions to avoid losing those rows:
df_world['description'] = df_world['description'].fillna('No description')

# Date transformation (critical step) 
df_world['publish_time'] = pd.to_datetime(df_world['publish_time'], errors='coerce')
df_world['trending_date'] = pd.to_datetime(df_world['trending_date'], format='%y.%d.%m', errors='coerce')

#Extracting hour from the datetime object:
df_world['hour_published'] = df_world['publish_time'].dt.hour

#Calculating engagement rate : (likes + Commentaries) / Views
df_world['engagement_rate'] = (df_world['likes'] + df_world['comments']) / df_world['views']

#Verification:
print("✅ ¡ Clean and transformation done !")
print(f"total data processed: {len(df_world)}")
print("\nData kind update:")
print(df_world[['publish_time', 'hour_published']].dtypes)

#Preview: 
df_world[['title', 'region', 'hour_published', 'engagement_rate']].head()

```
✅ ¡ Clean and transformation done !
total data processed: 178399

Data kind update:
publish_time      datetime64[us, UTC]
hour_published                  int32
dtype: object

After cleaning and transformation we obtain 178,399 records (13 metadata rows removed from the original dataset), now fully cleaned and without null values.

### 3.📊 Data visualization 

### 3.1. Evaluation of publications global distribution by hour:
```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.set_theme(style="whitegrid")
plt.figure(figsize=(12, 6))

# Bar Graphic: 
sns.countplot(data=df_world, x='hour_published', palette='viridis', hue='hour_published', legend=False)

plt.title('Publications global distribution by hour', fontsize=16)
plt.xlabel('Hora del día (0-23)', fontsize=12)
plt.ylabel('Cantidad de Videos', fontsize=12)

plt.show()
```
<img width="1275" height="681" alt="image" src="https://github.com/user-attachments/assets/e31f00e1-3e79-4b78-83c5-069e2d334b12" />

Key Insights: Global Posting Peak
As visualized in the distribution, the "Global Rush Hour" for trending content occurs at 15:00 UTC.

- Core Interval: There is a critical high-activity window between 14:00 and 17:00 UTC.

- Strategic Takeaway: This peak suggests a massive synchronization of global creators aiming for the overlap between European evening audiences and North American morning viewers, maximizing the potential for viral growth.

- Efficiency: The sharp decline after 22:00 UTC reflects the end of the daily cycle for the Western Hemisphere’s digital activity.

### 3.2. The Engagement Paradox: Quantity vs. Interaction

```python
import matplotlib.pyplot as plt
import seaborn as sns

# We group by hour and calculate average engagement
hourly_engagement = df_world.groupby('hour_published')['engagement_rate'].mean().reset_index()

plt.figure(figsize=(12, 6))
sns.lineplot(data=hourly_engagement, x='hour_published', y='engagement_rate', 
             marker='o', color='red', linewidth=2.5)

plt.title('At what time are more interaction? (Average Engagement Rate by hour)', fontsize=16)
plt.xlabel('Publication hour (00:00 - 23:00)', fontsize=12)
plt.ylabel('Average Engagement Rate ', fontsize=12)
plt.xticks(range(0, 24)) 
plt.grid(True, alpha=0.3)

plt.show()

```
<img width="1271" height="684" alt="image" src="https://github.com/user-attachments/assets/afb1624d-6a33-43ae-92d0-1f4f6d26cf37" />

While the previous chart shows a massive upload peak at 15:00 UTC, this line chart reveals a completely different story regarding audience behavior.

- Maximum Interaction: The highest Average Engagement Rate occurs between 22:00 and 03:00 UTC.

- The "Saturated Market" Effect: At 15:00 UTC (the rush hour), engagement actually hits its lowest point. This proves that high competition for trending slots dilutes the audience's attention.

- Strategic Opportunity: For creators, the "Golden Window" for high-loyalty interaction is during global off-peak hours.

### 3.3. Top 3 Countries by Peak Activity
```python
# We order to obtain top three:
counts = df_world.groupby(['hour_published', 'region']).size().reset_index(name='video_count')
top_3_per_hour = counts.sort_values(['hour_published', 'video_count'], ascending=[True, False]).groupby('hour_published').head(3)

# 2. Graph
plt.figure(figsize=(18, 9)) 

sns.barplot(
    data=top_3_per_hour, 
    x='hour_published', 
    y='video_count', 
    hue='region', 
    palette='tab10',
    width=0.8 
)

plt.title('Top 3 with high activity', fontsize=18)
plt.xlabel('Publication hour (0-23)', fontsize=14)
plt.ylabel('Videos Quantity', fontsize=14)


plt.legend(title='Country / Región', bbox_to_anchor=(1.02, 1), loc='upper left', fontsize=10)
plt.grid(axis='y', alpha=0.3)

plt.show()
```
<img width="1719" height="812" alt="image" src="https://github.com/user-attachments/assets/d48baeb6-2dc7-4619-9741-b47fdee04142" />

The chart uses a multi-country distribution to visualize how the global trending volume is distributed; while some countries have specific peak hours, the FR, JP, and BR trio maintains the highest density of uploads.

---

## 📬 Contact
* **Name:** Pablo Arévalo
* **Role:** Industrial Engineer | Data Analyst
* **LinkedIn:** www.linkedin.com/in/pablo-arevalo-8296b4199
