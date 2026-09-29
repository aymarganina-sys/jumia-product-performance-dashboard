# Jumia Product Performance Dashboard

An Excel case study for the DSEAfrica Data Lab: turning a raw export of 115 Jumia home, kitchen and tools listings into a clean dataset, a set of descriptive statistics and an interactive dashboard on pricing, discounts and customer reviews.

## Business problem

Jumia and its sellers set discounts and prices without knowing whether they move customer engagement. The dashboard answers:

- Do higher discounts bring more customer reviews?
- Do highly rated products cost more or less than the rest?
- Which listings perform well, and which need a different pricing or marketing strategy?

## Workbook contents (`Jumia_Dashboard_Step6_Dashboard.xlsx`)

| Sheet | Purpose |
|---|---|
| `Dashboard` | KPIs, top-10 tables, charts and three filters (rating, discount, price category) |
| `Raw_Data` | The original export, untouched (115 rows) |
| `Cleaned_Data` | Cleaned and enriched data (112 products) with calculated columns |
| `Cleaning_Log` | Every cleaning and enrichment decision |
| `Assumptions` | All thresholds (editable, blue text on yellow) and consistency checks |
| `Statistics` | Descriptive statistics and price extremes |
| `Analysis` | Correlations, averages by category, top-10 lists, outlier fences, scatter charts |
| `Segments` | Live lists of products that need attention or are performing well |

All figures are Excel formulas on `Cleaned_Data`, so they recalculate when data or thresholds change.

## Process

### 1. Cleaning (`Cleaned_Data`, `Cleaning_Log`)

- Kept the raw sheet untouched and built a cleaned copy.
- Removed **3 exact duplicate rows** (identical on all six columns): 115 to **112 rows**. Listings with the same title but different prices (Owl Keychain, Metal Dog Sculpture, Angle Measuring Tool) were kept as separate variants.
- Stripped the `KSh` prefix and thousands separators, converting both price columns to numbers.
- One listing shows price ranges (`KSh 1,620 - KSh 1,980` / `KSh 2,200 - KSh 3,200`). The **midpoint** was used (1,800 / 2,700) and the row flagged. Its reported 38% discount does not match the midpoints (33%), so the reported value was kept and flagged.
- Discount converted from text (`38%`) to a number.
- Review counts were stored as negatives, so the absolute value was taken. Blank reviews were set to 0 (no reviews).
- Ratings (`4.5 out of 5`) converted to numbers. Blank ratings were left **empty, not 0**, so they do not distort averages.
- Fixed the misspelt `Ratingd` header and trimmed extra spaces in product names.

### 2. Enrichment

| Column | Rule |
|---|---|
| Discount Amount | Old price - current price |
| Calc Discount / Discount Gap | Recomputed discount vs reported. The gap is at most 0.5 point on every row except the price-range row |
| Rating Category | Poor < 3; Average 3 to < 4; **Good 4 to < 4.5**; Excellent >= 4.5; blank = No Rating |
| Discount Category | Low < 20%; Medium 20-40%; High > 40% |
| Price Category | Budget < KSh 500; Mid-range 500-1,499; Premium >= 1,500 |

The brief's rating bands leave a gap between 4 and 4.5, so a fourth band, **Good**, was added. Lower bounds are inclusive and upper bounds exclusive. Price thresholds are round numbers close to the first and third quartiles of current price (493 and 1,670).

### 3. Descriptive statistics

| Measure | Value |
|---|---|
| Products | 112 |
| Average current price | KSh 1,186.89 (median 998.5) |
| Average old price | KSh 1,811.11 |
| Average discount | 36.78% (KSh 624.21) |
| Average rating | 3.89 / 5 (57 rated products) |
| Total reviews | 723 (55 products have none) |
| Most expensive | 32PCS Portable Cordless Drill Set, KSh 3,750 |
| Least expensive | 3PCS Single Head Knitting Crochet Sweater Needle Set, KSh 38 |

### 4. Analysis

Pearson correlations:

| Relationship | r | Reading |
|---|---|---|
| Discount % vs reviews (all products) | 0.01 | No relationship |
| Discount % vs reviews (rated products) | -0.14 | Weak, negative |
| Rating vs reviews | 0.06 | No relationship |
| Price vs rating | 0.11 | Weak, positive |
| Price vs discount % | -0.55 | Strong, negative |

Averages by category:

| Discount category | Products | Avg reviews | Avg rating |
|---|---|---|---|
| Low (< 20%) | 18 | 2.1 | 3.73 |
| Medium (20-40%) | 32 | 11.0 | 4.28 |
| High (> 40%) | 62 | 5.4 | 3.61 |

| Price category | Products | Avg reviews | Avg rating |
|---|---|---|---|
| Budget | 29 | 8.0 | 3.64 |
| Mid-range | 46 | 4.5 | 3.85 |
| Premium | 37 | 7.6 | 4.07 |

Segments (thresholds in `Assumptions`):

| Segment | Products |
|---|---|
| High discount (> 40%) but Poor rating (< 3) | 10 |
| High discount but no reviews | 32 (of 62 high-discount products) |
| Strong demand: >= 10 reviews and rating >= 4.5 | 9 |
| >= 10 reviews but only Average/Good rating | 10 |
| >= 10 reviews but Poor rating | 5 |

Outliers (1.5 x IQR): 3 on price, 10 on review count.

## Key findings and insights

1. **Higher discounts do not lead to higher engagement.** Correlation between discount and reviews is about zero. Medium discounts (20-40%) get the most reviews (11.0 per product) and the best ratings (4.28). Deep discounts above 40% average 5.4 reviews and 3.61 stars, and 32 of the 62 high-discount listings have no review at all.
2. **Rating and price barely relate to review volume.** Poor-rated products get about as many reviews (14.1) as Excellent ones (14.4), so a large review count does not mean satisfied customers.
3. **Premium products are rated slightly higher** (4.07 vs 3.64 for Budget), but the relationship is weak (r = 0.11).
4. **Expensive products are discounted less** (r = -0.55 between price and discount).
5. **Half of the catalogue is invisible in the feedback data**: 55 of 112 products have no reviews or ratings.

## Recommendations for Jumia sellers

1. **Prefer moderate discounts of 20-40% over deep cuts.** Medium-discount listings earn 11.0 reviews and a 4.28 rating on average, against 5.4 and 3.61 for discounts above 40%. Deep discounts did not buy engagement.
2. **Fix quality before discounting.** 10 listings combine a discount above 40% with a rating below 3 (for example the 5-piece stainless steel cooking pot set at 55% off, rated 2.1). The 120W cordless vacuum cleaner has the most reviews (69) but only 2.8 stars, so its exposure is working against it. Review these products before spending more on promotion.
3. **Turn proven products into winners and get the rest reviewed.** Nine products already show strong demand (>= 10 reviews, rating >= 4.5), such as the 137-piece cake decorating set (55 reviews, 4.6) and the digital vernier caliper (49 reviews, 4.6). Feature them, and encourage post-purchase reviews for the 55 listings that have none.

## Limitations

- The sample is small (112 products, only 57 rated), so correlations are indicative, not conclusive.
- Correlation is not causation, and review count is used as a proxy for engagement, not sales.
- The price-range listing uses midpoints, which is a judgement call.
- Rating and review-count thresholds are analyst choices and can be changed in `Assumptions`.
- Segments use fixed thresholds. Small counts (for example a 5.0 rating from 1-3 reviews) should be read with care.

## How to use the dashboard

1. Open the `Dashboard` sheet.
2. Choose a value in the yellow cells D4:D6 (Rating, Discount, Price category). "All" removes a filter.
3. Every KPI, top-10 table and category chart updates. The three scatter charts at the bottom always show all products.
4. Colour key: rating below 3 = red, 4.5 or more = green, discount above 40% = amber, blue bars = number of reviews.

### Adding PivotTables and native slicers in Excel

The workbook was generated programmatically, so PivotTables and native slicers are added in Excel (about 5 minutes):

1. In `Cleaned_Data`, click any cell of the data and choose **Insert > PivotTable**, on a new sheet.
2. Build pivots such as: rows = Discount Category, values = average Reviews and average Rating; rows = Price Category, values = average Rating; rows = Rating Category, values = count of Product.
3. With a pivot selected, choose **Insert > PivotChart** for each.
4. With a pivot selected, choose **Insert > Slicer** and tick Rating Category, Discount Category and Price Category.
5. Right-click each slicer, choose **Report Connections** and connect it to every pivot so one click filters them all.

## Repository structure

```
/
├── Jumia_Dashboard_Step6_Dashboard.xlsx   # workbook
├── Excel_jumia_dataset.csv                # raw export
└── README.md
```

## Tools

Microsoft Excel (formulas, conditional formatting, charts, data validation). Data preparation and checks were scripted in Python (pandas, openpyxl) and every workbook formula was recalculated and checked for errors.
