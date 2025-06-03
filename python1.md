
### Question 1: Departmental Salary Analysis (Easy-Medium)

**Problem Description:**

You are given a pandas DataFrame representing employee data. The DataFrame has the columns: `EmployeeID, Name, Department, Salary`. Your task is to write a Python function that takes this DataFrame and a `target_department` name as input. The function should calculate and return the average salary of all employees belonging to the `target_department`.

If the `target_department` does not exist in the data, if there are no employees in that department, or if the input DataFrame is empty or lacks the required columns, the function should return `0.0`.

**Create the following input files for testing:**

**`employee_input1.txt`** (Standard case)
```csv
EmployeeID,Name,Department,Salary
1,Alice,Engineering,75000
2,Bob,Engineering,80000
3,Charlie,HR,60000
4,David,Engineering,70000
5,Eve,HR,65000
```

**`employee_input2.txt`** (Target department has one/few employees)
```csv
EmployeeID,Name,Department,Salary
10,Ivy,Marketing,85000
11,Jack,Sales,72000
12,Grace,Marketing,88000
13,Mike,Sales,70000
```

**`employee_input3.txt`** (Target department not present, or data leading to empty filtered set)
```csv
EmployeeID,Name,Department,Salary
20,Ken,Finance,90000
21,Liam,Finance,92000
```
**Function to Complete:**
```python
import pandas as pd

def calculate_average_salary_for_department(employee_df: pd.DataFrame, target_department: str) -> float:
    """
    Calculates the average salary for employees in a specific department from a DataFrame.

    Args:
        employee_df: A pandas DataFrame with employee data.
                     Expected columns: EmployeeID,Name,Department,Salary
        target_department: The name of the department to filter by.

    Returns:
        The average salary of employees in the target_department.
        Returns 0.0 if the department has no employees, does not exist,
        the DataFrame is empty, or lacks required columns.
    """
    # Check if DataFrame is empty
    if employee_df.empty:
        return 0.0
            
    # Ensure required columns exist
    required_cols = ['Department', 'Salary']
    if not all(col in employee_df.columns for col in required_cols):
        return 0.0

    # Filter the DataFrame for the target department
    department_df = employee_df[employee_df['Department'] == target_department]

    # Check if the filtered DataFrame is empty (department not found or no employees)
    if department_df.empty:
        return 0.0
    
    # Check if 'Salary' column in the filtered set is suitable for mean calculation
    if department_df['Salary'].isnull().all() or department_df['Salary'].empty:
        return 0.0

    try:
        # Calculate the average salary for the filtered department
        average_salary = department_df['Salary'].mean()
        
        # Handle potential NaN result if all salaries were non-numeric/null
        if pd.isna(average_salary):
            return 0.0
            
        return round(average_salary, 2) # Round to 2 decimal places for consistency
    except TypeError: # If 'Salary' column is not numeric after all checks
        return 0.0
    except Exception:
        # Catch any other unexpected errors during processing
        return 0.0
```

---
