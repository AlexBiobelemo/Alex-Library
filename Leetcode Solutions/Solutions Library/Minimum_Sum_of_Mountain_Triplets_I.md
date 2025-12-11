## Minimum Sum of Mountain Triplets I
**Language:** python
**Tags:** python,oop,dynamic programming,array

### Description:
### 1. Overview & Intent

This code aims to find the minimum sum of a "mountain triplet" within a given list of integers `nums`. A mountain triplet `(nums[i], nums[j], nums[k])` is defined by the conditions:
*   `i < j < k` (indices must be strictly increasing)
*   `nums[i] < nums[j]` (the first element is smaller than the middle)
*   `nums[k] < nums[j]` (the third element is smaller than the middle)

The method returns this minimum sum. If no such triplet can be found, it returns `-1`.

### 2. How It Works

The solution uses a dynamic programming-like approach with two auxiliary arrays to efficiently find the required minimums:

1.  **Handle Base Case**: If the input list `nums` has fewer than 3 elements, a triplet cannot be formed, so it immediately returns `-1`.
2.  **Precompute Left Minimums**:
    *   It creates an array `left_min_prefix` of the same size as `nums`, initialized with `math.inf`.
    *   It iterates from the second element (`i = 1`) to the end of the list.
    *   `left_min_prefix[i]` stores the minimum value encountered in `nums` from index `0` up to `i-1`. This means `left_min_prefix[j]` will hold `min(nums[0], ..., nums[j-1])`, representing the smallest possible `nums[i]` for a given `nums[j]`.
3.  **Precompute Right Minimums**:
    *   It creates an array `right_min_suffix` of the same size as `nums`, also initialized with `math.inf`.
    *   It iterates from the second-to-last element (`i = n - 2`) down to the beginning of the list.
    *   `right_min_suffix[i]` stores the minimum value encountered in `nums` from `i+1` up to `n-1`. This means `right_min_suffix[j]` will hold `min(nums[j+1], ..., nums[n-1])`, representing the smallest possible `nums[k]` for a given `nums[j]`.
4.  **Find Minimum Triplet Sum**:
    *   It initializes `min_total_sum` to `math.inf`.
    *   It then iterates through `nums` with `j` as the potential middle element, from index `1` to `n-2` (as `j` cannot be the first or last element).
    *   For each `nums[j]`:
        *   It checks if `left_min_prefix[j] < nums[j]` (satisfying `nums[i] < nums[j]`) AND `right_min_suffix[j] < nums[j]` (satisfying `nums[k] < nums[j]`).
        *   If both conditions are met, a valid triplet exists. The current sum is `left_min_prefix[j] + nums[j] + right_min_suffix[j]`.
        *   `min_total_sum` is updated with the minimum of its current value and this `current_sum`.
5.  **Return Result**: Finally, if `min_total_sum` is still `math.inf` (meaning no valid triplet was found), it returns `-1`. Otherwise, it returns `min_total_sum`.

### 3. Key Design Decisions

*   **Prefix/Suffix Minimum Arrays**: This is the core design choice.
    *   **Rationale**: Instead of iterating through all possible `(i, j, k)` triplets (which would be O(N^3)), or for each `j` scanning `0...j-1` and `j+1...n-1` (which would be O(N^2)), pre-calculating the minimums for the left and right sides allows constant-time lookup for `nums[i]` and `nums[k]` for any given `nums[j]`.
    *   **Trade-offs**: This approach significantly improves time complexity from O(N^2) or O(N^3) to O(N) by using O(N) auxiliary space. For problems where space is extremely constrained, this might not be viable, but for typical constraints, it's a very common and efficient pattern.
*   **Initialization with `math.inf`**: Ensures that any actual number from the input `nums` will be smaller than the initial minimum, allowing for correct comparison and update.
*   **Edge Case Handling (n < 3)**: Explicitly checks for the minimum required length, preventing index out-of-bounds errors and providing the specified return value.
*   **Final `math.inf` Check**: Clearly distinguishes between a sum of `math.inf` (no triplet found) and a potentially very large but valid sum.

### 4. Complexity

*   **Time Complexity**:
    *   `len(nums)`: O(1)
    *   `left_min_prefix` array creation and loop: O(N)
    *   `right_min_suffix` array creation and loop: O(N)
    *   Main loop for `j`: O(N)
    *   Overall Time Complexity: **O(N)**, where N is the length of `nums`.

*   **Space Complexity**:
    *   `left_min_prefix`: O(N)
    *   `right_min_suffix`: O(N)
    *   Overall Space Complexity: **O(N)**, due to the two auxiliary arrays.

### 5. Edge Cases & Correctness

*   **`n < 3`**: Handled explicitly; returns `-1`. Correct.
*   **No valid triplet found**: `min_total_sum` remains `math.inf`, correctly resulting in a `-1` return.
    *   *Example*: `nums = [5, 4, 3, 2, 1]` (strictly decreasing) or `nums = [1, 2, 3, 4, 5]` (strictly increasing).
*   **Smallest valid `n` (n=3)**:
    *   *Example*: `nums = [1, 5, 2]`.
        *   `left_min_prefix` would be `[inf, 1, 1]`
        *   `right_min_suffix` would be `[2, 2, inf]`
        *   For `j=1` (value 5): `left_min_prefix[1]` (1) < `nums[1]` (5) and `right_min_suffix[1]` (2) < `nums[1]` (5). Both true.
        *   `current_sum = 1 + 5 + 2 = 8`. `min_total_sum` becomes 8. Correct.
*   **Duplicate numbers**: The logic correctly handles duplicates. `min` operations work fine, and the strict inequality `left_min_prefix[j] < nums[j]` ensures `nums[i]` is strictly smaller.
*   **Negative numbers/Zero**: The algorithm works correctly with negative numbers and zero, as comparisons and sums are standard. `math.inf` remains the largest value.
*   **Large integer values**: Python's arbitrary precision integers prevent overflow issues that might occur in languages with fixed-size integer types.

### 6. Improvements & Alternatives

*   **Readability**: The code is quite readable. Variable names are clear. No significant readability improvements are necessary.
*   **Space Optimization (Not Trivial)**: While the O(N) space is generally acceptable for O(N) time, it's theoretically possible to achieve O(1) space if you fix `j` and then iterate `i` and `k` with two pointers, but this would increase time complexity. For this specific problem structure (finding `min(val_left) + val_j + min(val_right)`), the prefix/suffix sum approach is usually preferred for its time efficiency. A truly O(1) space solution might involve multiple linear passes, but would still be O(N) time overall. The current O(N) space solution is optimal for time.
*   **Alternative (Less Efficient)**: A brute-force O(N^3) solution would iterate through all `i`, `j`, `k` triplets and check conditions. An O(N^2) solution could iterate `j` from `1` to `n-2`, and for each `j`, iterate `i` from `0` to `j-1` to find `min_left`, and `k` from `j+1` to `n-1` to find `min_right`. The current solution is superior to both.
*   **Minor stylistic choice**: The final `if/else` block could be simplified slightly to `return min_total_sum if min_total_sum != math.inf else -1`. This is a matter of personal preference.

### 7. Security/Performance Notes

*   **Security**: There are no apparent security concerns. The code operates solely on the input list, performs numerical computations, and does not interact with external systems, files, or user inputs in a way that would introduce vulnerabilities.
*   **Performance**: The O(N) time complexity is optimal for this problem, as every element potentially needs to be considered at least once to determine if it can be part of a triplet or a minimum for one. The overhead of creating two additional arrays is linear and acceptable. For extremely large `N`, the memory usage might become a factor, but for typical competitive programming constraints (N up to 10^5 or 10^6), it's well within limits.

### Code:
```python
import math
from typing import List

class Solution:
    def minimumSum(self, nums: List[int]) -> int:
        n = len(nums)
        if n < 3:
            return -1

        left_min_prefix = [math.inf] * n
        for i in range(1, n):
            left_min_prefix[i] = min(left_min_prefix[i-1], nums[i-1])

        right_min_suffix = [math.inf] * n
        for i in range(n - 2, -1, -1):
            right_min_suffix[i] = min(right_min_suffix[i+1], nums[i+1])

        min_total_sum = math.inf

        for j in range(1, n - 1):
            if left_min_prefix[j] < nums[j] and right_min_suffix[j] < nums[j]:
                current_sum = left_min_prefix[j] + nums[j] + right_min_suffix[j]
                min_total_sum = min(min_total_sum, current_sum)

        if min_total_sum == math.inf:
            return -1
        else:
            return min_total_sum
```
