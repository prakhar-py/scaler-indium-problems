### Question 1: Top Spending Customers by Quarter (Easy)


**Problem Statement:**
You are given tables for `Customers`, `Orders`, and `OrderItems`. Write an SQL query to find the total amount spent by each customer in each quarter of the year 2023. The output should include the customer's name, the quarter (e.g., 'Q1', 'Q2', 'Q3', 'Q4'), and the total amount spent by that customer in that quarter. Only include customers who have made purchases in 2023. Order the results by customer name and then by quarter.

**Schema:**

**Table: `Customers`**
| Column Name  | Data Type    | Constraints/Notes |
|--------------|--------------|-------------------|
| CustomerID   | INT          | PRIMARY KEY       |
| CustomerName | VARCHAR(255) | NOT NULL          |

**Table: `Orders`**
| Column Name | Data Type | Constraints/Notes                        |
|-------------|-----------|------------------------------------------|
| OrderID     | INT       | PRIMARY KEY                              |
| CustomerID  | INT       | FOREIGN KEY REFERENCES Customers(CustomerID) |
| OrderDate   | DATE      |                                          |

**Table: `OrderItems`**
| Column Name   | Data Type     | Constraints/Notes                    |
|---------------|---------------|--------------------------------------|
| OrderItemID   | INT           | PRIMARY KEY                          |
| OrderID       | INT           | FOREIGN KEY REFERENCES Orders(OrderID) |
| ProductName   | VARCHAR(255)  |                                      |
| Price         | DECIMAL(10, 2)|                                      |
| Quantity      | INT           |                                      |



**Sample Input:**

**Customers Table:**
| CustomerID | CustomerName |
|------------|--------------|
| 1          | Alice Wonderland |
| 2          | Bob The Builder |
| 3          | Charlie Brown |

**Orders Table:**
| OrderID | CustomerID | OrderDate  |
|---------|------------|------------|
| 101     | 1          | 2023-02-15 |
| 102     | 2          | 2023-03-20 |
| 103     | 1          | 2023-05-10 |
| 104     | 3          | 2023-08-01 |
| 105     | 2          | 2023-11-05 |
| 106     | 1          | 2022-12-20 |

**OrderItems Table:**
| OrderItemID | OrderID | ProductName | Price   | Quantity |
|-------------|---------|-------------|---------|----------|
| 1           | 101     | Widget A    | 10.00   | 2        |
| 2           | 101     | Widget B    | 15.00   | 1        |
| 3           | 102     | Gadget X    | 25.00   | 3        |
| 4           | 103     | Widget A    | 10.00   | 5        |
| 5           | 104     | Gizmo Z     | 50.00   | 1        |
| 6           | 105     | Gadget Y    | 30.00   | 2        |
| 7           | 106     | Old Item    | 5.00    | 1        |



**Sample Output:**
```
| CustomerName    | Quarter | TotalSpent |
|-----------------|---------|------------|
| Alice Wonderland| Q1      | 35.00      |
| Alice Wonderland| Q2      | 50.00      |
| Bob The Builder | Q1      | 75.00      |
| Bob The Builder | Q4      | 60.00      |
| Charlie Brown   | Q3      | 50.00      |
```
*Calculation Hints:*
*   Alice (Q1): (10.00 * 2) + (15.00 * 1) = 20 + 15 = 35.00 (Order 101 on 2023-02-15)
*   Alice (Q2): (10.00 * 5) = 50.00 (Order 103 on 2023-05-10)
*   Bob (Q1): (25.00 * 3) = 75.00 (Order 102 on 2023-03-20)
*   Bob (Q4): (30.00 * 2) = 60.00 (Order 105 on 2023-11-05)
*   Charlie (Q3): (50.00 * 1) = 50.00 (Order 104 on 2023-08-01)
*   Order 106 is ignored as it's not in 2023.

---
**Solution:**

```sql
SELECT
    c.CustomerName,
    CASE
        WHEN STRFTIME('%m', o.OrderDate) IN ('01', '02', '03') THEN 'Q1'
        WHEN STRFTIME('%m', o.OrderDate) IN ('04', '05', '06') THEN 'Q2'
        WHEN STRFTIME('%m', o.OrderDate) IN ('07', '08', '09') THEN 'Q3'
        WHEN STRFTIME('%m', o.OrderDate) IN ('10', '11', '12') THEN 'Q4'
    END AS Quarter,
    SUM(oi.Price * oi.Quantity) AS TotalSpent
FROM
    Customers c
JOIN
    Orders o ON c.CustomerID = o.CustomerID
JOIN
    OrderItems oi ON o.OrderID = oi.OrderID
WHERE
    STRFTIME('%Y', o.OrderDate) = '2023'
GROUP BY
    c.CustomerName,
    Quarter
ORDER BY
    c.CustomerName,
    Quarter;

```
**Hints:**

Hint 1:
You'll need to combine information from three tables: one for customer details, one for order headers (including dates), and one for order line items (including price and quantity). Think about which JOIN types are appropriate.

Hint 2:
To group by quarter, you'll need to extract the quarter from the OrderDate. After that, aggregate the spending (price * quantity) for each customer within each of these quarters.


