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


- **Type 0:** Never change  
- **Type 1:** OverwritesReplace  
- **Type 2:** Add new row  
- **Type 3:** Add new column  

| Type | History Level                 | Real Use Focus           |
|------|-------------------------------|--------------------------|
| 0    | None                          | Static reference data |
| 1    | Overwrites history(no history)| Corrections / overwrites |
| 2    | Full                          | Auditing, analytics |
| 3    | Partial                       | Last known change only |

---
# 🔗 Bridge Table in Power BI (Many-to-Many Resolution)

## 📌 What is a Bridge Table?

A **bridge (mapping) table** is a lookup table that stores relationship pairs between two entities.

👉 It defines **who is linked to what** in a many-to-many relationship.

---

## 📊 Example Bridge Table

| CustomerID | AccountID |
|------------|----------|
| C001       | A100     |
| C002       | A100     |
| C001       | A200     |

---

## 🧠 Meaning of the Data

- Customer **C001** has accounts **A100 and A200**
- Customer **C002** has account **A100**
- Account **A100** is shared by **C001 and C002**

---

## 🔌 How It Connects (VERY IMPORTANT)

Instead of this ❌ (bad design):
Customer ↔ Account (Many-to-Many)
We use this ✅ (correct model):
Customer 1 → Many Bridge rows
Account  1 → Many Bridge rows
---## 🧩 Relationships in the Model### 1. Customer → Bridge
Customer.CustomerID  ——  Bridge.CustomerID
(1 to many)
### 2. Account → Bridge
Account.AccountID  ——  Bridge.AccountID
(1 to many)
---## 📊 Data Model Flow
Customer Table
│
│ (1-to-many)
▼
Bridge Table
▲
│ (many-to-1)
│
Account Table
│
▼
Transactions Table
---## 🧠 Why This WorksWith a bridge table, Power BI now has:✔ Only **one-to-many relationships**  ✔ No ambiguity in filtering  ✔ Correct aggregation logic  ---## ⚙️ How Filtering WorksWhen a user selects a Customer:1. Power BI finds matching rows in **Bridge Table**2. Bridge returns related **Account IDs**3. Accounts filter **Transactions**4. Transactions are correctly aggregated by Customer---## 🚀 Key Takeaway👉 A bridge table converts a **many-to-many relationship into two clean one-to-many relationships**, enabling accurate filtering and aggregation in Power BI.

