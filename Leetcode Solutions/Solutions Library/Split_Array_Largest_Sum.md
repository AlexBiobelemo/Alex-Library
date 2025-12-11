## Split Array Largest Sum
**Language:** python
**Tags:** python,binary search,greedy,array

### Description:
This code solves the "Split Array Largest Sum" problem. Given an array `nums` of non-negative integers and an integer `k`, the goal is to split `nums` into `k` non-empty continuous subarrays such that the largest sum among these `k` subarrays is minimized.

---

### 1. Overview & Intent

The primary goal of the `splitArray` function is to find the minimum possible value for the maximum sum among all subarrays when `nums` is split into exactly `k` parts. This is a classic optimization problem that often involves a search over the possible answer range.

---

### 2. How It Works

The solution employs a **binary search on the answer space**. Instead of searching for an index, it searches for the optimal value of the *maximum sum allowed* for any subarray.

*   **`can_split(max_sum_allowed: int) -> bool` helper function**:
    *   This function determines if it's possible to split `nums` into `k` or fewer subarrays, with none of their sums exceeding `max_sum_allowed`.
    *   It iterates through `nums`, maintaining `current_sum` for the active subarray and `num_subarrays` count.
    *   If adding the current `num` to `current_sum` keeps it within `max_sum_allowed`, the `num` is added.
    *   Otherwise, a new subarray is started, `num_subarrays` is incremented, and `current_sum` is reset to `num`.
    *   It returns `True` if `num_subarrays` is less than or equal to `k`, indicating that `max_sum_allowed` is a feasible upper limit.

*   **Binary Search**:
    *   **Search Space**:
        *   `left`: The smallest possible maximum sum is `max(nums)`. This occurs if `k` is large enough that each element can be its own subarray.
        *   `right`: The largest possible maximum sum is `sum(nums)`. This occurs if `k=1`, and the entire array forms a single subarray.
    *   The `while left <= right` loop performs a standard binary search.
    *   `mid` is calculated.
    *   `can_split(mid)` is called:
        *   If `True`: It means `mid` is a *possible* answer, or an even smaller maximum sum might be achievable. So, `ans` is updated to `mid`, and the search continues in the lower half (`right = mid - 1`).
        *   If `False`: It means `mid` is too small to split the array into `k` or fewer subarrays. The search must continue in the upper half (`left = mid + 1`).
    *   The loop continues until `left > right`, and `ans` will hold the smallest `max_sum_allowed` for which `can_split` returned `True`.

---

### 3. Key Design Decisions

*   **Binary Search on the Answer Space**: This is the core algorithmic choice. It's suitable because the problem asks for the minimum value of a maximum, and the `can_split` predicate exhibits monotonicity: if an array can be split with a maximum sum `X`, it can also be split with any maximum sum `Y > X`.
*   **Greedy `can_split` Function**: The helper function uses a greedy approach to count subarrays. By trying to fit as many numbers as possible into the `current_sum` before starting a new subarray, it minimizes the number of subarrays needed for a given `max_sum_allowed`. This greedy strategy correctly determines if the `max_sum_allowed` is feasible.
*   **Well-defined Search Bounds**: `max(nums)` for `left` and `sum(nums)` for `right` correctly define the absolute minimum and maximum possible values for the target answer, ensuring the optimal solution is within this range.

---

### 4. Complexity

*   **Time Complexity**:
    *   The `can_split` function iterates through `nums` once, making it O(N), where N is `len(nums)`.
    *   The binary search performs `log(Range)` iterations, where `Range` is `sum(nums) - max(nums)`. Let `S = sum(nums)`. The number of iterations is approximately `log(S)`.
    *   Total Time Complexity: **O(N * log(S))**.
*   **Space Complexity**:
    *   The solution uses a few constant-size variables.
    *   Auxiliary Space Complexity: **O(1)**.

---

### 5. Edge Cases & Correctness

*   **`k = 1`**: The array must be split into one subarray. `can_split` will return `True` only when `max_sum_allowed >= sum(nums)`. The binary search will correctly converge to `sum(nums)`.
*   **`k = len(nums)`**: Each element will be its own subarray. `can_split` will return `True` when `max_sum_allowed >= max(nums)`. The binary search will correctly converge to `max(nums)`.
*   **Single element in `nums` (e.g., `nums = [X]`, `k = 1`)**: `left` and `right` will both be `X`. `mid` will be `X`. `can_split(X)` will be true. `ans` becomes `X`, `right` becomes `X-1`, loop terminates. Correctly returns `X`.
*   **All elements are positive**: The problem constraints typically ensure `nums` contains non-negative integers. The logic holds, as sums are always increasing or resetting.
*   **Individual element greater than `max_sum_allowed`**: The `left = max(nums)` initialisation for the binary search range is crucial. It ensures that `mid` will never be less than any individual number in `nums`. If `mid` were, say, `5` and `nums` contained `10`, then `can_split(5)` would correctly return `False` because `10` itself is greater than `5`, and it needs to form its own subarray of sum `10`. The loop logic `current_sum = num` handles the start of a new subarray correctly.

---

### 6. Improvements & Alternatives

*   **Readability**: The variable names are clear. Comments could be added, especially to explain the bounds of the binary search (`left = max(nums)`, `right = sum(nums)`) and the monotonicity property.
*   **Performance**: The current approach is highly efficient for typical constraints. Alternative solutions like Dynamic Programming (e.g., `dp[i][j]` for min max sum of splitting `nums[:i]` into `j` parts) exist but usually have a higher time complexity (e.g., O(N^2 * k)), making the binary search approach generally superior.
*   **No significant structural improvements are evident** without changing the core algorithm, as the current solution is well-optimized.

---

### 7. Security/Performance Notes

*   **Performance**: The O(N log S) complexity is optimal for this problem, making it suitable for large input arrays where `sum(nums)` is also large but `N` is the dominant factor.
*   **Security**: This code is a pure computational algorithm and does not handle external input, file I/O, network requests, or user authentication. Therefore, typical security concerns (e.g., injection attacks, data breaches) are not applicable. The primary concern is correctness for all valid inputs, which has been addressed.

### Code:
```python
class Solution:
    def splitArray(self, nums: List[int], k: int) -> int:
        
        def can_split(max_sum_allowed: int) -> bool:
            current_sum = 0
            num_subarrays = 1
            
            for num in nums:
                if current_sum + num <= max_sum_allowed:
                    current_sum += num
                else:
                    num_subarrays += 1
                    current_sum = num
            
            return num_subarrays <= k

        left = max(nums)
        right = sum(nums)
        
        ans = right
        
        while left <= right:
            mid = (left + right) // 2
            
            if can_split(mid):
                ans = mid
                right = mid - 1
            else:
                left = mid + 1
                
        return ans
```
