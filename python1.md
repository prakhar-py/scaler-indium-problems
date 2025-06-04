### Question 1: Low Stock Alert System (Easy-Medium)

**Problem Description:**

A small retail store uses a simple system to track its inventory. You are tasked with developing a feature that identifies products running low on stock.
You will be given a list of product inventory records, where each record is a tuple `(product_id: str, current_stock: int)`. You will also be given an integer `stock_threshold`.

Your function should process this inventory list and return a list of `product_id`s for all products whose `current_stock` is less than or equal to the `stock_threshold`. The returned list of `product_id`s should be sorted in alphabetical order. If no products are below the threshold, return an empty list.

**Function to Complete:**
```python
from typing import List, Tuple

def find_low_stock_products(inventory_levels: List[Tuple[str, int]], stock_threshold: int) -> List[str]:

    # Your code here
    low_stock_ids = []
    for product_id, current_stock in inventory_levels:
        if current_stock <= stock_threshold:
            low_stock_ids.append(product_id)
    
    low_stock_ids.sort() # Sort alphabetically
    return low_stock_ids

```

