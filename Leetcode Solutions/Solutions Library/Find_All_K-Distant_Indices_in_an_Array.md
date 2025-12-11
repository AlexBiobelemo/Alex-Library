## Find All K-Distant Indices in an Array
**Language:** python
**Tags:** python,oop,set,sorting

### Description:
This code snippet implements a solution to find all indices `i` in a list `nums` that are "k-distant" from any index `j` where `nums[j]` equals a specified `key`.

---

### 1. Overview & Intent

The primary goal of this code is to identify all indices `i` in the input list `nums` such that for each `i`, there exists at least one index `j` satisfying two conditions:
1. `nums[j]` is equal to the given `key`.
2. The absolute difference between `i` and `j` is less than or equal to `k` (i.e., `abs(i - j) <= k`).

The output must be a sorted list of these unique k-distant indices.

---

### 2. How It Works

The algorithm proceeds in several steps:

1.  **Initialization:** An empty `set` called `k_distant_indices_set` is created. Using a set is crucial for automatically handling unique indices.
2.  **Iterate through `nums` to find `key`:** The code iterates through the input list `nums` using an index `j` from `0` to `n-1` (where `n` is the length of `nums`).
3.  **Identify `key` occurrences:** If `nums[j]` is equal to the `key`, it means `j` is an index from which other indices can be k-distant.
4.  **Calculate k-distant range:** For each `j` where `nums[j] == key`, it calculates the range of indices `[start, end]` that are k-distant from `j`.
    *   `start` is determined by `max(0, j - k)` to ensure the index is not negative.
    *   `end` is determined by `min(n - 1, j + k)` to ensure the index does not exceed the list's bounds.
5.  **Add indices to set:** All indices `i` within the calculated `[start, end]` range (inclusive) are added to the `k_distant_indices_set`. The set automatically takes care of duplicates.
6.  **Convert to list and sort:** After checking all possible `j` values, the `k_distant_indices_set` is converted into a list. This list is then sorted in increasing order, as required by the problem.
7.  **Return result:** The sorted list of unique k-distant indices is returned.

---

### 3. Key Design Decisions

*   **Using a `set` for intermediate storage:**
    *   **Pros:** This is a good choice for collecting unique indices efficiently. The `add` operation in a set has an average time complexity of O(1), making it fast to insert elements and automatically handle duplicates.
    *   **Cons:** Sets do not maintain insertion order, which necessitates a final sorting step.
*   **Direct range calculation and addition:**
    *   For each occurrence of `key`, the code directly calculates the `k`-distant range and adds all individual indices within that range to the set. This is straightforward to implement and understand.

---

### 4. Complexity

Let `n` be the length of the `nums` list. Let `M` be the number of unique k-distant indices found (at most `n`).

*   **Time Complexity:**
    *   The outer loop iterates `n` times (for `j`).
    *   Inside the outer loop, if `nums[j] == key`, an inner loop iterates up to `2k + 1` times (from `j-k` to `j+k`). Each `set.add()` operation is O(1) on average.
    *   Therefore, the nested loops contribute `O(n * k)` to the time complexity in the worst case (e.g., if every element is the `key`, or `k` is very large).
    *   Converting the set to a list takes `O(M)` time.
    *   Sorting the final list takes `O(M log M)` time.
    *   **Overall Time Complexity:** `O(n * k + M log M)`. Since `M <= n`, this can be simplified to `O(n * k + n log n)`. In typical scenarios where `k` is not tiny, `O(n*k)` dominates.
*   **Space Complexity:**
    *   The `k_distant_indices_set` can store up to `n` unique indices.
    *   The `result` list also stores up to `n` indices.
    *   **Overall Space Complexity:** `O(n)` to store the unique indices.

---

### 5. Edge Cases & Correctness

The code handles several edge cases correctly:

*   **Empty `nums` list:** `n` would be 0. The loops won't execute, and an empty list `[]` will be returned after conversion and sorting, which is correct.
*   **`key` not found in `nums`:** `k_distant_indices_set` remains empty. An empty list `[]` will be returned, which is correct.
*   **`k = 0`:** Only `j` itself will be added to the set for each `key` occurrence, as `start = j` and `end = j`. Correct.
*   **`k` is large (e.g., `k >= n`):** For any `key` occurrence at `j`, the range `[max(0, j-k), min(n-1, j+k)]` will effectively cover `[0, n-1]`. All indices in `nums` will be added to the set. Correct.
*   **`key` at boundaries (index 0 or `n-1`):** The `max(0, ...)` and `min(n-1, ...)` logic correctly clamps the range to stay within valid list boundaries.
*   **Multiple occurrences of `key`:** The set automatically handles uniqueness, and the final sort ensures the correct order.

---

### 6. Improvements & Alternatives

*   **Performance Optimization (for large `k` or many `key` occurrences):**
    *   **Merge Overlapping Intervals:** Instead of individually adding `2k+1` elements for each `key` occurrence, one could first collect all k-distant *ranges* `[max(0, j-k), min(n-1, j+k)]`. Then, sort these ranges by their start points and merge overlapping or contiguous ranges into a minimal set of non-overlapping ranges. Finally, iterate through these merged ranges to collect all individual indices. This approach can reduce the time complexity from `O(n*k)` closer to `O(n + N_key_occurrences * log N_key_occurrences)` (where `N_key_occurrences` is the number of times `key` appears) for the merging step, plus `O(n)` for final index collection.
    *   **Boolean Array Flagging:** For very large `n`, if memory isn't an issue, initialize a boolean array `is_k_distant = [False] * n`. Iterate as in the current solution, but instead of adding to a set, set `is_k_distant[i] = True` for all `i` in the range `[start, end]`. Finally, iterate through `is_k_distant` to build the result list. This avoids set overheads and ensures sorted order naturally (if iterating `0` to `n-1`), eliminating the final `sort()`. This has `O(n*k)` time and `O(n)` space.
*   **Readability:** The current code is already quite readable and self-explanatory.

---

### 7. Security/Performance Notes

*   **Performance:** The `O(n * k)` time complexity is the primary performance bottleneck. If `n` and `k` are both large (e.g., `n = 10^5`, `k = 10^5`), the total operations could be `10^{10}`, which is too slow for typical competitive programming or real-world applications. For scenarios where `k` can be significant relative to `n`, the "Merge Overlapping Intervals" optimization would be essential to prevent quadratic time complexity.
*   **Security:** This code does not interact with external systems, handle sensitive data, or involve complex parsing. Therefore, there are no immediate security concerns. The code only performs computations based on its input parameters.

### Code:
```python
from typing import List

class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        n = len(nums)
        k_distant_indices_set = set()

        # Iterate through all possible indices j in nums
        for j in range(n):
            # If nums[j] is equal to the key
            if nums[j] == key:
                # Calculate the range of indices [start, end] that are k-distant from j
                # Ensure start is not less than 0 and end is not greater than n-1
                start = max(0, j - k)
                end = min(n - 1, j + k)

                # Add all indices within this calculated range to the set
                # Using a set automatically handles duplicates
                for i in range(start, end + 1):
                    k_distant_indices_set.add(i)
        
        # Convert the set of k-distant indices to a list
        result = list(k_distant_indices_set)
        
        # Sort the list in increasing order as required
        result.sort()
        
        return result
```
