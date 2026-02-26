# 🚴 Cyclistic Bike-Share Case Study

## Phase 1 — ASK

### Business Task

Analyze behavioral differences between casual riders and annual members using historical trip data to identify usage patterns associated with remaining a casual rider and support data-driven marketing decision-making aimed at increasing membership conversion.

---

### Stakeholders

**Lily Moreno (Director of Marketing)**  
Responsible for developing and executing marketing campaigns. Focused on campaign effectiveness, ROI, data privacy compliance, and ensuring insights align with strategic business goals.

**Cyclistic Marketing Analytics Team**  
Responsible for collecting, analyzing, and reporting data to guide marketing strategy. Focused on data quality, analytical integrity, privacy adherence, and measurable business impact.

**Cyclistic Executive Team**  
Responsible for approving strategic initiatives. Focused on long-term company growth, profitability, risk mitigation, and ensuring recommendations are supported by credible analysis.

---

### Success Criteria

- Clear, quantified differentiation between casual riders and annual members based on behavioral and usage patterns.
- Statistically supported comparisons identifying meaningful trends.
- Identification of high-potential behavioral segments for conversion.
- Executive-ready insights supported by professional data visualizations.
- Actionable marketing recommendations backed by analytical evidence.

---

### Constraints

- Analysis limited to the previous 12 months of historical trip data.
- No use of personally identifiable information (PII).
- No demographic linking or inference beyond available trip data.
- Conclusions must rely on objective statistical comparisons rather than assumptions.
- Insights are limited to behavioral data and cannot establish direct causation.

## Phase 2 — PREPARE

### Dataset Description

The dataset contains historical trip-level data from Cyclistic’s bike-share program. Each row represents a single completed bike ride, including details about the ride type, start and end stations, geographic coordinates, timestamps, and rider classification (casual or annual member).

The data spans the previous 12 months and is organized into monthly CSV files. It captures operational ride behavior rather than individual customer profiles.

The dataset includes categorical variables such as `rideable_type`, `start_station_name`, `end_station_name`, and `member_casual`, as well as datetime variables (`started_at`, `ended_at`) and numerical variables (station latitude and longitude coordinates).

The dataset does not include personally identifiable information (PII), demographic data, or payment details. As a result, the analysis is limited to behavioral patterns observed at the trip level and cannot track individual users across multiple rides.

---

### Data Organization

The dataset consists of 12 separate monthly CSV files representing one full year of trip data. These files are stored locally within the project directory in Visual Studio Code and are version-controlled through GitHub.

To maintain data integrity and reproducibility, the project follows a structured folder hierarchy:

- `data/raw/` — Contains the original, unmodified monthly CSV files.
- `data/processed/` — Will contain merged and cleaned datasets prepared for analysis.

The raw data files are preserved to ensure traceability and to prevent accidental modification of source data during processing.

---

### ROCCC Evaluation

**Reliable**  
The dataset represents operational trip data collected directly from Cyclistic’s bike-share system. As transactional ride data, it reflects actual recorded usage rather than survey-based or self-reported information.

**Original**  
The data is made available by Motivate International Inc. under a data license agreement with Divvy Bikes. It represents first-party operational data.

**Comprehensive**  
The dataset includes detailed trip-level information such as ride duration, start and end stations, timestamps, bike type, and rider classification. While demographic data is not available, the dataset is sufficient for behavioral analysis.

**Current**  
The data covers the most recent 12-month period, making it relevant for identifying current usage trends and seasonal patterns.

**Cited**  
The dataset is publicly available under a stated license agreement, providing transparency regarding its source and permitted use.

---

### Initial Data Quality Observations

A preliminary review reveals missing values in the `end_station_name` and `end_station_id` columns. These will require further validation during the processing phase.

Column names appear consistent across the monthly files, facilitating future merging operations.

Both rider classifications — `member` and `casual` — are present in the `member_casual` column, confirming suitability for comparative analysis.

No obvious structural anomalies are immediately visible through manual inspection; however, additional validation checks such as ride duration consistency and duplicate detection will be performed during data processing.

The dataset spans 12 monthly files and contains a substantial number of ride records, providing sufficient volume for meaningful behavioral analysis once consolidated.

---

### Sufficiency for Business Task

The dataset is sufficient to address the business task because it contains trip-level behavioral data that allows for direct comparison between casual riders and annual members. The `member_casual` variable enables segmentation, while timestamps, ride duration, station information, and rideable type provide measurable indicators of usage behavior.

Although demographic and payment data are not included, the business objective focuses on behavioral differences rather than customer profiling. Therefore, trip-level operational data is adequate to generate actionable insights aimed at supporting membership conversion strategies.

## Phase 3 — PROCESS

### Data Cleaning and Feature Engineering

After merging all 12 monthly datasets (5,552,092 rows), a structured and controlled cleaning pipeline was implemented to ensure data integrity while preserving meaningful ride behavior.

---

### 1. Column Standardization

- Renamed `started_at` → `ride_start_datetime`
- Renamed `ended_at` → `ride_end_datetime`
- Converted both columns to datetime format to enable accurate time-based calculations.

---

### 2. Duplicate Handling

- Removed duplicate rows to maintain dataset integrity.
- Ensured no repeated ride records remained in the dataset.

---

### 3. Station Name Recovery via Coordinate Mapping

Instead of removing rows with missing station names, a coordinate-based recovery strategy was implemented:

- Rounded latitude and longitude values to 4 decimal places (~11m precision).
- Built a master lookup table of known coordinate–station pairs.
- Recovered missing `start_station_name` and `end_station_name` values using coordinate matching.
- Remaining unresolved stations were labeled as: `Public Rack/Unknown`.

This approach preserved ride records while minimizing information loss.

---

### 4. Coordinate Validation

Rows with missing end coordinates were removed, as station inference was not possible without geographic reference.

---

### 5. Ride Duration Engineering

Created a new feature:

`ride_length_mins` = (ride_end_datetime − ride_start_datetime) converted to minutes.

This metric serves as a core behavioral indicator for rider comparison.

---

### 6. Outlier Filtering

To remove structural noise while preserving realistic behavior:

- Removed trips ≤ 1 minute (likely accidental unlocks).
- Removed trips ≥ 1440 minutes (24 hours; likely lost or stolen bikes).

---

### Cleaning Impact Summary

- Initial merged dataset: 5,552,092 rows
- Rows removed during cleaning: 154,280 (~2.78%)
- Final cleaned dataset: 5,397,812 rows

This controlled cleaning strategy focused strictly on data integrity and operational realism, avoiding over-cleaning while ensuring analytical reliability for behavioral comparison.