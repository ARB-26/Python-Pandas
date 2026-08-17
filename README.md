# 🐼 Python Pandas: DataFrame Analysis

Week 6 project from the Leep Data Technician Skills Bootcamp. Uses the pandas library to build, clean, filter, slice, group, and visualise DataFrames — starting from raw Python data structures, then moving through a car fuel economy dataset, a penguin biology dataset, a student marks dataset, and a country-level GDP dataset.

## 📝 Overview

Five notebooks, each building on the last:

- Constructing DataFrames from scratch and exporting them
- Exploring, filtering, and transforming a 234-row car fuel economy dataset (`mpg.csv`)
- Identifying and handling missing data in a 344-row penguin measurements dataset (`penguins.csv`)
- Slicing and filtering a 35-row student marks dataset with `.loc[]` and `.iloc[]`
- Grouping, filling missing values, visualising, and removing outliers from a 223-country GDP per capita dataset

**Note:** none of the five datasets are retail or sales data — they're inventory items, car fuel economy, penguin biology, student exam marks, and country-level GDP figures. The same pandas skills below (filtering, slicing, grouping, cleaning, visualising) transfer directly to retail/sales data, but that's not what these notebooks practiced on.

## 🧠 Skills Gained

- **Creating DataFrames** – from lists of dictionaries, dictionaries of lists, a list of lists, and directly from CSV/Excel files with `pd.read_csv` / `pd.read_excel`
- **Exploring and inspecting datasets** – `.head()`, `.tail()`, `.sample()`, `.shape`, `.info()`, `.describe()`, `.dtypes`
- **Data cleaning and type casting** – stripping non-numeric characters from strings (e.g. `"$10.99"` → `10.99`) and converting to `float` for calculations
- **Filtering and slicing** – boolean filtering with comparison, `&` (AND), and `|` (OR) operators; label-based and position-based selection with `.loc[]` and `.iloc[]` (single cells, row/column ranges, list-based selection, and boolean-conditioned filters like `df.loc[df["mark"]>90, [...]]`); plus `.query()` and `.filter()` as alternative filtering syntax
- **Sorting and ranking** – `.sort_values()` on one or more columns, and `.nlargest()`/`.nsmallest()`
- **Grouping and aggregation** – `.groupby()` to compute a statistic per category, e.g. average GDP per region
- **Aggregate statistics** – `.describe()`, `.mean()`, `.median()`, `.std()`, and `.corr()`
- **Handling missing data** – `.isna()`/`.isnull()` to count and locate nulls, `.dropna()` to remove incomplete rows, and `.fillna()` to fill gaps with a calculated value instead of dropping them
- **Data visualisation and outlier detection** – matplotlib and seaborn: histograms, bar charts, scatter plots, boxplots, and correlation heatmaps; using the IQR (interquartile range) method to identify and filter out outliers
- **Column creation and transformation** – building new columns from arithmetic on existing ones, including a unit conversion (MPG to miles-per-litre) and a calculated fill value
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

### Task 4 — Slicing and Filtering a Student Marks Dataset with `.loc[]` and `.iloc[]`
Loaded a 35-row dataset of student exam marks (`id`, `name`, `class`, `mark`, `gender`) and practiced label-based vs. position-based indexing, list- and range-based selection, and condition-based filtering.

```python
# iloc: position-based selection
df.iloc[0]          # first row
df.iloc[0:3]         # first three rows
df.iloc[:, 1:3]      # all rows, columns 2-3

# loc: label-based selection, including boolean-conditioned filtering
df.loc[df["mark"] > 90, ["name", "class", "mark"]]
df.loc[(df["class"] == "Seven") & (df["mark"] >= 70), ["name", "class", "mark"]]

# sorting combined with filtering
df.sort_values(by="mark", ascending=False).head().loc[df["gender"] == "female", :]
```

Findings: 17 male and 16 female students across 8 classes (two gender values were missing), with "Seven" the largest class at 10 students.

### Task 5 — GroupBy, Missing Data, and Visualisation on a GDP per Capita Dataset
Loaded a [223-country dataset of GDP per capita estimates](https://www.kaggle.com/datasets/rajkumarpandey02/gdp-in-usd-per-capita-income-by-country) from three sources (IMF, World Bank, UN), then grouped by region, filled in missing estimates, visualised distributions, and removed outliers.

```python
# Grouping and aggregating by region
df.groupby("UN_Region")["IMF_Estimate"].mean().sort_values()

# Filling missing (zero) values with a calculated average of two other columns
df["IMF_Estimate"] = df["IMF_Estimate"].replace(0, np.nan)
df["avg_worldbank_un"] = df[["WorldBank_Estimate", "UN_Estimate"]].mean(axis=1)
df["IMF_Estimate"] = df["IMF_Estimate"].fillna(df["avg_worldbank_un"])

# Visualising distributions and correlation
df.hist(figsize=(10, 8))
sns.heatmap(corr, annot=True, cmap="Purples")
sns.barplot(x="UN_Region", y="IMF_Estimate", data=df, errorbar=None)

# Removing outliers using the IQR method
iqr = higher_q - lower_q
upper_boundary = higher_q + 1.5 * iqr
lower_boundary = lower_q - 1.5 * iqr
df_filtered = df[(df["UN_Estimate"] < upper_boundary) & (df["UN_Estimate"] > lower_boundary)]
```

Findings: Europe had the highest average IMF GDP-per-capita estimate by region, and Africa the lowest. 🇱🇺 Luxembourg topped the individual IMF ranking outright, at over $132,000 per capita. Removing outliers with the IQR method dropped 27 of the 223 countries and roughly halved the average UN estimate (from ~$17,767 to ~$9,415 per capita) — showing how much a small number of very high-income countries and territories skew the overall average.

## 🛠️ Tools

Python, pandas, NumPy, matplotlib, seaborn, Google Colab

## 🎓 About

Completed as part of Week 6 (Python Pandas) of the [Leep Talent Data Technician Skills Bootcamp](https://leepgroup.com), August 2026.
