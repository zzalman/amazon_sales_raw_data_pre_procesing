# amazon_sales_raw_data_pre_procesing
# Amazon Sales Dataset – Data Preprocessing & Cleaning

[![Python](https://img.shields.io/badge/python-3.8%2B-blue)](https://www.python.org/)
[![pandas](https://img.shields.io/badge/pandas-1.4%2B-green)](https://pandas.pydata.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains a complete data preprocessing pipeline for an Amazon product sales dataset. The original dataset (source: [GeeksforGeeks](https://media.geeksforgeeks.org/wp-content/uploads/20240517162110/amazon.csv)) includes product details, pricing, categories, and multiple user reviews stored in non‑atomic format. The goal of this project is to transform the raw data into a clean, analysis‑ready structure that can be used for exploratory data analysis (EDA), machine learning, and business intelligence.

---

## 📁 Dataset Overview

| Column Name          | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `product_id`         | Unique identifier for each product                                          |
| `product_name`       | Product title                                                               |
| `category`           | Hierarchical category path (e.g., `Electronics|Headphones|In‑Ear`)         |
| `discounted_price`   | Current selling price (with ₹ symbol and commas)                            |
| `actual_price`       | Original price before discount                                              |
| `discount_percentage`| Discount offered (e.g., `64%`)                                              |
| `rating`             | Average customer rating (e.g., `4.2`)                                       |
| `rating_count`       | Number of ratings (with commas)                                             |
| `about_product`      | Short product description                                                   |
| `user_id`            | Comma‑separated list of reviewer IDs                                        |
| `user_name`          | Comma‑separated list of reviewer names                                      |
| `review_id`          | Comma‑separated list of review IDs                                          |
| `review_title`       | Comma‑separated list of review titles                                       |
| `review_content`     | Comma‑separated list of review texts                                        |
| `img_link`           | URL to product image                                                        |
| `product_link`       | URL to product page                                                         |

### Key Challenges in Raw Data
- Prices, discounts, and ratings stored as strings with symbols.
- Category column contains up to 7 hierarchical levels separated by `|`.
- All review‑related columns are **not atomic** – multiple reviews per row, comma‑separated.
- Missing values in `rating_count`, `rating`, and `product_type`.
- Duplicate rows.

---

## 🎯 Project Objectives

1. **Convert** price, discount, and rating columns to proper numeric types.
2. **Parse** the nested category into three clean levels: `main_category`, `sub_category`, `product_type`.
3. **Transform** the dataset into an **atomic review‑level format** – one row per review, preserving all product metadata.
4. **Handle missing values** without losing significant data.
5. **Remove duplicates** and ensure data integrity.
6. Produce a clean, well‑structured dataset ready for downstream tasks.

---

## 🔧 Preprocessing Steps (What I Did)

### 1. Import Libraries & Load Data
```python
import pandas as pd
import numpy as np
df = pd.read_csv('amazon.csv')
```

### 2. Clean Numeric Columns
Removed currency symbols and commas, converted to `float`.
```python
df['discounted_price'] = df['discounted_price'].str.replace('₹','').str.replace(',','').astype(float)
df['actual_price']     = df['actual_price'].str.replace('₹','').str.replace(',','').astype(float)
df['discount_percentage'] = df['discount_percentage'].str.replace('%','').astype(float)
df['rating_count']     = pd.to_numeric(df['rating_count'].str.replace(',',''), errors='coerce')
df['rating']           = pd.to_numeric(df['rating'].replace('|', np.nan), errors='coerce')
```

### 3. Parse Category Hierarchy
- Split category column by `|`.
- Identified maximum depth (7 levels).
- Extracted first three levels into `main_category`, `sub_category`, `product_type`.
- For products with shorter paths, `product_type` was initially `NaN`; later filled with `sub_category` where appropriate.
- A sanity check confirmed that most `product_type` values appear in the product name.

### 4. Make the Dataset Atomic (One Review per Row)
- Review columns (`user_id`, `user_name`, `review_id`, `review_title`, `review_content`) were originally comma‑separated lists.
- Iterated through each row, split the lists, and expanded them so **each review gets its own row**.
- All product‑level information is duplicated appropriately, preserving the relationship between product and review.
- Final shape: **21,968 rows** × 19 columns (originally 1,465 rows).

### 5. Handle Missing Values & Empty Strings
- Dropped the few rows with missing `rating_count` (3 rows).
- Filled missing `product_type` with the corresponding `sub_category`.
- Replaced empty strings in `user_id`, `user_name`, `review_id` with `'Unknown'`.
- Replaced empty `review_title` with `'No Title'` and empty `review_content` with `'No Content'`.

### 6. Remove Duplicates
- Found and dropped 60 duplicate rows.

### 7. Final Dataset Shape
- **21,905 rows** after all cleaning.
- No missing values in any column; all textual placeholders are present.

---

## 📊 Results – What the Cleaned Data Looks Like

| product_id | product_name | main_category | sub_category | product_type | discounted_price | actual_price | rating | rating_count | review_title | review_content |
|------------|--------------|---------------|--------------|--------------|------------------|--------------|--------|--------------|--------------|----------------|
| B07JW9H4J1 | Wayona Nylon Braided USB… | Computers&Accessories | Accessories&Peripherals | Cables&Accessories | 399.0 | 1099.0 | 4.2 | 24269.0 | Satisfied | Looks durable… |
| B07JW9H4J1 | … (same product) | … | … | … | 399.0 | 1099.0 | 4.2 | 24269.0 | Charging is really fast | Charging is really fast |
| … | … | … | … | … | … | … | … | … | … | … |

Now the dataset is **atomic**, fully numeric where appropriate, and ready for:
- **Exploratory Data Analysis**
- **Sentiment Analysis** on reviews
- **Price & discount trend analysis**
- **Product recommendation systems**
- **Machine Learning models** (e.g., rating prediction)

---



## 📦 Requirements
- Python 3.8+
- pandas
- numpy

---

## 🙋‍♂️ About the Author

**Muhammad Salman Saleem**  
Data Science enthusiast, passionate about transforming messy data into actionable insights.

- **Instagram:** [@zzalman0](https://www.instagram.com/zzalman0/)
- **Facebook:** [zzalman0](https://web.facebook.com/zzalman0/)
- **LinkedIn:** [Muhammad Salman](https://www.linkedin.com/in/muhammadsalman010/)
- **GitHub:** [zzalman](https://github.com/zzalman)
- **Kaggle:** [zzalman](https://www.kaggle.com/zzalman)
- **Email:** salman793440@gmail.com

---


---

## 🙏 Acknowledgements
- Dataset originally provided by [GeeksforGeeks](https://www.geeksforgeeks.org/).
- Inspiration and code assistance from the open‑source community and AI tools (DeepSeek, ChatGPT) to explore best practices and validate decisions.

---

*Feel free to ⭐ the repository if you find it useful! Feedback and contributions are always welcome.*
