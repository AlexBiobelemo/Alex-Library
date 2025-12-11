## Binary Search
**Language:** python
**Tags:** python,binary search,array

### Description:
This Python code implements the classic Binary Search algorithm to find a target value in a sorted list of integers.

---

### 1. Overview & Intent

*   **Purpose:** To efficiently locate the index of a `target` integer within a sorted list of integers (`nums`).
*   **Input:**
    *   `nums`: A list of integers, assumed to be sorted in ascending order.
    *   `target`: The integer value to search for.
*   **Output:**
    *   An integer representing the index of the `target` if found.
    *   `-1` if the `target` is not present in the list.

---

### 2. How It Works

The algorithm iteratively narrows down the search space by repeatedly dividing the list in half.

1.  **Initialization:** Two pointers, `left` and `right`, are initialized to the beginning (`0`) and end (`len(nums) - 1`) of the list, respectively. These pointers define the current search range.
2.  **Looping Condition:** The search continues as long as `left` is less than or equal to `right`, meaning there's still a valid search space (including a single element).
3.  **Midpoint Calculation:** In each iteration, a `mid` index is calculated as `left + (right - left) // 2`. This calculation is preferred over `(left + right) // 2` to prevent potential integer overflow in languages with fixed-size integers, though less critical in Python.
4.  **Comparison:**
    *   If `nums[mid]` is equal to `target`, the target is found, and its index `mid` is returned.
    *   If `nums[mid]` is less than `target`, the target must be in the right half of the current search space (if it exists). The `left` pointer is updated to `mid + 1` to exclude `mid` and the left half.
    *   If `nums[mid]` is greater than `target`, the target must be in the left half. The `right` pointer is updated to `mid - 1` to exclude `mid` and the right half.
5.  **Termination:** If the loop finishes without finding the target (i.e., `left > right`), it means the target is not in the list, and `-1` is returned.

---

### 3. Key Design Decisions

*   **Algorithm Choice:** Binary Search. This is the optimal choice for searching in a *sorted* array due to its logarithmic time complexity.
*   **In-Place Search:** The algorithm operates directly on the input list without creating significant additional data structures, contributing to its excellent space complexity.
*   **Inclusive Search Range:** The `while left <= right` condition and `mid + 1` / `mid - 1` adjustments ensure that the entire range, including the `mid` element and boundary elements, is correctly considered and eventually checked or excluded.
*   **Midpoint Calculation:** Using `left + (right - left) // 2` is a robust way to compute the midpoint, minimizing risk of overflow.

---

### 4. Complexity

*   **Time Complexity: O(log N)**
    *   In each step, the search space is approximately halved. For a list of `N` elements, this means the number of comparisons grows logarithmically with `N`.
*   **Space Complexity: O(1)**
    *   The algorithm uses a constant amount of extra space for variables like `left`, `right`, and `mid`, regardless of the input list size.

---

### 5. Edge Cases & Correctness

*   **Empty list (`nums = []`):**
    *   `left` becomes `0`, `right` becomes `-1`.
    *   The `while left <= right` condition (`0 <= -1`) is immediately false.
    *   Returns `-1`, which is correct.
*   **Single-element list (`nums = [5]`, `target = 5`):**
    *   `left=0`, `right=0`. `mid=0`. `nums[0] == target`. Returns `0`. Correct.
*   **Single-element list (`nums = [5]`, `target = 3`):**
    *   `left=0`, `right=0`. `mid=0`. `nums[0] > target`. `right` becomes `-1`.
    *   Loop terminates (`0 <= -1` is false). Returns `-1`. Correct.
*   **Target at the beginning or end of the list:** Handled correctly by the pointer adjustments.
*   **Target not present:** The `left` and `right` pointers will eventually cross (`left > right`), leading to loop termination and a `-1` return. Correct.
*   **Duplicate elements:** The standard binary search finds *an* index if the target is present. It does not guarantee the first or last occurrence without modification, but for simply finding *an* instance, it's correct.
*   **Pre-requisite of a Sorted Array:** The correctness of binary search *critically depends* on the input array `nums` being sorted. If `nums` is not sorted, the algorithm will yield incorrect results.

---

### 6. Improvements & Alternatives

*   **Docstrings/Comments:** For production code, adding a docstring explaining the function's purpose, parameters, return values, and especially the *pre-requisite of a sorted array* would enhance clarity.
*   **Explicit Empty List Check:** While the current code correctly handles an empty list, adding an explicit `if not nums: return -1` at the beginning can sometimes improve readability by addressing this edge case upfront.
*   **Python's `bisect` module:** For more advanced or compact solutions in Python, the `bisect` module offers highly optimized binary search functions:
    ```python
    import bisect

    class Solution:
        def search(self, nums: List[int], target: int) -> int:
            idx = bisect.bisect_left(nums, target) # Finds insertion point for target
            if idx < len(nums) and nums[idx] == target:
                return idx
            return -1
    ```
    This alternative is more "Pythonic" and uses a C-optimized implementation, but the core logic remains the same.

---

### 7. Security/Performance Notes

*   **Performance:** The code is already optimally performing for its task (O(log N) time complexity). There are no further significant algorithmic performance improvements possible for searching in a sorted array without changing the problem's constraints (e.g., pre-hashing elements if lookups were vastly more frequent than modifications).
*   **Security:** This algorithm is purely computational and does not interact with external systems, user input beyond the direct parameters, or sensitive data in a way that would introduce security vulnerabilities. It's inherently safe from a security standpoint.

### Code:
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = left + (right - left) // 2
            
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        return -1
```
