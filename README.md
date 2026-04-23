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

---

## ⚡ Comparison of SCD Types

| Type | History | Data Handling        | Storage Impact | Complexity |
|------|--------|----------------------|---------------|------------|
| 0    | No     | No updates allowed   | Very Low      | Very Low   |
| 1    | No     | Overwrites data      | Low           | Low        |
| 2    | Yes    | New row per change   | High          | High       |
| 3    | Partial| Adds extra columns   | Medium        | Medium     |

---

Show me real SQL implementation for SCD Type 1 & 2
