### Question 3: Departments with High Average Employee Tenure (Medium)


**Problem Statement:**
You are given `Departments` and `Employees` tables. The `Employees` table includes a `HireDate`. Write an SQL query to identify departments where the average employee tenure (in years, calculated as of '2023-12-31') is greater than 5 years. The output should list the department name and its calculated average tenure in years, rounded to two decimal places. Order the results by average tenure in descending order.

**Schema:**

**Table: `Departments`**
| Column Name    | Data Type    | Constraints/Notes |
|----------------|--------------|-------------------|
| DepartmentID   | INT          | PRIMARY KEY       |
| DepartmentName | VARCHAR(255) | NOT NULL          |

**Table: `Employees`**
| Column Name  | Data Type    | Constraints/Notes                             |
|--------------|--------------|-----------------------------------------------|
| EmployeeID   | INT          | PRIMARY KEY                                   |
| EmployeeName | VARCHAR(255) | NOT NULL                                      |
| DepartmentID | INT          | FOREIGN KEY REFERENCES Departments(DepartmentID)|
| HireDate     | DATE         |                                               |

---


**Sample Input:**

**Departments Table:**
| DepartmentID | DepartmentName |
|--------------|----------------|
| 1            | Engineering    |
| 2            | Marketing      |
| 3            | Sales          |
| 4            | Research       |

**Employees Table:**
| EmployeeID | EmployeeName | DepartmentID | HireDate   |
|------------|--------------|--------------|------------|
| 101        | John Smith   | 1            | 2015-06-15 |
| 102        | Jane Doe     | 1            | 2017-08-20 |
| 103        | Mike Brown   | 2            | 2020-01-10 |
| 104        | Lisa Green   | 2            | 2021-07-01 |
| 105        | David Lee    | 3            | 2018-03-01 |
| 106        | Sarah King   | 1            | 2014-11-01 |
| 107        | Paul Young   | 4            | 2022-05-01 |

**Sample Output:**
(Assuming calculations are as of '2023-12-31')
```
| DepartmentName | AvgTenureYears |
|----------------|----------------|
| Engineering    | 7.61           |
```
**Solution:**

```sql

SELECT
    d.DepartmentName,
    ROUND(AVG((JULIANDAY('2023-12-31') - JULIANDAY(e.HireDate)) / 365.25), 2) AS AvgTenureYears
FROM
    Departments d
JOIN
    Employees e ON d.DepartmentID = e.DepartmentID
GROUP BY
    d.DepartmentName
HAVING
    AVG((JULIANDAY('2023-12-31') - JULIANDAY(e.HireDate)) / 365.25) > 5
ORDER BY
    AvgTenureYears DESC;

```

**Hints:**
Hint 1:
For each employee, calculate their tenure by finding the difference between a reference date (e.g., '2023-12-31') and their HireDate. Then, group these employees by their department to calculate an average tenure.

Hint 2:
Once you have the average tenure for each department, you'll need to filter these groups. The HAVING clause is used to filter groups created by GROUP BY, unlike WHERE which filters individual rows before grouping.
