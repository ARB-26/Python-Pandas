# 🐼 Python Pandas: DataFrame Analysis

Week 6 project from the Leep Data Technician Skills Bootcamp. Uses the pandas library to build, clean, filter, and analyse DataFrames — starting from raw Python data structures, then moving on to a real-world fuel economy dataset and a real-world biological dataset with missing values.

## 📝 Overview

Three notebooks, each building on the last:

- Constructing DataFrames from scratch and exporting them
- Exploring, filtering, and transforming a 234-row car fuel economy dataset (`mpg.csv`)
- Identifying and handling missing data in a 344-row penguin measurements dataset (`penguins.csv`)

## 🧠 Skills Gained

- **Creating DataFrames** – from lists of dictionaries, dictionaries of lists, and directly from a CSV file with `pd.read_csv`
- **Data cleaning and type casting** – stripping non-numeric characters from strings (e.g. `"$10.99"` → `10.99`) and converting to `float` for calculations
- **Column creation and transformation** – building new columns from arithmetic on existing ones, including a unit conversion (MPG to miles-per-litre)
- **Boolean filtering** – using comparison, `&` (AND), and `|` (OR) operators to filter rows, and counting/summing Boolean columns directly
- **Sorting and ranking** – `sort_values` on one or more columns, and `nlargest`/`nsmallest`
- **Aggregate statistics** – `.describe()`, `.mean()`, `.median()`, `.std()`, and `.corr()`
- **Handling missing data** – `.isna()` to count and locate nulls by column and by row, and `.dropna()` to remove them
- **Exporting data** – `.to_csv()` and `.to_excel()`

## 🗂️ Task Breakdown

### Task 1 — Building and Exporting DataFrames
Practiced creating DataFrames three different ways (from a list of dictionaries, a dictionary of lists, and a list of lists), then explored DataFrame properties (`.shape`, `.dtypes`, `.info()`, `.describe()`) before working through an exercise: build a DataFrame of inventory items, clean a price column that started as text (`"$10.99"`), calculate total revenue, and export the result.

```python
# Cleaning a price column stored as text, then calculating revenue
df["price"] = df["price"].str.replace("$", "", regex=False).astype(float)
df["total_revenue"] = df["price"] * df["units_sold"]
df["total_revenue"] = df["total_revenue"].round(0)

# Exporting the result
df.to_csv("items.csv", index=False)
df.to_excel("items.xlsx", index=False)
```

### Task 2 — Exploring the MPG (Fuel Economy) Dataset
Loaded a 234-row dataset of car models and fuel economy figures, then practiced filtering, sorting, correlation, and building new calculated columns — including converting miles-per-gallon figures into miles-per-litre.

```python
# Boolean filtering with AND / OR
mpg[(mpg["class"] == "midsize") & (mpg["displ"] < 2)]
mpg[(mpg.model == "maxima") | (mpg.cyl == 5)]

# Row-wise average across two columns, then converting mpg to miles-per-litre
mpg["average_mileage"] = mpg[["cty", "hwy"]].mean(axis=1)
mpg_to_mpl = 0.425144
mpg["avg_mpl"] = ((mpg["cty"] + mpg["hwy"]) / 2) * mpg_to_mpl
```

```python
# Exercise: flag automatic vehicles, then calculate a weighted fuel economy figure
mpg["is_automatic"] = mpg["trans"].str.contains("auto")
mpg["is_automatic"].sum()  # 157 automatic vehicles out of 234

mpg["fuel_economy"] = mpg["cty"] * 0.55 + mpg["hwy"] * 0.45
median_fuel_economy = mpg["fuel_economy"].median()
mpg[mpg["fuel_economy"] > median_fuel_economy]
```

Findings: 157 of the 234 vehicles (67%) are automatic, and 14.96% of vehicles in the dataset are subcompacts. More cylinders and greater engine displacement both correlate with lower average mileage.

### Task 3 — Handling Missing Data in the Penguins Dataset
Loaded a 344-row dataset of penguin measurements (species, island, bill and flipper measurements, body mass) and practiced identifying, quantifying, and removing missing values.

```python
# Counting missing values by column, and the proportion missing by row
penguins.isna().sum()
percent_missing_by_row = penguins.isna().mean(axis=1)
percent_missing_by_row.sort_values(ascending=False)

# Removing incomplete rows
penguins.shape          # (344, 8) before
penguins.dropna(inplace=True)
penguins.shape          # (333, 8) after
```

Findings: the `sex` column had the most missing values (11), while the bill and flipper measurement columns each had 2. Dropping every row with any missing value reduced the dataset from 344 to 333 rows — an 11-row (3.2%) loss, small enough that dropping was a reasonable choice here rather than filling the gaps.

## 🛠️ Tools

Python, pandas, Google Colab

## 🎓 About

Completed as part of Week 6 (Python Pandas) of the [Leep Talent Data Technician Skills Bootcamp](https://leepgroup.com), August 2026.
