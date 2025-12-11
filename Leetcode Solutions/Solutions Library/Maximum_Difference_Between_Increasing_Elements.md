## Maximum Difference Between Increasing Elements
**Language:** python
**Tags:** python,oop,greedy,list

### Description:
This code snippet efficiently finds the maximum difference between two numbers in a list, `nums[j] - nums[i]`, such that `j > i` and `nums[j] > nums[i]`. If no such pair exists, it returns -1.

---

### 1. Overview & Intent

*   **Goal:** Calculate the greatest difference between an element `nums[j]` and a preceding element `nums[i]` (where `j > i`), with the additional constraint that `nums[j]` must be strictly greater than `nums[i]`.
*   **Context:** This is a classic "buy low, sell high" or "maximum profit" type of problem, often encountered in technical interviews.
*   **Return Value:** The maximum difference found, or -1 if no pair satisfies the conditions (`j > i` and `nums[j] > nums[i]`).

---

### 2. How It Works

The algorithm uses a single pass through the list to keep track of the minimum value encountered *so far* and the maximum difference found.

*   **Initialization:**
    *   `n`: Stores the length of the input list `nums`.
    *   **Edge Case Check:** If `n` is less than 2, it's impossible to form a pair, so it immediately returns -1.
    *   `min_so_far`: Initialized to the first element of the list (`nums[0]`). This variable will always hold the smallest number encountered *up to the current point* in the iteration (excluding the current element itself, for the difference calculation).
    *   `max_diff`: Initialized to -1. This will store the maximum valid difference found.

*   **Iteration:**
    *   The code iterates through the list starting from the second element (`j` from 1 to `n-1`).
    *   **Difference Calculation:** For each `nums[j]`:
        *   It checks if `nums[j]` is greater than `min_so_far`. If it is, a valid difference `nums[j] - min_so_far` can be formed.
        *   `max_diff` is then updated to be the maximum of its current value and this new difference.
    *   **Update `min_so_far`:** After calculating potential differences with the *current* `min_so_far`, the `min_so_far` variable is updated to be the minimum of its current value and `nums[j]`. This ensures that `min_so_far` always reflects the minimum value encountered *before or at* the current index `j`.

*   **Result:** After the loop completes, `max_diff` holds the overall maximum difference that satisfies the conditions, or -1 if no such difference was found.

---

### 3. Key Design Decisions

*   **Algorithm:** Single-pass (greedy approach). This is a common and highly efficient pattern for this type of problem. It avoids nested loops, which would be less performant.
*   **State Management:** The use of `min_so_far` is crucial. It cleverly keeps track of the smallest element seen *before* the current element `nums[j]`, allowing for the `j > i` condition to be implicitly maintained.
*   **Initialization of `max_diff`:** Starting `max_diff` at -1 correctly handles the case where no valid difference (where `nums[j] > nums[i]`) can be found.

---

### 4. Complexity

*   **Time Complexity: O(n)**
    *   The code iterates through the `nums` list once (from the second element to the end).
    *   Each operation inside the loop (comparison, subtraction, `max`, `min`) takes constant time.
    *   Therefore, the total time complexity is directly proportional to the number of elements `n` in the input list.

*   **Space Complexity: O(1)**
    *   The algorithm uses a fixed number of variables (`n`, `min_so_far`, `max_diff`, `j`) regardless of the input list size.
    *   No auxiliary data structures that scale with `n` are used.

---

### 5. Edge Cases & Correctness

*   **Empty or Single-Element List (`n < 2`):**
    *   **Handled:** Yes. The `if n < 2: return -1` check explicitly covers this, returning -1 as no pair can be formed.
*   **List with All Decreasing Elements (e.g., `[5, 4, 3, 2, 1]`):**
    *   **Correctness:** `min_so_far` will always be `nums[j]` or less. The condition `nums[j] > min_so_far` will never be met, so `max_diff` will remain -1. This is correct, as no element is strictly greater than a preceding one.
*   **List with All Increasing Elements (e.g., `[1, 2, 3, 4, 5]`):**
    *   **Correctness:** `min_so_far` will always remain `1`. `max_diff` will be updated progressively (e.g., `2-1=1`, `3-1=2`, `4-1=3`, `5-1=4`). The final `max_diff` will be `5-1=4`. This is correct.
*   **List with Duplicate Elements (e.g., `[1, 5, 1, 5]`):**
    *   **Correctness:** `nums[j]` must be *strictly greater* than `min_so_far`. If `nums[j]` is equal to `min_so_far`, the `if` condition `nums[j] > min_so_far` will be false, and `max_diff` won't be updated by a difference of zero. This ensures only positive differences (from `nums[j] > nums[i]`) contribute to `max_diff`. For `[1,5,1,5]`:
        *   `min_so_far=1`, `max_diff=-1`
        *   `j=1, nums[1]=5`: `5 > 1`, `max_diff = max(-1, 5-1=4) = 4`. `min_so_far = min(1,5) = 1`.
        *   `j=2, nums[2]=1`: `1 !> 1`, `max_diff` unchanged. `min_so_far = min(1,1) = 1`.
        *   `j=3, nums[3]=5`: `5 > 1`, `max_diff = max(4, 5-1=4) = 4`. `min_so_far = min(1,5) = 1`.
        *   Result: 4. Correct.

---

### 6. Improvements & Alternatives

*   **Readability:** The current code is very readable and self-explanatory. Variable names are clear, and the logic is straightforward.
*   **Performance:** For this specific problem, an O(N) time complexity solution is optimal because every element must be examined at least once to determine the global minimum and potential differences. No significant performance improvements are possible with a different algorithm while adhering to the problem constraints.
*   **Alternative (Less Efficient):** A brute-force approach using nested loops would look like this:
    ```python
    max_diff = -1
    for i in range(n):
        for j in range(i + 1, n):
            if nums[j] > nums[i]:
                max_diff = max(max_diff, nums[j] - nums[i])
    return max_diff
    ```
    This approach is simpler to understand initially but has a time complexity of O(N^2), making it much slower for larger input lists compared to the O(N) single-pass solution provided.

---

### 7. Security/Performance Notes

*   **Security:** This code performs purely numerical computations on an input list. There are no security implications, such as injection vulnerabilities, reliance on external resources, or sensitive data handling.
*   **Performance:** The O(N) time and O(1) space complexity make this solution highly efficient and suitable for large inputs. It minimizes memory usage and avoids redundant calculations. This is an excellent solution from a performance perspective.

### Code:
```python
class Solution:
    def maximumDifference(self, nums: List[int]) -> int:
        n = len(nums)
        if n < 2:
            return -1

        min_so_far = nums[0]
        max_diff = -1

        for j in range(1, n):
            if nums[j] > min_so_far:
                max_diff = max(max_diff, nums[j] - min_so_far)
            min_so_far = min(min_so_far, nums[j])
        
        return max_diff
```
