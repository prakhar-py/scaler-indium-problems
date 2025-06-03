### Question 2: Products Not Sold in the Last 6 Months (Easy)

**Topics:** Subqueries (NOT IN or LEFT JOIN/IS NULL), Joins, Date Functions

**Question:**
You have `Products` and `Sales` tables. Write an SQL query to find all products that have not been sold in the last 6 months from a given reference date (e.g., '2023-12-31'). The output should only contain the product names. Assume the current date for calculation is '2023-12-31'.

**Schema:**

**Table: `Products`**
| Column Name | Data Type    | Constraints/Notes |
|-------------|--------------|-------------------|
| ProductID   | INT          | PRIMARY KEY       |
| ProductName | VARCHAR(255) | NOT NULL          |
| Category    | VARCHAR(100) |                   |

**Table: `Sales`**
| Column Name  | Data Type | Constraints/Notes                       |
|--------------|-----------|-----------------------------------------|
| SaleID       | INT       | PRIMARY KEY                             |
| ProductID    | INT       | FOREIGN KEY REFERENCES Products(ProductID)|
| SaleDate     | DATE      |                                         |
| QuantitySold | INT       |                                         |

---

**Sample Output:**
(Assuming reference date '2023-12-31'. Last 6 months means sales on or after '2023-07-01'.)
```
| ProductName     |
|-----------------|
| Ergonomic Chair |
| Desk Lamp       |
```
*Calculation Hints:*
*   The reference date is '2023-12-31'. The 6-month period starts from '2023-07-01'.
*   Laptop Pro: Sold on 2023-09-01 (within last 6 months).
*   Coffee Maker: Sold on 2023-08-20 (within last 6 months).
*   Ergonomic Chair: Last sold on 2023-03-10 (NOT within last 6 months).
*   Smart Watch: Sold on 2023-10-05 (within last 6 months).
*   Desk Lamp: No sales record at all, so definitely not sold in the last 6 months.

---

**Solution:**

```sql
SELECT
    p.ProductName
FROM
    Products p
LEFT JOIN
    Sales s ON p.ProductID = s.ProductID
            AND s.SaleDate >= DATE('2023-12-31', '-6 months') -- Sales within the last 6 months
WHERE
    s.SaleID IS NULL  -- Product has no sales in the last 6 months
    OR p.ProductID NOT IN ( -- Or product exists but all its sales are older
        SELECT DISTINCT s_inner.ProductID
        FROM Sales s_inner
        WHERE s_inner.SaleDate >= DATE('2023-12-31', '-6 months')
    )
GROUP BY p.ProductName; -- Ensures each product name appears once if it had multiple old sales

-- A more streamlined LEFT JOIN approach focusing only on recent sales:
SELECT
    p.ProductName
FROM
    Products p
LEFT JOIN (
    SELECT DISTINCT ProductID
    FROM Sales
    WHERE SaleDate >= DATE('2023-12-31', '-6 months') -- Reference date for SQLite
) AS RecentSales ON p.ProductID = RecentSales.ProductID
WHERE
    RecentSales.ProductID IS NULL;

```

**Hints:**

Hint 1:
Consider finding all products that have been sold within the specified recent period. Then, think about how to find products from your main product list that are not in this "recently sold" list.

Hint 2:
A LEFT JOIN from the Products table to a subquery of recent Sales can be very useful. Look for rows where the join condition doesn't find a match in the recent sales. Alternatively, NOT EXISTS or NOT IN can be used with a subquery.


