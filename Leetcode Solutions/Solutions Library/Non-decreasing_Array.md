## Non-decreasing Array
**Language:** python
**Tags:** python,oop,arrays,greedy,single-pass

### Description:
This Python code defines a method `checkPossibility` within a `Solution` class that determines if an array can be made non-decreasing by modifying at most one element.

---

### 1. Overview & Intent

The primary goal of this code is to check if a given list of integers (`nums`) can be transformed into a non-decreasing array by changing the value of *at most one* element. A non-decreasing array means that for all `i`, `nums[i] <= nums[i+1]`.

---

### 2. How It Works

The algorithm iterates through the array, keeping track of how many "violations" (where `nums[i] > nums[i+1]`) it encounters.

1.  **Initialize `violations` counter**: A variable `violations` is set to 0. This counter tracks how many times the non-decreasing property is broken.
2.  **Iterate and detect violations**: The code loops from the first element up to the second-to-last element (`n-1`).
    *   If `nums[i] > nums[i+1]`, a violation is found.
3.  **Handle violations**:
    *   Increment `violations`.
    *   **Early Exit**: If `violations` becomes greater than 1, it means more than one change would be required, so the function immediately returns `False`.
    *   **Attempt to fix the first violation**: If it's the *first* violation (`violations == 1`), the algorithm tries to fix it by modifying either `nums[i]` or `nums[i+1]` in-place.
        *   **Condition for decreasing `nums[i]`**: If `i` is the first element (`i == 0`) OR if the element *before* `nums[i]` (`nums[i-1]`) is less than or equal to `nums[i+1]`, then it's safe and generally preferable to decrease `nums[i]` to `nums[i+1]`. This maintains `nums[i-1] <= nums[i]` (if `i > 0`) and fixes `nums[i] <= nums[i+1]`.
        *   **Condition for increasing `nums[i+1]`**: If the above condition is not met (i.e., `i > 0` AND `nums[i-1] > nums[i+1]`), it means decreasing `nums[i]` would still violate `nums[i-1] <= nums[i]`. In this specific case, the only option is to increase `nums[i+1]` to `nums[i]`.
4.  **Final Check**: If the loop completes and `violations` is 0 or 1, it means the array could be made non-decreasing with at most one modification, so the function returns `True`.

---

### 3. Key Design Decisions

*   **In-place Modification**: The array `nums` is modified directly. This allows the subsequent iterations to consider the "fixed" array, which is crucial for determining if the *single* modification was sufficient.
*   **Greedy Approach**: At the first point of violation, the code makes a "greedy" choice about which element to modify (`nums[i]` or `nums[i+1]`). The logic prioritizes decreasing `nums[i]` because it is less likely to create new violations with elements *after* `nums[i+1]`. Only if decreasing `nums[i]` would create a *new* violation with `nums[i-1]` does it resort to increasing `nums[i+1]`.
*   **Early Exit**: The `if violations > 1: return False` statement allows the algorithm to stop as soon as it determines that more than one change is needed, optimizing performance for such cases.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The code iterates through the `nums` list exactly once (from `i = 0` to `n-2`).
    *   Each step within the loop involves constant-time operations (comparisons, assignments).
    *   Therefore, the time taken scales linearly with the number of elements `N` in the input array.
*   **Space Complexity: O(1)**
    *   The algorithm uses a fixed amount of extra space regardless of the input array size (a few integer variables like `violations`, `n`, `i`).
    *   The in-place modification of `nums` does not count towards auxiliary space complexity.

---

### 5. Edge Cases & Correctness

The algorithm handles various edge cases correctly:

*   **Empty or Single-Element Array**:
    *   If `nums` is empty or has one element, `n-1` will be -1 or 0. `range(n-1)` will be `range(-1)` or `range(0)`, so the loop won't run. `violations` remains 0, and `True` is returned, which is correct (empty and single-element arrays are non-decreasing).
*   **Already Non-Decreasing Array**:
    *   If `nums` is already non-decreasing (e.g., `[1, 2, 3, 4]`), `violations` will remain 0, and `True` is returned. Correct.
*   **Exactly One Fixable Violation (by decreasing `nums[i]`)**:
    *   Example: `[4, 2, 3]`. At `i=0`, `4 > 2`. `violations` becomes 1. `i == 0` is true, so `nums[0]` becomes `2`. Array is now `[2, 2, 3]`. Loop finishes, returns `True`. Correct.
    *   Example: `[1, 5, 3, 4]`. At `i=1`, `5 > 3`. `violations` becomes 1. `i != 0`. `nums[i-1]` (1) `<= nums[i+1]` (3) is true. So `nums[1]` becomes `3`. Array is `[1, 3, 3, 4]`. Loop finishes, returns `True`. Correct.
*   **Exactly One Fixable Violation (by increasing `nums[i+1]`)**:
    *   Example: `[3, 4, 2, 5]`. At `i=1`, `4 > 2`. `violations` becomes 1. `i != 0`. `nums[i-1]` (3) `> nums[i+1]` (2) is true, so the `else` branch is taken. `nums[2]` becomes `nums[1]` (4). Array is `[3, 4, 4, 5]`. Loop finishes, returns `True`. Correct.
*   **Multiple Violations**:
    *   Example: `[4, 2, 1]`. At `i=0`, `4 > 2`. `violations` is 1. `nums[0]` becomes `2`. Array is `[2, 2, 1]`. At `i=1`, `2 > 1`. `violations` becomes 2. Returns `False`. Correct.

---

### 6. Improvements & Alternatives

*   **Readability**: The core `if/else` block for handling the first violation is dense. Adding comments to explicitly state why each modification choice is made could enhance clarity.
    ```python
    # ... inside the loop when violations == 1 ...
    if i == 0 or nums[i-1] <= nums[i+1]:
        # Option 1: Decrease nums[i]. This is preferred as it's less likely to
        # affect subsequent elements negatively, and satisfies nums[i-1] <= nums[i]
        # (if i > 0)
        nums[i] = nums[i+1]
    else:
        # Option 2: Increase nums[i+1]. This is necessary if decreasing nums[i]
        # would create a new violation with nums[i-1].
        nums[i+1] = nums[i]
    ```
*   **Alternative Approach (Simulated Modification)**: Instead of modifying the array in-place, one could store the index of the first violation. If only one violation is found, create two temporary arrays (or simulate two checks): one where `nums[i]` is changed, and one where `nums[i+1]` is changed, and then check if *either* of these new arrays is non-decreasing using a helper function. This would avoid direct modification but would likely involve more overhead (copying arrays or two full passes) or more complex logic than the current greedy in-place approach. The current solution's greedy strategy is quite elegant for achieving O(N) time.

---

### 7. Security/Performance Notes

*   **Security**: No apparent security vulnerabilities. The code operates purely on numerical input within memory and does not interact with external systems, files, or user input in a way that would introduce common security risks (e.g., injection, DoS).
*   **Performance**: The performance is optimal for this problem, achieving O(N) time complexity. It processes each element at most once and makes constant-time decisions, making it highly efficient for large inputs.

### Code:
```python
from typing import List

class Solution:
    def checkPossibility(self, nums: List[int]) -> bool:
        violations = 0
        n = len(nums)

        for i in range(n - 1):
            if nums[i] > nums[i+1]:
                violations += 1
                if violations > 1:
                    return False

                if i == 0 or nums[i-1] <= nums[i+1]:
                    nums[i] = nums[i+1]
                else:
                    nums[i+1] = nums[i]
        
        return True
```
