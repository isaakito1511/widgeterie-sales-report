# Technical documentation

This document explains the data, data quality issues, business inputs, assumptions and calculations behind the four decisions presented in the [README](README.md).

The full Python code is available in the [Jupyter notebook](notebooks/widgeterie_decisions.ipynb).

## 1. Data

### Data sources and grain

| File | Table name | One row represents | Rows |
| --- | --- | --- | ---: |
| `supply_data_2020.xlsx` to `supply_data_2024.xlsx` (tabs `Month_1` to `Month_12`) | `ORDER_LINES` | One part in one order | 396,978 |
| `stores.xlsx` | `WHOLESALERS` | One wholesaler | 275 |

One `Order_ID` can appear on several rows because one order can contain several parts. The dataset represents approximately 60,000 orders.

### Data dictionary

**ORDER_LINES** (`supply_data` files)

| Column | Type | Description |
| --- | --- | --- |
| `Date` | Date | Order date, from 2020-01-01 to 2024-12-30 |
| `Order_ID` | String | Order identifier. Repeats for every line belonging to the same order |
| `SKU_Name` | String | Part code, for example `SKU-6682` |
| `Quantity` | Integer | Units of this part in the order |
| `Sales` | Float | Revenue of the order line in euros |
| `Location` | String | Wholesaler name, used to connect to `WHOLESALERS` |
| `Year` | Integer | Year of the order, added from the workbook name |
| `Month` | Integer | Month of the order, added from the worksheet name |

**WHOLESALERS** (`stores.xlsx`)

| Column | Type | Description |
| --- | --- | --- |
| `location` | String | Wholesaler name, used as the unique identifier |
| `city` | String | Wholesaler city |
| `lat` | Float | Latitude |
| `lng` | Float | Longitude |
| `region` | String | French region |

### Data structure and relationship

- `WHOLESALERS.location` is the primary key: each wholesaler appears once in `stores.xlsx`.
- `ORDER_LINES.Location` is the foreign key: it identifies the wholesaler associated with each order line.
- One wholesaler can have many order lines: a one-to-many relationship.

```mermaid
erDiagram
    WHOLESALERS ||--o{ ORDER_LINES : "has order lines"

    ORDER_LINES {
        date Date
        string Order_ID
        string SKU_Name
        int Quantity
        float Sales
        string Location FK
        int Year
        int Month
    }

    WHOLESALERS {
        string location PK
        string city
        float lat
        float lng
        string region
    }
