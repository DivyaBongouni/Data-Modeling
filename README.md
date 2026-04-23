# 📊 Slowly Changing Dimensions (SCD) Types

This document explains the different types of Slowly Changing Dimensions (SCD) used in data warehousing to manage historical changes in dimension data.

---

## 🟢 SCD Type 0 – Fixed (No Changes Allowed)

### Definition
Data is never updated once inserted.

### Characteristics
- Values remain **static**
- No updates allowed
- No history tracking

### Use Cases
- Birth dates
- Original identifiers
- Reference/master immutable data

### Example

| CustomerID | Name  | DOB        |
|------------|-------|------------|
| 101        | John  | 1990-01-01 |

> Even if the name changes, it is not updated.
### 📌Examples

### 1. Country Master

| Country_Code | Country_Name | ISO_Code |
|--------------|--------------|----------|
| DE           | Germany      | DEU      |
| IN           | India        | IND      |
| US           | United States| USA      |

---

### 2. Date Dimension (Core Attributes)

| Date_Key | Full_Date  | Fiscal_Year |
|----------|------------|-------------|
| 20260101 | 2026-01-01 | 2026        |
| 20260102 | 2026-01-02 | 2026        |

---

### 3. Currency Codes

| Currency_Code | Currency_Name | Symbol |
|---------------|--------------|--------|
| EUR           | Euro         | €      |
| USD           | US Dollar    | $      |
---

## 🔵 SCD Type 1 – Overwrite (No History)

### Definition
Existing data is overwritten with new data.

### Characteristics
- Updates existing record
- No history preserved
- Simplest implementation

### Use Cases
- Correcting errors (e.g., spelling mistakes)
- Non-critical attributes

### Example

**Before Update**

| CustomerID | City   |
|------------|--------|
| 101        | Berlin |

**After Update**

| CustomerID | City   |
|------------|--------|
| 101        | Munich |

> Previous value is lost permanently.
Corrected or Non-Historical Attributes
### 📌Examples
### 1. Customer Profile (Contact Updates)

| Customer_ID | Name   | Email              |
|-------------|--------|--------------------|
| 101         | John   | john@abc.com       |
| 102         | Alice  | alice@xyz.com      |

---

### 2. Product Category Mapping

| Product_ID | Product_Name | Category   |
|------------|--------------|------------|
| P001       | Laptop       | Electronics|
| P002       | Chair        | Furniture  |

---

### 3. Employee Department (Reclassification)

| Employee_ID | Name   | Department |
|-------------|--------|------------|
| E001        | Raj    | Finance    |
| E002        | Sara   | HR         |

---

## 🟡 SCD Type 2 – Full History Tracking

### Definition
Maintains full history by inserting a new row for every change.

### Characteristics
- Inserts new record for each change
- Uses surrogate keys
- Tracks history using effective dates or flags

### Common Columns
- Start_Date
- End_Date
- Is_Current

### Example

| Cust_SK | CustomerID | City   | Start_Date | End_Date   | Current |
|---------|------------|--------|------------|------------|---------|
| 1       | 101        | Berlin | 2020-01-01 | 2022-06-01 | N       |
| 2       | 101        | Munich | 2022-06-02 | NULL       | Y       |

> Best for analytics, auditing, and historical reporting.
### 📌 Example: Full Historical Audit Trail

### 1. Customer Address History

| Cust_SK | Customer_ID | City    | Start_Date | End_Date   | Is_Current |
|---------|------------|---------|------------|------------|------------|
| 1       | 101        | Berlin  | 2020-01-01 | 2023-05-01 | N          |
| 2       | 101        | Munich  | 2023-05-02 | NULL       | Y          |

---

### 2. Employee Job Role History

| Emp_SK | Employee_ID | Role          | Start_Date | End_Date   | Is_Current |
|--------|------------|---------------|------------|------------|------------|
| 1      | E001       | Analyst       | 2021-01-01 | 2022-12-31 | N          |
| 2      | E001       | Senior Analyst| 2023-01-01 | NULL       | Y          |

---

### 3. Product Price History

| Product_SK | Product_ID | Price | Start_Date | End_Date   | Is_Current |
|------------|------------|-------|------------|------------|------------|
| 1          | P001       | 1000  | 2022-01-01 | 2023-06-01 | N          |
| 2          | P001       | 1200  | 2023-06-02 | NULL       | Y          |
---

## 🟠 SCD Type 3 – Limited History

### Definition
Stores limited history using additional columns.

### Characteristics
- Keeps only current and previous value
- No full history maintained

### Use Cases
- Tracking last known change only
- Lightweight historical tracking

### Example

| CustomerID | Current_City | Previous_City |
|------------|--------------|----------------|
| 101        | Munich       | Berlin         |

> Only one prior state is preserved.

📌 Use Case: Track Current + Previous Value Only

### 1. Customer City Tracking

| Customer_ID | Current_City | Previous_City |
|-------------|--------------|----------------|
| 101         | Munich       | Berlin         |
| 102         | Hamburg      | Cologne        |

---

### 2. Employee Department Change

| Employee_ID | Current_Dept | Previous_Dept |
|-------------|--------------|---------------|
| E001        | Finance      | IT            |
| E002        | HR           | Admin         |

---

### 3. Product Category Change

| Product_ID | Current_Category | Previous_Category |
|------------|------------------|-------------------|
| P001       | Electronics      | Home Appliances   |
| P002       | Furniture        | Office Supplies   |
---

## ⚡ Quick Summary

| Type | History Level | Real Use Focus |
|------|--------------|----------------|
| 0    | None         | Static reference data |
| 1    | None         | Corrections / overwrites |
| 2    | Full         | Auditing, analytics |
| 3    | Partial      | Last known change only |

---
