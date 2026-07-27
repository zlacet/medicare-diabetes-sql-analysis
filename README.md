# The Cost of Diabetes — Medicare Claims (SQL)

## Overview

A focused analysis of diabetes prevalence, comorbidities, and cost impact within the CMS DE-SynPUF Medicare sample population, using SQL against a combined 2008 to 2010 patient dataset built in SQLite.

## Data & Method

The data comes from CMS's [2008-2010 Data Entrepreneurs' Synthetic Public Use File (DE-SynPUF), Sample 1](https://www.cms.gov/data-research/statistics-trends-and-reports/medicare-claims-synthetic-public-use-files/cms-2008-2010-data-entrepreneurs-synthetic-public-use-file-de-synpuf/de10-sample-1), which provides three separate yearly beneficiary snapshots (2008, 2009, 2010) plus claims tables (inpatient, outpatient) spanning all three years. To analyze the full patient population rather than a single year, two SQL views were built on top of the raw tables: `all_ids`, every unique patient across the three beneficiary files, and `beneficiary_profile`, one row per patient combining all three years, with demographics from the most recent year on record, chronic condition flags marked present if flagged in any of the three years, and reimbursement totals summed across all three. This gives a single, de-duplicated view of the full population of 116,352 patients without discarding any year's data.

## Findings

Of the 116,352 patients in the combined population, 64,228, just over half, at 55.2%, were flagged as diabetic in at least one of the three years. That population skews slightly older (74.4 years average, versus 72.7 for non-diabetic patients) and slightly more female, with 36,792 women against 27,436 men. It is also heavily concentrated demographically: race code 1 alone accounts for 54,315 of the 64,228 diabetic patients, and geographically, with state code 5 leading at 5,392 patients, followed by state codes 10 and 45.

Diabetes rarely shows up alone. Ischemic heart disease appears in 91% of diabetic patients (58,392 of 64,228), congestive heart failure in 77% (49,618), and chronic kidney disease in 54% (34,414). A smaller but still substantial 7,427 diabetic patients also carry an End-Stage Renal Disease diagnosis. This comorbidity pattern is not incidental to the cost story, it drives it. Diabetic patients with none of CHF, CKD, or ischemic heart disease average $4,043 in total reimbursement; that figure climbs to $7,161 with one additional condition, $11,581 with two, and $26,278 for the 29,642 patients carrying all three. The relationship between comorbidity count and spending is close to linear, and by the time a diabetic patient has all three conditions, their average cost is more than six times that of a diabetic patient with none.

That comorbidity-driven cost shows up clearly at the population level: diabetic patients average $17,340 in total reimbursement across the three years, compared to $2,457 for non-diabetic patients, roughly seven times higher. Interestingly, this gap isn't coming from the size of individual hospital stays. Average inpatient payment per claim is nearly identical between diabetic ($9,541) and non-diabetic ($9,826) patients, and average length of stay is close as well (5.75 days versus 5.30 days). The gap instead shows up in outpatient spending, where diabetic patients average $276 per claim against $220 for non-diabetic patients, and in the sheer volume and combination of care diabetic patients require: 32,140 of them have both an inpatient and an outpatient claim on record, reflecting ongoing, multi-setting management rather than a single acute event.

The clinical detail bears this out. The most common inpatient diagnosis codes among diabetic patients include pneumonia, coronary atherosclerosis, septicemia, COPD with acute exacerbation, congestive heart failure, urinary tract infection, atrial fibrillation, and acute kidney failure, a list dominated by cardiovascular and renal conditions, consistent with the comorbidity burden identified above.

Measured year over year rather than combined, the diabetes rate moved from 37.87% in 2008 to 41.65% in 2009 to 29.25% in 2010, a fluctuation rather than a clean trend in either direction, worth noting as a limitation of a three-year synthetic sample rather than a real epidemiological signal.

## Tools

Python (pandas), SQLite, SQL (multi-table joins, `LEFT JOIN`, `UNION`, `COALESCE`, `CASE WHEN`, `julianday()` date functions, SQL views).
