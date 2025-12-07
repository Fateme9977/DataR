# DataR
# RECS 2020 – Data Assets for Heat Pump Retrofit Project

This README documents **exactly which RECS 2020 files** are needed for the project  
“Techno-Economic Feasibility and Life-Cycle Optimization of Heat Pump Retrofits in Aging U.S. Housing Stock”.

---

## 1. Core Microdata (Main Dataset)

These files come from the **RECS 2020 Microdata** section (not the “Characteristics” tables).

### 1.1. Public-use Microdata File (CSV)

- **File (required)**:  
  - `recs2020_public_vX.csv`  
    - (Version number may be `v1`, `v2`, `v3`, etc. – always use the **latest** available.)

- **Purpose**:
  - Household-level records (one row per home).
  - Contains variables needed for modeling:
    - Climate: `HDD65`, `CDD65`, region/division.
    - Heating fuel & equipment: `FUELHEAT`, `EQUIPM`, `EQUIPAGE`, etc.
    - Building characteristics: year built, housing type, square footage, windows, draftiness (`DRAFTY` or equivalent), etc.
    - Energy consumption & expenditures: natural gas (Therms, dollars) and electricity (kWh, dollars) – e.g. `NGUSED`, `DOLNG`, `KWH`, `DOLEL`.
  - This is the **main input** to:
    - XGBoost model for thermal intensity.
    - Definition of envelope efficiency classes.
    - Household-level techno-economic calculations.

> Without this CSV, nothing else in the workflow can run.

---

## 2. Supporting Documentation for Microdata

These are also from the **Microdata** section and describe how to use the CSV correctly.

### 2.1. Codebook

- **File (required)**:
  - `RECS 2020 Codebook for Public File - v7.xlsx` (or latest version)

- **Purpose**:
  - Full description of every variable in `recs2020_public_vX.csv`.
  - Includes:
    - Variable names, labels, units.
    - Category codes for categorical variables (e.g., values for `FUELHEAT`, `DRAFTY`, `EQUIPAGE`, etc.).
  - Needed to:
    - Select correct features for machine learning.
    - Interpret categories when defining retrofit archetypes and envelope classes.

### 2.2. Microdata User Guide (Weights/RSE)

- **File (required)**:
  - `2020 RECS Microdata User’s Guide` (PDF – sometimes named `microdata.pdf` or similar)

- **Purpose**:
  - Explains how to:
    - Apply **household weights** (`NWEIGHT` or equivalent) to make national estimates.
    - Use **replicate weights** to compute standard errors and RSEs.
  - Needed for:
    - Aggregating results to census division / national level.
    - Validating model outputs against official published tables.
    - Producing statistically defensible “tipping point” maps.

### 2.3. Methodology / Square Footage / CE Methodology (Optional but Recommended)

- **Files (recommended)**:
  - `2020 RECS Methodology Report.pdf`
  - `2020 RECS Square Footage Methodology.pdf`
  - `2020 RECS CE Methodology - Monthly Appendix - Final.pdf`

- **Purpose**:
  - Documentation of:
    - Survey design and sampling.
    - How **square footage** is defined and computed.
    - How consumption & expenditures are estimated.
  - Useful for:
    - Writing methodology & limitations section.
    - Explaining uncertainty and survey design in the thesis.

---

## 3. Summary Tables from “Characteristics” Page

These files come from the page:  
`https://www.eia.gov/consumption/residential/data/2020/index.php?view=characteristics`  

Here, you do **not** need everything. Only download the **XLSX** versions of the tables listed below for descriptive statistics and external validation.

---

### 3.1. Structural & Geographic Characteristics (HC2.x)

- **Files (recommended)**: all XLSX files for:
  - `HC2.1` – `HC2.9` (XLSX versions)

- **Why we need them**:
  - Show distributions of:
    - Housing unit type (single-family, multi-family, mobile home, etc.).
    - Year of construction.
    - Census region/division and climate region.
  - Uses:
    - To compare weighted microdata results with official EIA tables.
    - To describe the national building stock and regional heterogeneity in the thesis.

---

### 3.2. Space Heating Characteristics (HC6.x)

- **Files (recommended)**: all XLSX files for:
  - `HC6.1` – `HC6.9` (XLSX versions)

- **Why we need them**:
  - Official statistics on:
    - Main heating fuel shares (natural gas, electricity, fuel oil, propane, etc.).
    - Presence of **heat pumps** and other equipment.
  - Uses:
    - To check that the sample of gas-heated homes in our microdata matches published proportions.
    - To motivate the retrofit potential (how many homes still rely on fossil fuels).

---

### 3.3. Square Footage (HC10.x)

- **Files (recommended)**: all XLSX files for:
  - `HC10.1` – `HC10.16` (XLSX versions)

- **Why we need them**:
  - Published statistics on:
    - Distribution of square footage by region, building type, etc.
  - Uses:
    - Validate any **area-weighted** national scaling (e.g., total heated floor area).
    - Cross-check any intensity metrics (BTU/sqft/HDD) aggregated from microdata.

---

### 3.4. Income & Energy Insecurity (Optional but Useful)

#### 3.4.1. Household Demographics (HC9.x)

- **Files (optional but useful)**:
  - `HC9.1` – `HC9.9` (XLSX versions)

- **Uses**:
  - Describing household income and demographic structure.
  - If the thesis includes:
    - Distributional impacts of retrofit policies.
    - Ability to pay and energy burden.

#### 3.4.2. Household Energy Insecurity (HC11.x)

- **Files (optional but useful)**:
  - `HC11.1` (XLSX)

- **Uses**:
  - Connecting “tipping point” analysis to:
    - Energy poverty.
    - Difficulty paying energy bills.
  - Very useful for discussion and policy implications.

---

## 4. How These Files Map to the Methodology

### 4.1. XGBoost Thermal Intensity Model

- **Input**:
  - `recs2020_public_vX.csv` (microdata, with climate, building, and fuel variables)
- **Support**:
  - `RECS 2020 Codebook…xlsx` (variable definitions)
  - `microdata-user-guide.pdf` (correct weighting)
  - HC2.x, HC6.x, HC10.x (for validating sample & aggregates)

### 4.2. SHAP Analysis (Drivers of Inefficiency)

- **Input**:
  - Same microdata CSV + selected features (DRAFTY, WINDOWS, year built, housing type, etc.)
- **Support**:
  - Codebook (to interpret categorical variables)
  - HC2.x and HC10.x (to show that feature distributions match national stock)

### 4.3. NSGA-II Optimization (Retrofit + Heat Pump)

- **Input**:
  - Microdata-derived:
    - Thermal intensity estimates.
    - Climate severity (HDD65).
    - Energy prices (implied from expenditures/quantities if needed).
- **Support**:
  - Methodology PDFs (for correct interpretation of square footage and consumption).
  - HC6.x (for baseline fuel/technology mix).
  - HC10.x (for total floor area by region).

### 4.4. Tipping-Point Map (Feasibility Boundary)

- **Input**:
  - Household-level techno-economic outputs aggregated via weights.
- **Support**:
  - HC tables (for sanity checks).
  - Microdata guide (for proper variance estimation if needed).

---

## 5. Minimal Download Checklist

If storage/time is limited, at a minimum download:

1. `recs2020_public_vX.csv` (latest)  
2. `RECS 2020 Codebook for Public File - v7.xlsx` (or latest)  
3. `RECS 2020 Microdata User’s Guide` (microdata PDF)  
4. From Characteristics page (XLSX only):
   - `HC2.1` – `HC2.9`  
   - `HC6.1` – `HC6.9`  
   - `HC10.1` – `HC10.16`  

Optional but recommended for a stronger thesis:

5. `2020 RECS Methodology Report.pdf`  
6. `2020 RECS Square Footage Methodology.pdf`  
7. `2020 RECS CE Methodology - Monthly Appendix - Final.pdf`  
8. `HC9.x` and `HC11.1` XLSX tables (for income & energy insecurity analysis)


