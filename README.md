# 🏠 Airbnb Paris Listings Analysis

## 📊 Project Overview

This project analyzes Airbnb listings in **Paris** using Python and explores how listing prices vary across neighbourhoods, accommodation capacity, and host registration over time.

The analysis focuses on:

* Data loading and preparation
* Data quality checks
* Paris listing filtering
* Average listing price by neighbourhood
* Average price by accommodation capacity
* New hosts over time
* Average listing price over time
* Visualization of trends using Matplotlib and Seaborn
* Exploring the potential impact of the **2015 Paris Airbnb regulations**

The project was completed using a combination of **Pandas, NumPy, Matplotlib, and Seaborn**.

---

## 🎯 Business Questions

The analysis aims to answer the following questions:

1. What is the average Airbnb listing price across different Paris neighbourhoods?
2. Which neighbourhood has the highest average listing price?
3. How does accommodation capacity affect the average listing price in the most expensive neighbourhood?
4. How has the number of new hosts changed over time?
5. How has the average listing price changed over time?
6. What patterns can be observed around the introduction of the 2015 regulations?

---

## 🗂️ Dataset

The analysis uses an Airbnb listings dataset containing **279,712 listings** and **33 columns**.

Important variables include:

| Column                 | Description                                  |
| ---------------------- | -------------------------------------------- |
| `listing_id`           | Unique listing identifier                    |
| `host_id`              | Unique host identifier                       |
| `host_since`           | Date the host joined Airbnb                  |
| `neighbourhood`        | Paris neighbourhood                          |
| `city`                 | Listing city                                 |
| `accommodates`         | Number of guests the listing can accommodate |
| `price`                | Listing price                                |
| `property_type`        | Type of property                             |
| `room_type`            | Type of room                                 |
| `bedrooms`             | Number of bedrooms                           |
| `review_scores_rating` | Listing rating                               |
| `minimum_nights`       | Minimum nights required                      |

The original dataset contains 33 columns, while the Paris-focused analysis uses five key variables:

`host_since`, `neighbourhood`, `city`, `accommodates`, and `price`.

---

## 🛠️ Tools & Technologies

* **Python**
* **Pandas** – data manipulation and analysis
* **NumPy** – numerical operations
* **Matplotlib** – data visualization
* **Seaborn** – statistical visualization
* **Jupyter Notebook**

---

## 🔄 Analysis Workflow

### 1. Import Libraries

The project starts by importing the main Python libraries used for data analysis and visualization.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

---

### 2. Load and Prepare the Dataset

The CSV file is loaded using Pandas.

Because the dataset had an encoding issue, `ISO-8859-1` encoding was used.

The `host_since` column was also converted to a datetime format during the loading process.

```python
listings = pd.read_csv(
    "Listings.csv",
    encoding="ISO-8859-1",
    low_memory=False,
    parse_dates=["host_since"]
)
```

The resulting dataset contains:

* **279,712 rows**
* **33 columns**

The `host_since` column is stored as a datetime data type.

---

## 🇫🇷 3. Filter Listings to Paris

The dataset was filtered to include only listings where:

```python
city == "Paris"
```

Only the following columns were retained:

```python
[
    "host_since",
    "neighbourhood",
    "city",
    "accommodates",
    "price"
]
```

This produced **64,690 Paris listings**.

---

## 🔍 4. Data Quality Analysis

Missing values were checked using:

```python
paris_listing.isna().sum()
```

The Paris dataset contained:

| Column          | Missing Values |
| --------------- | -------------: |
| `host_since`    |             33 |
| `neighbourhood` |              0 |
| `city`          |              0 |
| `accommodates`  |              0 |
| `price`         |              0 |

Therefore, the five analytical variables were largely complete, with only **33 missing values in `host_since`**.

The analysis also checked for listings where both `price` and `accommodates` were zero. The query returned **54 rows**.

---

# 📍 5. Average Price by Neighbourhood

The first major analysis calculates the average listing price for each Paris neighbourhood.

```python
paris_listings_neighbourhood = (
    paris_listing
    .groupby("neighbourhood")
    .agg({"price": "mean"})
    .sort_values("price")
)
```

### Visualization

A horizontal bar chart was created to compare average prices between neighbourhoods.

### Key Findings

The notebook shows substantial differences between neighbourhoods.

Some of the lower average prices were:

| Neighbourhood     | Average Price |
| ----------------- | ------------: |
| Menilmontant      |        €74.94 |
| Buttes-Chaumont   |        €82.69 |
| Buttes-Montmartre |        €87.21 |
| Reuilly           |        €89.06 |
| Popincourt        |        €90.56 |

Some of the higher average prices were:

| Neighbourhood  | Average Price |
| -------------- | ------------: |
| Luxembourg     |       €155.64 |
| Palais-Bourbon |       €156.86 |
| Passy          |       €161.14 |
| Louvre         |       €175.38 |
| Elysee         |       €210.54 |

The analysis therefore shows a clear difference in average listing prices across Paris neighbourhoods.

---

# 🏡 6. Accommodation Capacity vs. Price

The analysis then focuses on **Elysee**, the neighbourhood with the highest average price in the analysis.

Listings were grouped according to the number of guests they can accommodate.

```python
paris_listings_accomodations = (
    paris_listing
    .query("neighbourhood == 'Elysee'")
    .groupby("accommodates")
    .agg({"price": "mean"})
    .sort_values("price")
)
```

### Selected Results

| Accommodates | Average Price |
| -----------: | ------------: |
|            1 |        €79.52 |
|            2 |       €155.10 |
|            3 |       €152.83 |
|            4 |       €212.10 |
|           12 |       €529.63 |
|           13 |       €842.50 |
|           14 |       €971.00 |
|           16 |       €800.00 |

The results indicate that larger-capacity listings generally have higher average prices, although the relationship is not perfectly linear.

This analysis demonstrates how **listing capacity can be associated with pricing differences within a single neighbourhood**.

---

# 📈 7. Airbnb Hosts Over Time

The next part of the project examines how the number of new hosts changed over time.

The `host_since` date was converted into yearly periods using Pandas resampling:

```python
paris_listings_over_time = (
    paris_listing
    .set_index("host_since")
    .resample("YE")
    .agg({
        "neighbourhood": "count",
        "price": "mean"
    })
)
```

The `neighbourhood` count is used as a proxy for the number of listings/hosts represented in each year, while `price` represents the average listing price.

### Early Results

| Year | Count | Average Price |
| ---: | ----: | ------------: |
| 2008 |     4 |        €77.75 |
| 2009 |   106 |       €159.64 |
| 2010 |   416 |       €125.03 |
| 2011 | 1,339 |       €124.83 |
| 2012 | 4,592 |       €111.58 |

These results show substantial growth in the number of listings represented in the dataset during the early years.

---

# 📊 8. Visualizations

The project contains several visualizations.

### Average Price by Neighbourhood

A horizontal bar chart compares the average nightly price across Paris neighbourhoods.

### Average Price by Accommodation Capacity

A horizontal bar chart shows the relationship between the number of guests a listing can accommodate and its average price in Elysee.

### New Hosts Over Time

A line chart visualizes the number of listings/hosts represented by year.

### Average Price Over Time

A second line chart shows the yearly average listing price.

### Dual-Axis Analysis

A final visualization combines:

* Number of listings/hosts over time
* Average listing price over time

This allows the two trends to be viewed together.

---

# 🧠 Key Insights

### 1. Location is strongly associated with listing price

Average Airbnb prices vary considerably between Paris neighbourhoods. Elysee has the highest average price in this analysis at approximately **€210.54**, while Menilmontant has an average of approximately **€74.94**.

### 2. Larger-capacity listings tend to have higher prices

Within Elysee, average prices generally increase as accommodation capacity increases, particularly for larger listings.

### 3. Airbnb listing activity increased substantially in the early years

The number of listings represented in the dataset increased from 4 in 2008 to 4,592 in 2012.

### 4. Regulation analysis requires careful interpretation

The project investigates the period around the **2015 Airbnb regulations**. The time-series analysis can be used to identify changes in listing activity and prices around that period.

However, this dataset alone cannot establish that the regulations **caused** a particular change. Other factors could also influence Airbnb listings and prices.

---

# 📌 Business Questions This Analysis Could Support

This analysis could be useful for:

* Airbnb hosts deciding where to list properties
* Understanding neighbourhood-level pricing
* Comparing accommodation capacity and pricing
* Identifying historical changes in Airbnb activity
* Exploring regulatory impacts on the short-term rental market
* Supporting future pricing and market analysis

---

# 🚀 Future Improvements

This project can be extended by adding:

* Distribution of listing prices
* Median price by neighbourhood
* Price per guest
* Price per bedroom
* Room-type analysis
* Property-type analysis
* Review score analysis
* Superhost vs. non-Superhost comparison
* Correlation between reviews and price
* Geographic price analysis using latitude/longitude
* Outlier detection and treatment
* Interactive dashboards using **Power BI** or **Tableau**
* Regression analysis to identify factors associated with Airbnb prices

---

# 📁 Project Structure

```text
Airbnb-Paris-Analysis/
│
├── AirBNB_Analysis.ipynb
├── Listings.csv
├── README.md
└── images/
    ├── price_by_neighbourhood.png
    ├── price_by_accommodates.png
    ├── hosts_over_time.png
    ├── average_price_over_time.png
    └── hosts_vs_price.png
```

---

# 💡 Skills Demonstrated

This project demonstrates practical skills in:

**Python**

* Pandas
* NumPy
* Matplotlib
* Seaborn

**Data Analysis**

* Data loading
* Data cleaning
* Data type conversion
* Filtering
* Missing-value analysis
* Descriptive statistics
* GroupBy aggregation
* Sorting
* Time-series resampling
* Exploratory Data Analysis (EDA)

**Data Visualization**

* Horizontal bar charts
* Line charts
* Dual-axis charts
* Trend analysis

---

# 👨‍💻 Author

**Ahasan Hasan**

Logistics & Business Professional | Data Analytics

Currently developing skills in:

* Python
* SQL
* Excel
* Power BI
* Tableau
* Data Analytics

This project is part of my growing **Data Analytics portfolio**, combining business knowledge with practical data analysis skills.
