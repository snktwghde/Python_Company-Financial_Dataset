# Company Financial Performance — EDA (Python / Pandas)

Exploratory analysis of a company financial dataset to identify which products and market
segments drive revenue and profit, and how discounting affects margin.

## Why this exists

Financial extracts arrive dirty: currency symbols inside numeric columns, thousands
separators, trailing spaces in headers, blank cells that pandas reads as objects. Before
any trend can be read, the file has to be made numeric. Most of this notebook is that
work — the charts are the easy part.

## Data

`Financials.csv` — **700 records, 16 columns.** Product-level financial records:

- Dimensions: Segment, Country, Product, Discount Band, Date / Month / Year
- Measures: Units Sold, Manufacturing Price, Sale Price, Gross Sales, Discounts, Sales,
  COGS, Profit

## Cleaning pipeline

The problems, and what fixed them:

| Problem | Fix |
|---|---|
| Column names carried leading/trailing whitespace | `df.columns.str.strip()` |
| `Units Sold`, `Manufacturing Price`, `Sale Price`, `Sales`, `Profit` stored as text with `$` and `,` | regex `.replace({'\$':'', ',':''}, regex=True)` |
| Blank / whitespace-only cells | `df.replace(r'^\s*$', 0, regex=True)` then `fillna(0)` |
| Residual non-numeric values after cleaning | `pd.to_numeric(..., errors='coerce')` on `Discounts` and `Profit` |
| `Date` as string | `pd.to_datetime(..., errors='coerce')` |

`errors='coerce'` is deliberate: it surfaces values that survived the regex pass as `NaN`
rather than silently failing the cast.

## Questions explored

1. How has revenue per segment changed over time?
2. How do product profits compare year over year?
3. Which segment's products contribute the most revenue?
4. Is Sales correlated with Profit, and does that relationship hold across years?
5. How do higher discount bands affect profit margin, per product?
6. Which months and quarters bring the highest revenue?
7. Which single product is the strongest by net profit?

Each is answered with a targeted chart (seaborn line, bar, and scatter plots) rather than
a generic dashboard, so the visual is chosen by the question.

## Stack

Python, Pandas, NumPy, Matplotlib, Seaborn

## Files

- `Finance-checkpoint.ipynb` — full notebook: cleaning, type coercion, EDA
- `Financials.csv` — source data

## Known limitations

- `fillna(0)` treats missing values as genuine zeros. For measures like `Discounts` that
  is defensible (no discount recorded = no discount); for `Profit` it is not, and those
  rows should be excluded rather than zero-filled. Flagged as a fix for the next pass.
- Analysis is descriptive only — no forecasting or significance testing on the observed
  segment differences.
