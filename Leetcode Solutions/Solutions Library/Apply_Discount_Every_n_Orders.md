## Apply Discount Every n Orders
**Language:** python
**Tags:** python,oop,hashmap,list

### Description:
---

### 1. Overview & Intent

The `Cashier` class simulates a simple point-of-sale system that calculates the total bill for a customer's purchase. Its primary function is to:

*   Store product prices.
*   Calculate a subtotal based on purchased items and quantities.
*   Apply a periodic discount to every `n`-th customer.

### 2. How It Works

*   **Initialization (`__init__`)**:
    *   Takes `n` (the discount frequency), `discount` (percentage), `products` (list of product IDs), and `prices` (list of corresponding prices) as input.
    *   Stores `n` and `discount` as instance variables.
    *   Creates a `product_prices` dictionary, mapping each `product` ID to its `price` for quick lookups.
    *   Initializes `customer_count` to `0`, tracking the number of customers served.

*   **Get Bill (`getBill`)**:
    *   Takes two lists: `product` (IDs of items purchased in this transaction) and `amount` (quantities for each item).
    *   Iterates through the `product` and `amount` lists simultaneously.
    *   For each item, it looks up its price in `self.product_prices` and adds `quantity * price` to a `subtotal`.
    *   Increments `self.customer_count` by one after calculating the subtotal.
    *   Checks if the `customer_count` is a multiple of `n` (i.e., `self.customer_count % self.n == 0`).
    *   If it is, the `subtotal` is reduced by the specified `discount_percentage` to calculate the `final_bill`.
    *   Returns the `final_bill`.

### 3. Key Design Decisions

*   **`product_prices` Dictionary**:
    *   **Decision**: Using a hash map (Python dictionary) to store product IDs and their prices.
    *   **Trade-off**: Requires O(P) space during initialization (where P is the number of unique products) but provides average O(1) time complexity for price lookups during `getBill`. This is crucial for performance when handling many different products.
*   **`customer_count`**:
    *   **Decision**: Maintaining a simple integer counter to track customers for discount logic.
    *   **Trade-off**: Straightforward and efficient for its purpose.
*   **Direct Discount Calculation**:
    *   **Decision**: Applying the discount by calculating `subtotal * ((100 - discount_percentage) / 100.0)` directly within `getBill`.
    *   **Trade-off**: Simple and readable for a one-off calculation.

### 4. Complexity

*   **`__init__` method**:
    *   **Time Complexity**: O(P), where P is the number of products in the input `products` list. This is due to iterating through the lists to populate the `product_prices` dictionary.
    *   **Space Complexity**: O(P), for storing the `product_prices` dictionary.

*   **`getBill` method**:
    *   **Time Complexity**: O(B), where B is the number of unique items in the current bill (length of the input `product` or `amount` list). Each price lookup in `self.product_prices` is O(1) on average.
    *   **Space Complexity**: O(1), ignoring the space for input lists.

### 5. Edge Cases & Correctness

*   **Unknown Product ID**:
    *   **Issue**: If a `product_id` in the `getBill` method's `product` list is not found in `self.product_prices`, a `KeyError` will be raised.
    *   **Correctness**: The current implementation is not robust to this and will crash.
*   **Empty Bill**:
    *   **Issue**: If `product` and `amount` lists are empty in `getBill`, the `subtotal` will correctly be `0.0`, and the method will return `0.0`.
    *   **Correctness**: Handled correctly.
*   **`n = 1`**:
    *   **Issue**: Every customer will receive the discount.
    *   **Correctness**: Handled correctly by `self.customer_count % 1 == 0`.
*   **`discount = 0`**:
    *   **Issue**: No discount will be applied even if the customer qualifies.
    *   **Correctness**: Handled correctly, as `subtotal * (100 / 100.0)` is `subtotal`.
*   **`discount = 100`**:
    *   **Issue**: The bill for discounted customers will be `0.0`.
    *   **Correctness**: Handled correctly, as `subtotal * (0 / 100.0)` is `0.0`.

### 6. Improvements & Alternatives

*   **Robustness: Handle Unknown Product IDs**:
    *   Modify `getBill` to check if `product_id` exists in `self.product_prices` using `self.product_prices.get(product_id)` with a default value (e.g., 0) or raise a custom error/log a warning.
    *   Example: `price = self.product_prices.get(product_id, 0)` could make unknown items free, or `raise ValueError(f"Unknown product ID: {product_id}")`.
*   **Readability: `zip` for Iteration**:
    *   Instead of `for i in range(len(product)):`, use `for product_id, quantity in zip(product, amount):`. This is more Pythonic and can improve readability.
*   **Minor Performance/Clarity: Pre-calculate Discount Factor**:
    *   The `((100 - self.discount_percentage) / 100.0)` calculation is repeated every time `getBill` is called, even if the discount percentage never changes.
    *   Calculate `self.discount_factor = (100 - self.discount_percentage) / 100.0` in `__init__` and then use `final_bill = subtotal * self.discount_factor` in `getBill`.
*   **Input Validation for `n` and `discount`**:
    *   While not explicitly requested by type hints, it's good practice to ensure `n > 0` and `0 <= discount <= 100` in the `__init__` method to prevent logical errors or unexpected behavior.

### 7. Security/Performance Notes

*   **Performance**: The current implementation is efficient for its intended purpose. Dictionary lookups are fast, and the loop in `getBill` scales linearly with the number of items in the current transaction. No major performance bottlenecks are apparent.
*   **Security**: This code snippet does not present direct security vulnerabilities. However, in a real-world system, input validation for product IDs (e.g., preventing injection attacks if product IDs were strings used in database queries) and amounts (preventing negative quantities) would be crucial, but those concerns are outside the scope of this specific code.

### Code:
```python
class Cashier:

    def __init__(self, n: int, discount: int, products: List[int], prices: List[int]):
        self.n = n
        self.discount_percentage = discount
        self.product_prices = {}
        for i in range(len(products)):
            self.product_prices[products[i]] = prices[i]
        self.customer_count = 0

    def getBill(self, product: List[int], amount: List[int]) -> float:
        subtotal = 0.0
        for i in range(len(product)):
            product_id = product[i]
            quantity = amount[i]
            price = self.product_prices[product_id]
            subtotal += quantity * price
        
        self.customer_count += 1
        
        final_bill = subtotal
        if self.customer_count % self.n == 0:
            final_bill = subtotal * ((100 - self.discount_percentage) / 100.0)
            
        return final_bill
```
