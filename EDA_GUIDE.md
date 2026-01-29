# Exploratory Data Analysis Guide — CHML Forecasting

## Purpose
Before modelling, understand your data. EDA tells you what you're working with, reveals patterns, and flags problems. Don't skip it.

---

## 1. Load and Inspect (5 min)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv('synthetic_chml_data.csv', parse_dates=['Flight_Date'])

# Basic shape and types
print(f"Shape: {df.shape}")
print(df.dtypes)
print(df.head())
print(df.describe())
```

**Questions to answer:**
- How many rows/columns?
- Any missing values? (`df.isnull().sum()`)
- Data types correct? (dates as datetime, categoricals as object/category)

---

## 2. Target Variable: CHML_Ordered (10 min)

```python
# Distribution
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
df['CHML_Ordered'].hist(bins=30)
plt.title('Distribution of CHML Orders')
plt.xlabel('CHML_Ordered')

plt.subplot(1, 2, 2)
df['CHML_Ordered'].plot(kind='box')
plt.title('CHML Orders Boxplot')
plt.tight_layout()
plt.show()

# Stats
print(df['CHML_Ordered'].describe())
print(f"Skewness: {df['CHML_Ordered'].skew():.2f}")
```

**Questions to answer:**
- What's the range? (min, max)
- Is it skewed? (Poisson data often is)
- Any outliers?

---

## 3. Categorical Breakdowns (15 min)

### By Route
```python
df.groupby('Route')['CHML_Ordered'].agg(['mean', 'std', 'min', 'max', 'count'])
```

### By Holiday Period
```python
df.groupby('Is_Holiday_Period')['CHML_Ordered'].agg(['mean', 'std', 'min', 'max'])
```

### By Weekend
```python
df.groupby('Is_Weekend')['CHML_Ordered'].agg(['mean', 'std', 'min', 'max'])
```

### Visualise
```python
fig, axes = plt.subplots(1, 3, figsize=(14, 4))

sns.boxplot(data=df, x='Route', y='CHML_Ordered', ax=axes[0])
axes[0].set_title('CHML by Route')

sns.boxplot(data=df, x='Is_Holiday_Period', y='CHML_Ordered', ax=axes[1])
axes[1].set_title('CHML by Holiday Period')

sns.boxplot(data=df, x='Is_Weekend', y='CHML_Ordered', ax=axes[2])
axes[2].set_title('CHML by Weekend')

plt.tight_layout()
plt.show()
```

**Questions to answer:**
- Which route has higher demand?
- How much does holiday period affect demand?
- Does weekend matter?

---

## 4. Time Series View (10 min)

```python
# Plot over time, coloured by route
fig, ax = plt.subplots(figsize=(14, 5))

for route in df['Route'].unique():
    route_data = df[df['Route'] == route]
    ax.plot(route_data['Flight_Date'], route_data['CHML_Ordered'], 
            label=route, alpha=0.7)

ax.set_xlabel('Date')
ax.set_ylabel('CHML Ordered')
ax.set_title('CHML Demand Over Time')
ax.legend()
plt.show()
```

```python
# Monthly averages
df['Month'] = df['Flight_Date'].dt.to_period('M')
monthly = df.groupby(['Month', 'Route'])['CHML_Ordered'].mean().unstack()
monthly.plot(figsize=(12, 4), title='Monthly Average CHML by Route')
plt.ylabel('Avg CHML Ordered')
plt.show()
```

**Questions to answer:**
- Any obvious seasonality?
- Do peaks align with school holidays?
- Any trends over time?

---

## 5. Correlations (10 min)

```python
# Numeric correlations
numeric_cols = ['Load_Factor', 'Actual_Pax_Count', 'CHML_Ordered']
corr = df[numeric_cols].corr()
print(corr)

sns.heatmap(corr, annot=True, cmap='coolwarm', center=0)
plt.title('Correlation Matrix')
plt.show()
```

```python
# Scatter: Load Factor vs CHML
plt.figure(figsize=(8, 5))
plt.scatter(df['Load_Factor'], df['CHML_Ordered'], alpha=0.3)
plt.xlabel('Load Factor')
plt.ylabel('CHML Ordered')
plt.title('Load Factor vs CHML Demand')
plt.show()
```

**Questions to answer:**
- Does load factor predict CHML?
- Does passenger count predict CHML?
- Any surprising correlations?

---

## 6. Feature Interactions (10 min)

```python
# Holiday + Route interaction
holiday_route = df.groupby(['Is_Holiday_Period', 'Route'])['CHML_Ordered'].mean().unstack()
print(holiday_route)

# Visualise
holiday_route.plot(kind='bar', figsize=(8, 4))
plt.title('Mean CHML by Holiday Status and Route')
plt.ylabel('Mean CHML Ordered')
plt.xticks(rotation=0)
plt.show()
```

**Questions to answer:**
- Is the holiday effect stronger for one route?
- Any interactions worth including in the model?

---

## 7. Summary Checklist

Before moving to modelling, you should know:

- [ ] Target distribution (shape, range, skewness)
- [ ] Impact of Route on target
- [ ] Impact of Holiday Period on target (this should be big)
- [ ] Impact of Weekend on target
- [ ] Whether Load Factor / Pax Count adds predictive value
- [ ] Any data quality issues (missing values, outliers)
- [ ] Time patterns (seasonality, trends)

---

## Output

Save your EDA notebook with clear section headers and conclusions. This is documentation for future-you and anyone reviewing your work.

When you're done, you should be able to answer: **"What features matter, and roughly how much?"**

Then: linear regression.

---

*Time estimate: 45-60 minutes of focused work.*
