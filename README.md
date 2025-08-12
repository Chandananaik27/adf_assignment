# adf_assignment

## assignment_overview
This project implements a complete data engineering pipeline using **Azure Data Lake Storage (ADLS)**, **Azure Data Factory (ADF)**, and **Azure Databricks** to process customer, product, store, and sales data into Bronze, Silver, and Gold layers.

---

## Steps Implemented

### 1. Data Storage Setup (ADLS)
- Created an ADLS container: `sales_view_devtst`.
- Created folders: `customer`, `product`, `store`, `sales`.
- Uploaded files in sequence to simulate real-time data ingestion.

---

### 2. Data Ingestion Pipeline (ADF)
- Created a parameterized ADF pipeline to get the **latest modified files** from ADLS.
- Passed folder and file parameters dynamically to handle **daily data ingestion**.

---

### 3. Bronze Layer (Raw Data)
- Created `Bronze/sales_view/` container.
- Subfolders: `customer`, `product`, `store`, `sales`.
- Stored raw data copied directly from ADF pipeline.

---

### 4. Silver Layer (Clean & Enriched Data)
**Database Name:** `sales_view`

#### Customer
- Converted all headers to **snake_case lowercase** (UDF).
- Split `Name` into `first_name` and `last_name`.
- Extracted `domain` from `email`.
- Converted `gender` → M/F format.
- Split `joining_date` into `date` and `time` (YYYY-MM-DD format).
- Created `expenditure_status` based on `spent` column (MINIMUM/MAXIMUM).
- Upserted to `silver/sales_view/customer` in Delta Parquet format.

#### Product
- Converted headers to snake_case lowercase (UDF).
- Added `sub_category` based on `category_id` mapping.
- Upserted to `silver/sales_view/product`.

#### Store
- Converted headers to snake_case lowercase (UDF).
- Extracted `store_category` from email domain.
- Converted `created_at` and `updated_at` to YYYY-MM-DD format.
- Upserted to `silver/sales_view/store`.

#### Sales
- Converted headers to snake_case lowercase (UDF).
- Upserted to `silver/sales_view/customer_sales`.

---

### 5. Gold Layer (Business-Level Data)
- Joined **product** and **store** tables to create a dataset with store, product, and inventory details.
- Joined the above with **customer_sales** to produce:
