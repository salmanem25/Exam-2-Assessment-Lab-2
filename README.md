## Econ 396 Exam 2/Assessment Lab 2 – Agricultural Data Analysis

### Overview

This repository contains the complete solution for **Exam 2 / Lab Assessment 2** of Econ 396 (Spring 2026). The analysis is performed in R Markdown and addresses six key data manipulation tasks using agricultural yield and irrigation demand data for corn grain and winter wheat across multiple counties in Washington State (Okanogan, Walla Walla, Yakima).

The main objectives are:

1. Add a `year` column from a combined date‑DOY string.
2. Remove outliers (irrigation demand > 90th percentile per county‑crop).
3. Split the cleaned data into two CSV files (irrigation demand and yield).
4. Re‑join the two files using `full_join`, `left_join`, and `right_join`.
5. Identify missing rows relative to the original dataset and impute missing irrigation demands with county‑crop averages.
6. Create a comparison data frame and a bar plot that contrasts original (outlier) vs. imputed irrigation demands.

All steps are fully reproducible and documented with clear comments suitable for a beginner who has learned R for only two weeks.

---

### File Structure

```
├── Exam 2 Input File.csv          # Raw input data (provided)
├── Exam_2_Assessment_Lab_2.rmd    # R Markdown script (the solution)
├── irrigation_demand.csv          # Output: filtered irrigation demand data
├── yield.csv                      # Output: filtered yield data
└── README.md                      # This file
```

> **Note**: Running the R Markdown file will generate the two CSV outputs and produce an PDF report with embedded plots.
 
---

### Requirements

To run this analysis, you need **R** (version ≥ 4.0) and the following R packages:

- `dplyr` – data manipulation
- `lubridate` – date handling
- `ggplot2` – visualisation
- `tidyr` – reshaping data

Install them with:

```r
install.packages(c("dplyr", "lubridate", "ggplot2", "tidyr"))
```

---

### How to Run

1. Clone this repository or download the files.
2. Place `Exam 2 Input File.csv` and `Exam_2_Assessment_Lab_2.rmd` in the same directory.
3. Open `Exam_2_Assessment_Lab_2.rmd` in RStudio.
4. Click **Knit** to generate the PDF report and execute all code chunks.
5. The CSV files (`irrigation_demand.csv` and `yield.csv`) will be written to the working directory.

---

### Step‑by‑Step Summary of the Analysis

#### 1. Add a `year` column
The original date column is in the format `"YYYY-MM-DD(DOY)"`.  
The script extracts the date part and uses `lubridate::ymd()` and `year()` to create a clean `year` integer column.

#### 2. Remove outliers
For each combination of `county_name` and `crop`, the 90th percentile of `irrigation_demand` is calculated. Any row with demand **above** this threshold is removed.  
This filters out unusually high irrigation demands that could distort subsequent analyses.

#### 3. Create two separate CSV files
From the filtered dataset, two files are saved:
- `irrigation_demand.csv` – contains `year`, `county_name`, `crop`, `lat_lon`, and `irrigation_demand`.
- `yield.csv` – contains `year`, `county_name`, `crop`, `lat_lon`, and `yield`.

Both files retain the same key columns for later merging.

#### 4. Perform joins
The two CSV files are read back into R, and three types of joins are applied on the four key columns (`year`, `county_name`, `crop`, `lat_lon`):
- **Full join** – all rows from both tables.
- **Left join** – all rows from `irrigation_demand`, matched rows from `yield`.
- **Right join** – all rows from `yield`, matched rows from `irrigation_demand`.

Because both files originate from the same filtered dataset, every row has a perfect match. Consequently, all three joins produce the **same number of rows** – no rows are lost or added.

#### 5. Identify missing rows and impute
The “full join” result contains only the filtered (non‑outlier) data. To assess completeness, the script compares it with the **original** input data.  
Rows present in the original but missing from the full join are exactly the outliers removed in Step 2. These are **explicit missing data** (we know why they are missing).

For each missing row, the original `irrigation_demand` (the outlier) is replaced with the **average irrigation demand** of its county‑crop group, calculated from the filtered (non‑outlier) data.  
All other columns (`year`, `county_name`, `crop`, `lat_lon`, `yield`) are retained. A complete dataset is created by binding the imputed rows to the filtered data.

#### 6. Comparison data frame and visualisation
A dedicated data frame (`missing_comparison`) is built that contains only the missing rows, showing:
- `irrigation_demand_original` – the actual outlier value.
- `irrigation_demand_filled` – the county‑crop average used for imputation.

A bar plot (using `ggplot2`) compares these two values for every outlier row.  
The plot clearly shows that the original demands are substantially higher than the imputed averages, confirming that the 90th percentile threshold effectively identifies extreme observations.

---

### Key Insights from the Visualisation

- Outliers (original irrigation demands) are consistently well above the county‑crop average.
- Imputed values are moderate and lie within the typical range of the non‑outlier data.
- While imputation makes the dataset complete, it reduces the demand values for those observations, which should be taken into account in any downstream modelling or inference.

---

### Outputs

| File | Description |
|------|-------------|
| `Exam_2_Assessment_Lab_2.pdf` | Knitted report containing all code, outputs, and plots. |
| `irrigation_demand.csv` | Filtered irrigation demand data (outliers removed). |
| `yield.csv` | Filtered yield data (outliers removed). |
| `complete_dataset` (in‑memory) | The final dataset with imputed missing demands. |
| `missing_comparison` (in‑memory) | Data frame used for the comparison plot. |

---

### Authors

- **Author**: Salman Rashid, Econ 396 – Spring 2026
- **Purpose**: Educational assignment for data manipulation and visualisation in R.

---

### Acknowledgements

Data provided as part of the course material. The analysis follows the instructions of the Exam‑Lab Assessment 2 strictly, with an emphasis on clarity for learners with minimal R experience.

For any questions or issues, please open an issue in this repository.
