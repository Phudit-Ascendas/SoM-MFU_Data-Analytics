# Low-Code Data Analytics with MATLAB: Online Retail Workshop

Workshop material for **Mae Fah Luang University, course 1203218 Business Statistics and Data Analytics**. Students are 2nd-year business students with no coding background. They take a messy online-retail order file from raw import to business insight, using **MATLAB Live Editor tasks** (point-and-click tools that write the code for you) and short one-line commands.

---

## Files

| File | Purpose |
|---|---|
| `LowCode_DataAnalytics_retail.mlx` | **Student worksheet.** A guided Live Script: headings, explanations and hints are written, and students fill in the empty code cells during the session. |
| `Solution_LowCode_DataAnalytics_retail.mlx` | **Answer key.** The same script with every cell completed and all outputs shown. |
| `online_retail_demo.xlsx` | **Workshop dataset.** 408 online-retail orders in 7 columns, made messy on purpose. |
| `SoMMFU.prj` + `resources/project/` | **MATLAB project "SoM MFU".** Opening it sets up the folder with all three files above. |

### MATLAB project

`SoMMFU.prj` is a MATLAB project named **SoM MFU**. Its definition is stored in `resources/project/`. The project:

- contains exactly the three files above;
- labels the two Live Scripts as **Design** files;
- adds the project root folder to the MATLAB path, and makes it the current folder when the project opens. The import step reads the dataset from the current folder (`.\online_retail_demo.xlsx`), so run the scripts from the project root.

Keep the `resources/` folder next to `SoMMFU.prj`, because the project cannot open without it.

## Requirements

- **MATLAB R2025a or newer.** The scripts were built and tested in R2026a.
- **Text Analytics Toolbox**, needed for the *Preprocess Text Data* task.
- Every other task (*Import Data*, *Pivot Table*, *Compute by Group*, *Clean Outlier Data*, *Create Plot*) is part of base MATLAB.

## Getting started

1. Open `SoMMFU.prj` in MATLAB (double-click it, or run `openProject("SoMMFU.prj")`). The **Project** window lists the three workshop files.
2. Open `LowCode_DataAnalytics_retail.mlx` in the Live Editor.
3. Work through the sections from top to bottom. Each empty code cell sits under a text instruction such as *"Open 'Import Data' task…"* or *"Hint: Pivot Table"*. Insert the Live Editor task it names from the **LIVE EDITOR → Task** gallery, or type the one-line command shown in the grey example box.
4. Run each section with **Run Section** (Ctrl+Enter) and read the output before moving on.

If you get stuck, compare your work with `Solution_LowCode_DataAnalytics_retail.mlx`.

---

## Workshop outline

The worksheet follows a five-stage pipeline: **Import → Assess → Clean → Explore → Conclude**. Each cleaning step follows the same pattern: **check, fix, confirm**.

### 1. Import the raw data
- **Task:** *Import Data* → browse to `online_retail_demo.xlsx`
- **Result:** `rawData` (408 rows × 7 columns)

### 2. Assess the data
- `summary(rawData)` shows what is wrong before anything is changed.
- The data is copied into `preData` for cleaning, so the raw table is never modified.

### 3. Clean the data

| Step | Problem in the data | Tool | Before → after |
|---|---|---|---|
| Remove duplicate orders | 8 repeated OrderIDs would count the same sale twice | `unique(..., "stable")` | 408 → 400 rows |
| Clean **ProductType** | Articles such as *"The"*, *"This"*, *"Our"*, *"a"* added in front of names | *Preprocess Text Data* → lemmatize + **Remove stop words** | 34 → 8 categories |
| Clean **Brand** | Stray punctuation (`-` `.` `!` `,` `( )`) | *Preprocess Text Data* → **Erase punctuation** | 30 → 5 categories |
| Fill missing **Brand** | 40 blanks | *Pivot Table* to diagnose, then *Compute by Group* (most common Brand per ProductType), then `ismember` to fill | 40 → 0 missing |
| Fill missing **UnitPrice** | 15 blanks, plus 6 extreme prices | *Clean Outlier Data* (mean, threshold factor 5) turns the 6 outliers into blanks; `ismissing` then finds all 21 and fills them with the median | 21 → 0 missing |

Text cleaning always follows the same shape: **tokenize → transform → rejoin** (`joinWords`), then `categorical` so that the column can be grouped and counted. The fully cleaned table is `clData`.

### 4. Business exploratory data analysis (EDA)
All EDA uses `clData`. Each question has a hint that names the task to use:

| Business question | Hint |
|---|---|
| Where do the orders come from? | Histogram |
| Which product type sells most? | Histogram |
| What is the dominant product type of each brand? | Pivot Table (heatmap) |
| How does brand preference vary across countries? | Pivot Table (percentage, stacked bar) |
| How does product type preference vary across countries? | Pivot Table (percentage, grouped bar) |
| What is our revenue? | Compute by Group (monthly sum), then Plot |
| How much does our top spender spend? | Compute by Group, Sort Rows (`sortrows`), then Plot (top 10 customers) |

---

## About the dataset

`online_retail_demo.xlsx` is synthetic. Its 7 columns are:

| Column | Content |
|---|---|
| OrderID | Order identifier (8 duplicates) |
| CustomerID | About 90 repeat customers, each linked to exactly one country |
| ProductType | 8 product types, polluted with stop words |
| Brand | 5 brands, polluted with punctuation, 40 blanks |
| Country | 5 countries, clean |
| OrderDate | January 2024 to December 2025 |
| UnitPrice | Order price, 15 blanks and a few extreme values |

Each type of mess is placed in a different column, so each Preprocess Text Data option has one clear effect. Brands also specialise by product type: each product type is sold mostly by one brand. This is why the Brand × ProductType heatmap shows a strong pattern, and why filling a missing brand with the most common brand for that product type is a sensible choice.
