# IAB ARCHITECTURAL FIRM Data Analysis

## project Overview:
This project involves cleaning, analyzing and visualizing data from the Institute of Architects Bangladesh (IAB). The dataset contains information on architectural firms, architects, graduates, members, and other related data points. The aim of this project is to gain insights into the distribution and characteristics of these firms and professionals within the architectural field in Bangladesh. The data is presented through various visualizations to provide a clear understanding of trends and patterns within the industry. The visualizations include bar charts, scatter plots and pie charts to convey insights such as the number of firms, their locations, the number of architects, and the availability of online information like ratings and websites.

## Objective:
- The objective of this analysis is to:
•	Understand the distribution of architectural firms across different regions (Thana) in Bangladesh.
•	Analyze the designations and roles within these firms.
•	Identify trends in firm establishment over the past five years.
•	Visualize the comparison between IAB Member Architect VS IAB Graduate. 
•	Evaluate the availability of digital resources (such as websites and ratings) for these firms, which can indicate their online presence and accessibility.

## Data Sources: 
-IAB Architectural data: The primary data used for this analysis is the "F:\Python dummy cleaning data.csv" containing detailed information about each of the IAB registered firm in Bangladesh. 

## Tools:
-Excel: Data Aggregation

-Python ( NumPy, Pandas, Matplotlib, re) for data cleaning, analysis and visualization.

## Data Cleaning: 

### 1. Data Loading and Inspection:
-Loaded the dataset into a Pandas DataFrame.
```python
import numpy as np
import pandas as pd
import os
file_path = r"F:\Python dummy cleaning data.csv"
if os.path.exists(file_path):
    print("File exists")
else:
    print("File does not exist")
```
```python
file_path = "F:\Python dummy cleaning data.csv"
encodings = ["utf-8", "ISO-8859-1", "latin1", "utf-16"]
for encoding in encodings:
    try:
        df = pd.read_csv(file_path, encoding=encoding)
        print(f"File successfully Encoded:{encodings}")
        break
    except UnicodeDecodeError:
        print(f"File failed to read: {encodings}")
```

-Inspected the first few rows, data types, and overall structure.
```python
df.head(5) 
```
```python
df.shape
```
```python
df.columns
```

### 2. Duplicate Removal:
-Checked and removed duplicates in key columns (iab_id, company_name, office_address) to ensure data integrity. Example:-
```python
df["IAB ID"].duplicated()
if df["IAB ID"].duplicated().any():
    print("There are Duplicates")
else:
    print("NO duplicates")
```
```python
df[df["IAB ID"].duplicated()==1]
```
```python
df[df["IAB ID"].isin(["IAB-RP-U01"])]
```
```python
df["IAB ID"] = df["IAB ID"].drop_duplicates()
```

### 3. Separate Column values:
-Separating column values using split(), replace()
```python
df['COMPANY NAME'].str.split(pat="(")
```
```python
df['COMPANY NAME'].str.split(pat="(").str[0]
```
```python
df['COMPANY NAME'] = df['COMPANY NAME'].str.split(pat="(").str[0]
```

### 4. Removing unnecessary Symbols/Characters:
-Removed unnecessary characters and symbols from columns.
```python
df["Establish year"] = df["Establish year"].str.replace("?", "-")
```

-Standardized values for consistency.

### 5. Column Name Fixes:
-Converted column names to lowercase with underscores.
```python
df.columns = df.columns.str.lower()
```
```python
df.columns = df.columns.str.replace(" ", "_")
```


### 6. Value Extraction Using RegEx:
-Used RegEx to extract specific information from columns (e.g., Thana ).
```python
import re
def extract_thana(address):
    if isinstance(address, str): # because there was null values in office_address
        match = re.search(r'\b([A-Za-z\s-]+),\sDhaka', address)
        if match:
            return match.group(1).strip()
    return "-"
df['thana'] = df['office_address'].apply(extract_thana)
```
-Created new columns for these extracted values.

### 7. Manual Data Entry:
-Manually filled in missing or incorrect values where needed.
```python
df.iloc[5, 15] = 'Dhanmondi'
```
```python
df.loc[10, "thana"] = "Gulshan"
```
### 8. Dropping Unnecessary Columns:
-Removed redundant columns that do not contribute to the analysis.
```python
df = df.drop('FULL TIME TECHNICAL PERSONNEL OF THE FIRM/COMPANY:', axis=1)
```
### 9. Merging Similar Values:
-Standardized similar values with different representations (e.g., variations in Thana names, Designation types).
```python
df["thana"] = df["thana"].str.replace('Khilkhat','Khilkhet')
```
```python
replacements = {'Green road': 'Green Road', 'West Panthapoth':'West Panthapath','New DOHShyamoli Mohakhali':'Mohakhali'}
df["thana"] = df["thana"].replace(replacements)
```
### 10. Year Grouping:
-Grouped establishment years into intervals (e.g., 5-year ranges).
```python
bins = [1975, 1980, 1985, 1990, 1995, 2000, 2005, 2010, 2015, 2020, 2025]
labels = ['1975-1979', '1980-1984', '1985-1989', '1990-1994', '1995-1999', 
          '2000-2004', '2005-2009', '2010-2014', '2015-2019', '2020-2024']
df['year_group'] = pd.cut(df['establish_year'], bins=bins, labels=labels, right=False)
```
-Created a year_group column for easier analysis.

### 11. Removing Rows with Null Key Values:
-Deleted rows with null values in essential columns like iab_id, company_name, or office_address.
```python
df = df.dropna(subset=['iab_id'])
```

### 12. Data Type Fixes:
-Ensured each column had the correct data type for efficient processing (e.g., int, float, object).
```python
df["iab_member_architect"] = pd.to_numeric(df["iab_member_architect"], errors='coerce').fillna(0).astype(int)
```

## Data analysis:
### 1. Top 10 Designation:
```python
designation_counts = df["designation"].value_counts().nlargest(10)
print(designation_counts)
```
```python
import matplotlib.pyplot as plt
plt.style.use('dark_background')
plt.figure(figsize=(10,6))
plt.bar(designation_counts.index, designation_counts.values, color="Blue")
plt.title("Designation Type", color = "White")
plt.xlabel("Designation", fontsize =14, color="White")
plt.ylabel("Count", fontsize = 14, color="White")
plt.xticks(rotation =45, ha='right', color = "white")
plt.yticks(color="White")
plt.show()
```
```python


### 2. Total Company by thana
```python
thana_count = df["thana"].value_counts().nlargest(10)
print(thana_count)
```
```python
plt.style.use('dark_background')
plt.figure(figsize=(10,6))
plt.bar(thana_count.index, thana_count.values, color ="Blue")
plt.title("Total Company by Thana", color = "White")
plt.xlabel("Thana Names", fontsize =14, color = "white")
plt.ylabel("Count", fontsize =14, color = "White")
plt.xticks(rotation=45, ha="right", color ="White")
plt.yticks(color="white")
plt.show()
```

### 3. Total Firms by year group
```python
year_group_counts = df['year_group'].value_counts()
print(year_group_counts)
```
```python
plt.style.use("dark_background")
plt.figure(figsize=(10,6))
plt.bar(year_group_counts.index, year_group_counts.values, color="Blue")
plt.title("Total Firms by Year", color="White")
plt.xlabel("Year group",fontsize=14, color="white")
plt.ylabel("Total Firms",fontsize=14, color="white")
plt.xticks(rotation = 45, ha = "right", color= "white")
plt.yticks(color= "White")
plt.show()
```

### 4. Rating Status
```python
rating_status_counts = df['rating_status'].value_counts()
print(rating_status_counts)
```
```python
plt.style.use("dark_background")
plt.figure(figsize=(8,8))
plt.pie(rating_status_counts, labels=rating_status_counts.index, autopct='%1.1f%%', colors=['Blue', 'Red'], startangle=90, explode=(0,0.02))
plt.title("Rating Status", color ="White")
plt.show()
```

### 5.Website Availability 
```python
web_av_counts = df["website_status"].value_counts()
print(web_av_counts)
```
```python
plt.style.use("dark_background")
plt.figure(figsize=(8,8))
plt.pie(web_av_counts, labels =web_av_counts.index, autopct='%1.1f%%', colors= ["Blue", "Red"], startangle=90, explode=(0,0.02))
plt.title("Website Status", color="White")
plt.show()
```
### 6. Architect Graduate vs IAB member Architect
```python
plt.figure(figsize=(10, 6))
plt.scatter(df['architectural_graduate'], df['iab_member_architect'], color='Red', edgecolor='black')
plt.xlabel('Architectural Graduates')
plt.ylabel('IAB Member Architects')
plt.title('Architectural Graduates vs. IAB Member Architects')
plt.grid(True)
plt.tight_layout()
plt.show()
```
### 7. Total Companies
```python
Total_Companies = df["company_name"].nunique()
print(Total_Companies)
```
### 8. Total IAB Member Architect
```python
Total_IAB_Member = df['iab_member_architect'].sum()
print(Total_IAB_Member)
```
### 9. Total IAB Graduate
```python
Total_IAB_Graduate = df['architectural_graduate'].sum()
print(Total_IAB_Graduate)
```
```python
Total_Member = df['total_member'].sum()
print(Total_Member)
```

## Result:

### 1.	Total Firms and Membership:
-	The dataset includes 134 companies, 451 IAB-registered architects, 584 IAB graduates, and 1,015 total members. This provides a comprehensive view of the professional architectural community represented within the IAB.

### 2.	Designation Distribution:
-	Principal Architect: The most common designation, with 40 individuals holding this title.

-	Managing Director: The second most frequent role, with 30 individuals.

-	Managing Partner & CEO:  A smaller group, with 13 individuals.

-	These findings suggest that senior leadership roles are well-represented in the IAB-registered firms, indicating a strong presence of experienced professionals.

### 3.	Firms by Thana:
-	Dhanmondi has the highest number of architectural firms, with 24 firms operating in this area.

-	Mohakhali follows with 16 firms, and Banani comes third with 14 firms.

-	These results indicate a high concentration of architectural activity in key urban areas of Dhaka, with significant business hubs in these regions.

### 4.	Firms by Year:
-	The period between 2010-2014 saw the largest growth in firm establishment, with 34 new firms registered.

-	This was followed by 2005-2009, with 33 firms, and 1995-1999, with 18 firms.

-	The data suggests a steady growth in the architectural sector over these years, with a notable peak between 2010-2014.

### 5. Google Map Rating Status:
-	58.2% of firms have been rated on Google Maps, indicating a level of public engagement and visibility.

-	However, 41.8% of firms are not rated, suggesting a potential gap in online presence or public feedback mechanisms.

### 6.	Website Availability:
-	53.7% of firms have websites, indicating that over half of the firms maintain an online presence.

-	However, 46.3% of firms do not have websites, reflecting a significant portion of the industry that may not be leveraging digital platforms for visibility and client engagement.

### 7. Architectural Graduate VS IAB Member Graduate:
-	Most frequency lies in between (7 to 10) which means most of the firms have less than & Architectural Graduate and less than 10 IAB Member Graduate

-	However,There are some outliers such as 46 and 58 Architectural Graduates. 

## Enhance Digital Presence:

-Websites: 46.3% of firms lack a website. Establishing an online presence can boost visibility, client engagement, and credibility.

-Google Maps Ratings: Encourage firms not rated on Google Maps to engage clients for reviews to enhance their reputation.
Focus on Emerging Areas:

-Urban Expansion: Explore opportunities in less saturated urban areas to tap into new markets and reduce competition.
Strengthen Leadership and Mentorship:

-Mentorship Programs: Leverage the experience of Principal Architects and Managing Directors to mentor the next generation of leaders within the IAB.
Capitalize on Growth Periods:

-Strategic Planning: Analyze the factors behind the 2010-2014 growth peak and replicate these strategies for future expansion.
Leverage Architectural Graduates:

-Graduate Development: Ensure firms with high numbers of graduates provide adequate opportunities and resources for their professional growth.

## Limitations:

-Incomplete Data: Some firms lack ratings or websites, which may lead to an incomplete analysis of their digital presence and client engagement.

-Geographical Focus: The dataset is heavily centered on specific urban areas, which may limit insights into architectural activity in less represented regions.

-Static Snapshot: The dataset provides a static view, and trends or changes over time may not be fully captured, potentially missing emerging patterns or shifts in the industry.

-Outlier Impact: The presence of outliers, such as firms with unusually high numbers of graduates, may skew the analysis, potentially leading to less generalized conclusions.





