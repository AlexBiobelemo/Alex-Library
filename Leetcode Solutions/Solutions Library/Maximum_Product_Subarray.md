## Maximum Product Subarray
**Language:** python
**Tags:** python,oop,dynamic programming,array

### Description:
This code implements a classic dynamic programming approach to find the maximum product of a contiguous subarray within a given list of integers. It cleverly handles the complexities introduced by negative numbers.

---

### 1. Overview & Intent

*   **Problem**: Given an array of integers `nums`, find the contiguous subarray within it that has the largest product.
*   **Output**: The maximum product found.
*   **Challenge**: Unlike maximum sum subarray problems, negative numbers flip the sign of products. A small negative number multiplied by another negative number can result in a large positive number. This requires tracking both maximum and minimum products ending at the current position.

### 2. How It Works

The algorithm iterates through the array, keeping track of three key values:

*   **`max_so_far`**: The maximum product of a subarray ending at the *current* position.
*   **`min_so_far`**: The minimum product of a subarray ending at the *current* position. This is crucial because a very small (negative) product, when multiplied by another negative number, can become a very large positive product.
*   **`overall_max`**: The absolute maximum product found across *any* subarray encountered so far.

The steps are:

1.  **Initialization**: All three variables (`max_so_far`, `min_so_far`, `overall_max`) are initialized with the first element of the array (`nums[0]`).
2.  **Iteration**: The code then iterates through the rest of the array starting from the second element (`nums[1]`).
3.  **Negative Number Swap**: If the current number `num` is negative, `max_so_far` and `min_so_far` are swapped. This is because a negative number will turn a large positive product (`max_so_far`) into a large negative product, and a large negative product (`min_so_far`) into a large positive product. Their roles effectively reverse.
4.  **Update `max_so_far`**: The new `max_so_far` is the maximum of:
    *   The `num` itself (starting a new subarray from `num`).
    *   The product of `max_so_far` and `num` (extending the previous maximum product subarray).
5.  **Update `min_so_far`**: The new `min_so_far` is the minimum of:
    *   The `num` itself (starting a new subarray from `num`).
    *   The product of `min_so_far` and `num` (extending the previous minimum product subarray).
6.  **Update `overall_max`**: The `overall_max` is updated by taking the maximum between its current value and the newly calculated `max_so_far`.
7.  **Return**: After iterating through all elements, `overall_max` holds the largest product found.

### 3. Key Design Decisions

*   **Dynamic Programming**: The core idea is that the maximum and minimum products ending at the current index `i` can be derived from the maximum and minimum products ending at `i-1`. This exhibits the optimal substructure and overlapping subproblems characteristics of dynamic programming.
*   **Tracking `min_so_far`**: This is the most crucial design choice that distinguishes this problem from the maximum sum subarray problem. Without tracking the minimum product, we would fail to correctly handle cases involving multiple negative numbers (e.g., `[-2, -3]`).
*   **Space Optimization**: Instead of using two full DP arrays (one for max products, one for min products), the solution uses only a few variables (`max_so_far`, `min_so_far`, `overall_max`) to store the necessary state from the previous step. This achieves O(1) space complexity.

### 4. Complexity

*   **Time Complexity**: O(N)
    *   The code iterates through the `nums` array exactly once. Each step inside the loop involves a constant number of operations (comparisons, multiplications, assignments).
*   **Space Complexity**: O(1)
    *   The algorithm uses a fixed number of variables (`max_so_far`, `min_so_far`, `overall_max`, `num`) regardless of the input array's size.

### 5. Edge Cases & Correctness

The algorithm correctly handles various edge cases:

*   **Single Element Array**: `nums = [x]`
    *   `max_so_far`, `min_so_far`, `overall_max` are all initialized to `x`. The loop `range(1, len(nums))` does not run. `x` is returned, which is correct.
*   **All Positive Numbers**: `nums = [2, 3, 4]`
    *   `max_so_far` will continuously grow as `num` is multiplied by `max_so_far`. `min_so_far` will also grow but will always be less than or equal to `max_so_far`. Correctly finds `24`.
*   **All Negative Numbers**: `nums = [-2, -3, -1]`
    *   `[-2, -3]` yields `6`. `overall_max` becomes `6`.
    *   When `-1` comes, `max_so_far` becomes `3` (`max(-1, -3*-1)`), `min_so_far` becomes `-6` (`min(-1, 6*-1)`). `overall_max` remains `6`. Correct.
*   **Array with Zeros**: `nums = [2, 3, -2, 0, 4, 5]`
    *   When `num` is `0`: `max_so_far` becomes `max(0, prev_max*0) = 0`. `min_so_far` becomes `min(0, prev_min*0) = 0`. This effectively "resets" the product chain. Any subarray containing `0` will have a product of `0`. The algorithm correctly finds the maximum products of subarrays on either side of the `0` (e.g., `[2,3,-2]` -> `6`, and `[4,5]` -> `20`), eventually returning `20`.
*   **Mixed Positives, Negatives, and Zeros**: Handled by the logic above.

### 6. Improvements & Alternatives

*   **Readability of the Swap**: The comment clearly explains the purpose of `if num < 0: max_so_far, min_so_far = min_so_far, max_so_far`. An alternative way to express the updates for `max_so_far` and `min_so_far` without the conditional swap could be:
    ```python
    # Calculate potential next max and min values
    p1 = num
    p2 = max_so_far * num
    p3 = min_so_far * num
    
    max_so_far = max(p1, p2, p3)
    min_so_far = min(p1, p2, p3)
    ```
    This approach considers all three possibilities for the next max/min product (the current number itself, or extending with the previous max/min product) directly. While arguably more explicit, the original code's conditional swap is a common and efficient idiom for this specific problem, often considered elegant by those familiar with the pattern. The current solution is already very good in terms of readability and performance.

### 7. Security/Performance Notes

*   **Integer Overflow**: In languages with fixed-size integer types (like C++, Java), intermediate products `max_so_far * num` or `min_so_far * num` could potentially exceed the maximum value an `int` can hold, leading to incorrect results. Python integers handle arbitrary precision, so this is not a concern for Python code.
*   **Optimal Performance**: The O(N) time and O(1) space complexity achieved by this solution are optimal, as every element of the input array must be processed at least once to determine the maximum product. No further significant performance improvements are possible with this approach.

### Code:
```python
from typing import List

class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        max_so_far = nums[0]
        min_so_far = nums[0]
        overall_max = nums[0]

        for i in range(1, len(nums)):
            num = nums[i]

            # If the current number is negative, it will swap the roles of max_so_far and min_so_far
            # A previously small negative product can become a large positive product
            # A previously large positive product can become a large negative product
            if num < 0:
                max_so_far, min_so_far = min_so_far, max_so_far

            # Update max_so_far and min_so_far for the current number
            # max_so_far is either the current number itself, or the current number multiplied by the previous max_so_far
            max_so_far = max(num, max_so_far * num)
            # min_so_far is either the current number itself, or the current number multiplied by the previous min_so_far
            min_so_far = min(num, min_so_far * num)

            # Update the overall maximum product found so far
            overall_max = max(overall_max, max_so_far)

        return overall_max
```
