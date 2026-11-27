# Do Audit Red Flags Reveal Earnings Management and Hard-to-Forecast Earnings?

ACCT3112 Accounting Data Management and Analytics, The University of Hong Kong. Final project by Luk Jay and Tong Kai Yu Ava.

**Live report:** see the GitHub Pages site for this repository (Settings > Pages).

## Question

When a firm reports a material weakness in internal control under SOX Section 404, or restates its financial statements, it signals that something went wrong in how its numbers were produced. We ask two questions:

1. Do these firms engage in more earnings management?
2. Do analysts find their earnings harder to forecast?

## Data

All data come from Wharton Research Data Services (WRDS) and were downloaded directly into R. The data are licensed, so **no data files are included in this repository**. Anyone with WRDS access can rebuild the dataset by knitting the R Markdown file with their own login.

We used nine datasets:

| Source | Dataset | Used for |
|---|---|---|
| Audit Analytics | SOX 404 Internal Controls | Material weakness, opinion type (auditor or management), number of weaknesses |
| Audit Analytics | Financial Restatements | Restated periods, filing dates, restatement type |
| Compustat | Fundamentals Annual (with company header) | Accruals, firm characteristics, CIK and industry codes |
| I/B/E/S | Detail EPS | Individual analyst forecasts (robustness consensus) |
| I/B/E/S | Actual EPS | Realised earnings per share |
| I/B/E/S | Summary EPS | Consensus forecasts and number of analysts |
| CRSP | Monthly Stock File | Share prices, shares outstanding, return volatility |
| CRSP/Compustat | CCM Link Table | Matching Compustat firms to CRSP |
| I/B/E/S/CRSP | Link Table | Matching I/B/E/S firms to CRSP |

The sample covers U.S. non-financial, non-utility firms in fiscal years 2016 to 2025: 28,449 firm-years from 4,989 firms for the earnings management tests, and 23,192 firm-years for the analyst forecast tests.

## Methods

- **Earnings management:** absolute discretionary accruals from the modified Jones model, estimated separately for every two-digit SIC industry and year (`nest()` + `map()` + `lm()`). Total accruals are measured from the cash-flow statement.
- **Forecast accuracy:** absolute difference between actual EPS and the median analyst consensus, divided by share price. Unadjusted I/B/E/S data keep forecasts, actuals and prices on the same per-share basis.
- **Tests:** OLS regressions with year and industry fixed effects, controlling for size, leverage, market-to-book, profitability, losses, sales growth, operating cash flow and stock return volatility.
- **Robustness:** performance-adjusted accruals, restatements dated by announcement, dropping the two most recent years, a consensus built from individual analyst forecasts, and splits by opinion type, number of weaknesses and restatement type.

## Main findings

| Hypothesis | Coefficient | t-stat | Result |
|---|---|---|---|
| H1: Material weakness and larger discretionary accruals | 0.024 | 15.10 | Supported |
| H2: Restatement and larger discretionary accruals | 0.006 | 3.66 | Supported |
| H3: Material weakness and larger analyst forecast errors | 2.026 | 9.95 | Supported |
| H4: Restatement and larger analyst forecast errors | -0.313 | -1.59 | Not supported |

- Firms with a material weakness have discretionary accruals about 2.4% of total assets higher than comparable firms, and much larger analyst forecast errors. These results hold in every robustness check.
- The effect is strongest for weaknesses reported by management only, and grows with the number of weaknesses disclosed.
- Restated years show more earnings management in the main test, but this result is weaker in some robustness checks, and restatements are not linked to less accurate forecasts.

## Files

| File | Description |
|---|---|
| `index.html` | Project page shown on GitHub Pages |
| `ACCT3112_Project_Report.Rmd` | R Markdown source: downloads the data, builds the samples, runs all analysis |
| `ACCT3112_Project_Report.pdf` | Knitted report |
| `ACCT3112_Project_Poster.Rmd` / `.pdf` | Poster source and knitted poster |
| `references.bib` | Bibliography |
| `coef.png`, `trend.png` | Figures used on the project page |

## How to reproduce

1. Install the packages: `tidyverse`, `DBI`, `RPostgres`, `dbplyr`, `broom`, `knitr`, `kableExtra`, `stargazer`.
2. In the R console, run `Sys.setenv(WRDS_USER = "...", WRDS_PASSWORD = "...")` with your own WRDS login.
3. Run `rmarkdown::render("ACCT3112_Project_Report.Rmd")`. The first run downloads the data into a local `data/` folder (do not upload that folder).
