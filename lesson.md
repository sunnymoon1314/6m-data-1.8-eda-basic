# 🎓 Lesson 1.8: EDA Basic — Instructor Guide

## Session Overview

| Item | Detail |
|------|--------|
| **Duration** | 3 hours |
| **Format** | Flipped Classroom + Guided Coding in Jupyter |
| **Prerequisites** | Pandas fundamentals (Lesson 1.7); basic statistics concepts (pre-class) |
| **Tools** | VS Code + `pds` conda environment; or Google Colab |
| **Notebook** | `notebooks/eda_basic.ipynb` |

### Agenda

| Time | Section | Focus |
|------|---------|-------|
| 0:00 – 0:10 | Welcome & Pre-Class Review | Quick quiz; resolve setup issues |
| 0:10 – 1:00 | Part 1: Descriptive Statistics | Summarising data with Pandas |
| 1:00 – 1:05 | Break | — |
| 1:05 – 1:55 | Part 2: Data Quality | Handling missing values, duplicates, outliers |
| 1:55 – 2:00 | Break | — |
| 2:00 – 2:55 | Part 3: Data Transformation | Strings, categoricals, file I/O, regex |
| 2:55 – 3:00 | Wrap-Up | Takeaways & post-class assignment briefing |

> **Instructor Note:** Students should have the notebook open and the `pds` kernel selected before class begins. Resolve any environment issues in the first 10 minutes.

---

## 🏃 Part 1: Descriptive Statistics (50 min)

### 🎯 Learning Objective
Summarise a dataset using descriptive statistics and identify its shape, data types, and distributions.

### 📖 Theory Recap (10 min)

**Analogy:** Think of EDA as a doctor's check-up for your data. Before any treatment (modelling), you need to check the vital signs: How big is it? What types of columns? Are there anomalies?

The three first questions every data scientist asks:
- **How big is it?** → `.shape`, `.info()`
- **What does it contain?** → `.dtypes`, `.columns`, `.head()`
- **How is it distributed?** → `.describe()`, `.value_counts()`

Key distinction: `.describe()` only covers **numeric** columns by default. Use `.describe(include='all')` to include categoricals.

### 🛠️ Hands-On Activity: "The First Look" (30 min)

**Scenario:** You've just received a real-world dataset from a client. Your job is to understand it before any analysis begins.

**In the notebook, work through:**

1. Load the dataset and run `.info()` — what data types do you see? Are any surprising?
2. Run `.describe()` — identify the min/max/mean for numeric columns.
3. Use `.value_counts()` on a categorical column — what's the most common value?
4. Use `.nunique()` to count unique values per column — which columns might be IDs?

**Discussion Questions:**
- "Which column has the most missing values? How do you know?"
- "Why might the mean and median differ significantly for a column?"

### 💬 Q&A & Reflection (10 min)

- **Common Misconception:** "`.describe()` tells me everything I need." → It only covers numeric columns and doesn't reveal correlations, missing patterns, or outliers on its own.
- **Business Case:** Data Scientists report spending **60–80% of project time** on EDA and data cleaning — getting this stage right prevents costly errors downstream.

---

## 🏃 Part 2: Data Quality (50 min)

### 🎯 Learning Objective
Identify and handle missing values, duplicate records, and outliers using appropriate strategies.

### 📖 Theory Recap (10 min)

**Analogy:** Imagine a survey where some people left questions blank, one person filled it in twice, and one person claimed to be 150 years old. Those are your three data quality problems: **missing values**, **duplicates**, and **outliers**.

| Problem | Detection | Fix Options |
|---------|-----------|-------------|
| Missing values | `.isnull().sum()` | Drop rows (`.dropna()`), fill with value (`.fillna()`), or flag |
| Duplicates | `.duplicated().sum()` | Drop with `.drop_duplicates()` |
| Outliers | `.describe()`, box plots, IQR method | Drop, cap, or flag (context-dependent) |

**Key Decision Rule:** Never blindly drop data. Ask *why* it's missing or extreme — sometimes it's the most important data point.

### 🛠️ Hands-On Activity: "The Cleaning Run" (30 min)

**Scenario:** The dataset has been flagged by a client as "possibly dirty." Your task is to assess the damage and clean it.

**In the notebook, work through:**

1. Identify missing values with `.isnull().sum()` — which columns are affected?
2. Decide strategy per column: drop, fill with median/mode, or flag with a boolean column.
3. Remove duplicate rows and verify the count before/after.
4. Identify outliers using the IQR method: values below `Q1 - 1.5×IQR` or above `Q3 + 1.5×IQR`.

```python
Q1 = df['column'].quantile(0.25)
Q3 = df['column'].quantile(0.75)
IQR = Q3 - Q1
outliers = df[(df['column'] < Q1 - 1.5 * IQR) | (df['column'] > Q3 + 1.5 * IQR)]
print(f"Found {len(outliers)} outliers")
```

**Discussion Questions:**
- "For a hospital dataset, would you drop a row with a missing blood pressure reading? Why or why not?"
- "What's the difference between an outlier and a data error?"

### 💬 Q&A & Reflection (10 min)

- **Common Misconception:** "Filling missing values with the mean is always safe." → This can hide natural variation and bias your model. Median is often safer for skewed data.
- **Business Case:** Amazon estimated that bad data costs US businesses $3.1 trillion per year. A single undetected duplicate in a financial database can cause reporting errors that trigger regulatory issues.

---

## 🏃 Part 3: Data Transformation (50 min)

### 🎯 Learning Objective
Transform data through string cleaning, categorical operations, and reading/writing across file formats.

### 📖 Theory Recap (10 min)

**Analogy:** Raw data is like ingredients fresh from a market — some need washing, some need chopping, some need to be categorised. Transformation is your kitchen prep work before cooking.

Key transformation categories:
- **String operations:** `.str.strip()`, `.str.lower()`, `.str.replace()`, regex with `.str.extract()`
- **Categorical encoding:** `pd.Categorical()`, `.astype('category')`, dummy encoding
- **File I/O:** `pd.read_csv()`, `pd.read_json()`, `pd.read_excel()`, `df.to_csv()`

### 🛠️ Hands-On Activity: "The Standardisation Sprint" (30 min)

**Scenario:** Your dataset has inconsistent text formats, mixed case names, and poorly typed columns. Standardise it.

**In the notebook, work through:**

1. Use `.str.strip().str.lower()` to normalise text columns.
2. Use regex to extract numerical values from mixed strings (e.g., "£45.99" → 45.99):

```python
df['price_clean'] = df['price_raw'].str.extract(r'(\d+\.?\d*)').astype(float)
```

3. Convert a column to categorical type and check the memory saving with `.memory_usage()`.
4. Export the cleaned DataFrame to CSV and reload it to verify integrity.

**Discussion Questions:**
- "What happens if you try to calculate the mean of a column stored as a string?"
- "Why might you store a column as `category` type instead of `object`?"

### 💬 Q&A & Reflection (10 min)

- **Common Misconception:** "String and Object are the same thing in Pandas." → `object` is the default dtype for mixed/text columns; `string` dtype is newer and more efficient. Always convert text to `string` or `category` explicitly.
- **Business Case:** A major UK retailer discovered £2M in accounting errors traced back to inconsistent currency formatting — "£" vs "GBP" vs no symbol — in their sales data pipeline.

---

## 🎯 Wrap-Up (5 min)

### Key Takeaways
1. EDA is about **asking questions of your data** before assuming anything — always run `.info()` and `.describe()` first.
2. **Missing values, duplicates, and outliers** each require different strategies; the choice depends on domain context, not just code.
3. **Transformation is prep work**: clean column names, consistent types, and standardised formats save hours of debugging later.

### Next Steps
- **Post-Class:** Complete the [post-class.md](./post-class.md) exercises — 5 Pandas challenges + a cleaning scenario (45–60 min).
- **Next Lesson:** Lesson 1.9 builds on today's skills with advanced data wrangling — time series, merges, and pivot tables.
