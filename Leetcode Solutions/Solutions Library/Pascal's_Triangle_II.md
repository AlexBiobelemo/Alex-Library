## Pascal's Triangle II
**Language:** python
**Tags:** python,oop,combinatorics,list

### Description:
---

### 1. Overview & Intent

This code implements a method to generate the `rowIndex`-th row of Pascal's Triangle. Pascal's Triangle is a triangular array of the binomial coefficients. The `rowIndex` corresponds to 'n' in C(n, k), where C(n, k) is the k-th binomial coefficient in the n-th row.

### 2. How It Works

The algorithm directly calculates the binomial coefficients for the given `rowIndex` using an iterative formula:

1.  **Initialization:**
    *   A list `row` of size `rowIndex + 1` is created, initialized with zeros.
    *   The first element `row[0]` is set to `1` (which is C(rowIndex, 0)).
2.  **Iterative Calculation:**
    *   It iterates from `r = 1` up to `rowIndex`.
    *   In each iteration `r`, it calculates the binomial coefficient `C(rowIndex, r)` using the relationship:
        `C(n, k) = C(n, k-1) * (n - k + 1) / k`
        *   Here, `n` is `rowIndex`, `k` is `r`, and `C(n, k-1)` is the `current_val` from the *previous* iteration.
        *   The formula becomes: `current_val = current_val * (rowIndex - r + 1) // r`.
    *   The calculated `current_val` is then stored in `row[r]`.
3.  **Return:** The populated `row` list, which contains all binomial coefficients for the specified `rowIndex`.

### 3. Key Design Decisions

*   **Data Structure:** A Python `List` (`row`) is used to store the binomial coefficients. This is suitable for sequential access and dynamic sizing if needed (though here the size is fixed once `rowIndex` is known).
*   **Algorithm:** The core of the solution is the iterative calculation of binomial coefficients. This approach is superior to:
    *   Calculating factorials directly (e.g., `n! / (k! * (n-k)!)`) which can lead to very large intermediate numbers and potential overflow in fixed-size integer types.
    *   Generating the full Pascal's Triangle row by row up to `rowIndex`, which would involve more redundant calculations and potentially more memory.
*   **Mathematical Property:** Leveraging the identity `C(n, k) = C(n, k-1) * (n - k + 1) / k` (where `C(n, k-1)` is the previous term in the row) is key to its efficiency.
*   **Integer Division (`//`):** Using `//` ensures that the results remain integers, as binomial coefficients are always whole numbers.

### 4. Complexity

*   **Time Complexity: O(rowIndex)**
    *   The loop runs `rowIndex` times.
    *   Each operation inside the loop (multiplication, subtraction, addition, division, assignment) is constant time.
    *   Therefore, the total time complexity is linear with respect to `rowIndex`.
*   **Space Complexity: O(rowIndex)**
    *   A list `row` of size `rowIndex + 1` is created and stored.
    *   The space required grows linearly with `rowIndex`.

### 5. Edge Cases & Correctness

*   **`rowIndex = 0`:**
    *   `row = [0]` (size 1), `row[0] = 1`.
    *   The loop `range(1, 1)` does not execute.
    *   Returns `[1]`. **Correct**, as the 0th row of Pascal's Triangle is `[1]`.
*   **`rowIndex = 1`:**
    *   `row = [0, 0]` (size 2), `row[0] = 1`.
    *   Loop `r = 1`: `current_val = 1 * (1 - 1 + 1) // 1 = 1`. `row[1] = 1`.
    *   Returns `[1, 1]`. **Correct**, as the 1st row is `[1, 1]`.
*   **Non-negative `rowIndex`:** The problem typically specifies `rowIndex >= 0`. The code implicitly assumes this and handles it correctly. A negative `rowIndex` would require explicit error handling if allowed.
*   **Large `rowIndex`:** Python's arbitrary-precision integers ensure that intermediate and final results for large `rowIndex` (where binomial coefficients can become very large) do not overflow, which is crucial for correctness.

### 6. Improvements & Alternatives

*   **Readability of `current_val`:** While functional, `current_val` could be renamed to something like `prev_coeff` or `last_computed_coeff` to more clearly indicate its role as the preceding binomial coefficient being used in the calculation.
*   **Alternative Calculation Method (In-place from previous row):**
    One could simulate the "sum of two numbers above" rule of Pascal's Triangle by starting with `[1]` and iteratively building the row. This requires updating elements in-place carefully, usually from right to left to avoid using already-updated values:
    ```python
    def getRow_alternative(self, rowIndex: int) -> List[int]:
        row = [1]
        for i in range(1, rowIndex + 1):
            row.append(1) # The last element is always 1
            for j in range(len(row) - 2, 0, -1): # Update from right to left, excluding first and last 1s
                row[j] = row[j] + row[j-1]
        return row
    ```
    This alternative also has O(rowIndex) time and O(rowIndex) space complexity. The provided solution, however, is arguably cleaner as it directly applies the combinatorial formula without complex in-place updates.
*   **No significant performance improvements are apparent for this problem statement.** The current solution is already optimal in terms of Big-O time and space complexity for generating the entire row.

### 7. Security/Performance Notes

*   **Python's Arbitrary-Precision Integers:** This is a key advantage. Unlike languages with fixed-size integers (e.g., C++, Java `int`), Python automatically handles arbitrarily large integer values. This prevents overflow errors when `rowIndex` is large and the binomial coefficients become enormous, ensuring mathematical correctness without requiring manual big-integer libraries.
*   **Integer Division `//`:** The use of integer division is critical. If `/` were used, it would perform float division, introducing floating-point inaccuracies and resulting in incorrect (non-integer) outputs for binomial coefficients, which must be integers. This is a correctness, not just a performance, concern.

### Code:
```python
from typing import List

class Solution:
    def getRow(self, rowIndex: int) -> List[int]:
        row = [0] * (rowIndex + 1)
        row[0] = 1
        
        current_val = 1
        for r in range(1, rowIndex + 1):
            current_val = current_val * (rowIndex - r + 1) // r
            row[r] = current_val
            
        return row
```
