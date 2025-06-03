
### Question 2: Product Revenue Analysis (Medium)

**Problem Description:**

You are given a pandas DataFrame representing sales data. The DataFrame has the columns: `OrderID, ProductID, ProductCategory, SaleDate, Quantity, Price`. Your task is to write a Python function that takes this DataFrame, a `target_category`, a `start_date_str`, and an `end_date_str` as input.

The function should:
1.  Filter the sales data for records belonging to the `target_category`.
2.  Further filter these records to include only sales made within the date range (inclusive of `start_date_str` and `end_date_str`). Dates are provided as 'YYYY-MM-DD' strings in the input parameters and are assumed to be 'YYYY-MM-DD' or datetime objects in the DataFrame's `SaleDate` column.
3.  For the filtered data, calculate the total revenue for each `ProductID`. Total revenue for a sale is `Quantity * Price`.
4.  Return a pandas DataFrame containing two columns: `ProductID` and `TotalRevenue`.
5.  The resulting DataFrame should be sorted by `ProductID` in ascending order.
6.  If no sales match the criteria, or if the input DataFrame is empty or lacks required columns, return an empty DataFrame with the columns `['ProductID', 'TotalRevenue']`.

**Create the following input files for testing:**

**`sales_input1.txt`** (Standard case)
```csv
OrderID,ProductID,ProductCategory,SaleDate,Quantity,Price
1,101,Electronics,2023-01-15,2,100
2,102,Electronics,2023-01-20,1,250
3,201,Books,2023-01-10,5,20
4,101,Electronics,2023-02-05,1,100
5,103,Electronics,2023-01-25,3,150
6,202,Books,2023-02-15,2,25
7,101,Electronics,2023-01-18,3,90
```

**`sales_input2.txt`** (Category exists, but no sales in date range, or different category focus)
```csv
OrderID,ProductID,ProductCategory,SaleDate,Quantity,Price
10,301,Apparel,2023-03-15,10,30
11,302,Apparel,2023-03-20,5,35
12,105,Electronics,2023-04-01,1,500
13,106,Electronics,2023-03-10,2,75
```

**`sales_input3.txt`** (File with only headers to test empty DataFrame scenario after load)
```csv
OrderID,ProductID,ProductCategory,SaleDate,Quantity,Price
```

**Function to Complete:**
```python
import pandas as pd

def aggregate_product_revenue(sales_df: pd.DataFrame, target_category: str, start_date_str: str, end_date_str: str) -> pd.DataFrame:
    """
    Filters sales DataFrame by category and date range, then aggregates total revenue per product.

    Args:
        sales_df: A pandas DataFrame with sales data.
                  Expected columns: OrderID,ProductID,ProductCategory,SaleDate,Quantity,Price
        target_category: The product category to filter by.
        start_date_str: The start date for the filter (YYYY-MM-DD string).
        end_date_str: The end date for the filter (YYYY-MM-DD string).

    Returns:
        A pandas DataFrame with columns ['ProductID', 'TotalRevenue'],
        sorted by ProductID.
        Returns an empty DataFrame with these columns if no matching sales are found
        or if the input DataFrame is empty/lacks required columns.
    """
    output_columns = ['ProductID', 'TotalRevenue']
    
    if sales_df.empty:
        return pd.DataFrame(columns=output_columns)
    
    required_cols = ['ProductCategory', 'SaleDate', 'Quantity', 'Price', 'ProductID']
    if not all(col in sales_df.columns for col in required_cols):
        return pd.DataFrame(columns=output_columns)

    try:
        # Make a copy to avoid SettingWithCopyWarning on original DataFrame if passed around
        df_copy = sales_df.copy()

        # Convert SaleDate to datetime objects if not already
        # Ensure 'SaleDate' column exists before trying to convert
        if 'SaleDate' in df_copy.columns:
            df_copy['SaleDate'] = pd.to_datetime(df_copy['SaleDate'])
        else: # Should have been caught by required_cols check, but defensive
            return pd.DataFrame(columns=output_columns)

        start_date = pd.to_datetime(start_date_str)
        end_date = pd.to_datetime(end_date_str)

        # 1. Filter by target_category
        category_df = df_copy[df_copy['ProductCategory'] == target_category]

        if category_df.empty:
            return pd.DataFrame(columns=output_columns)

        # 2. Filter by date range
        date_filtered_df = category_df[
            (category_df['SaleDate'] >= start_date) & (category_df['SaleDate'] <= end_date)
        ]

        if date_filtered_df.empty:
            return pd.DataFrame(columns=output_columns)

        # 3. Calculate revenue for each sale
        date_filtered_df['Revenue'] = date_filtered_df['Quantity'] * date_filtered_df['Price']

        # 4. Group by ProductID and sum revenue
        revenue_by_product = date_filtered_df.groupby('ProductID')['Revenue'].sum().reset_index()
        
        # Ensure 'TotalRevenue' column exists after aggregation (it will be 'Revenue')
        if 'Revenue' not in revenue_by_product.columns: # Should not happen if groupby worked
             revenue_by_product['Revenue'] = 0 # Or handle as error

        revenue_by_product.rename(columns={'Revenue': 'TotalRevenue'}, inplace=True)
        
        # 5. Sort by ProductID
        result_df = revenue_by_product.sort_values(by='ProductID').reset_index(drop=True)
        
        # Ensure the final DataFrame has the correct columns in the correct order
        # If ProductID was not in revenue_by_product (e.g., after an empty filter)
        if 'ProductID' not in result_df.columns:
             return pd.DataFrame(columns=output_columns) # Or create with empty 'ProductID'
        if 'TotalRevenue' not in result_df.columns: # Should have been renamed
             result_df['TotalRevenue'] = 0 # Placeholder if rename failed or column missing

        return result_df[output_columns]

    except Exception: # Catch any other unexpected errors
        return pd.DataFrame(columns=output_columns)
```

**Hints:**
Hint 1:
First, convert the 'SaleDate' column to datetime objects for easy comparison. Then, apply sequential filtering: first by ProductCategory, and then by the date range using the converted dates.

Hint 2:
After filtering, create a new 'Revenue' column (Quantity * Price). Then, use groupby() on 'ProductID' and aggregate the 'Revenue' using sum(). Remember to sort the final result.

