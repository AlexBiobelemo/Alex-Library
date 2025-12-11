## Count Number of Pairs With Absolute Difference K
**Language:** python
**Tags:** python,oop,hashmap,frequency counting

### Description:
This code snippet provides an efficient solution for counting pairs `(i, j)` within a list of numbers `nums` such that `i < j` and the absolute difference `|nums[i] - nums[j]|` equals a given integer `k`.

---

### 1. Overview & Intent

*   **Goal**: Count all pairs of indices `(i, j)` in a list `nums` such that `i < j` and `|nums[i] - nums[j]| == k`.
*   **Approach**: The code uses a single pass through the list, maintaining a frequency map of numbers encountered *so far*. For each number, it checks if its potential partners (`num - k` and `num + k`) have been seen previously.

---

### 2. How It Works

1.  **Initialization**:
    *   `count`: An integer initialized to `0` to store the total number of valid pairs.
    *   `freq_map`: A `defaultdict(int)` to store the frequency of each number encountered as we iterate through `nums`. `defaultdict(int)` is convenient because accessing a non-existent key automatically returns `0`.

2.  **Iteration**:
    *   The code iterates through each `num` in the input list `nums`.

3.  **Finding Partners**:
    *   For the current `num`, it looks for two potential partners that would satisfy the `k` difference:
        *   `num - k`: If `num - k` exists in `freq_map`, it means we've seen this number before. Every occurrence of `num - k` forms a valid pair with the current `num` (where `num` is at index `j` and `num - k` was at some `i < j`). The count of these pairs is added from `freq_map[num - k]`.
        *   `num + k`: Similarly, if `num + k` exists in `freq_map`, every occurrence of `num + k` forms a valid pair with the current `num`. This accounts for cases where `nums[i] - nums[j] = -k`, which is `nums[j] - nums[i] = k`. The count of these pairs is added from `freq_map[num + k]`.

4.  **Update Frequency Map**:
    *   After checking for partners, the current `num` is added to the `freq_map` (or its count incremented). This makes it available as a potential partner for subsequent numbers in the list.

5.  **Return Value**:
    *   After processing all numbers, `count` holds the total number of pairs satisfying the condition, and is returned.

---

### 3. Key Design Decisions

*   **Data Structure**: `collections.defaultdict(int)` for `freq_map`.
    *   **Pros**: Provides average O(1) time complexity for insertions, lookups, and updates. The `defaultdict`'s default factory for `int` (which is `0`) simplifies code by not requiring explicit checks for key existence before accessing or incrementing counts.
    *   **Trade-offs**: Requires O(N) auxiliary space in the worst case (if all numbers are unique) to store the frequencies.

*   **Algorithm**: Single pass iteration with a frequency map.
    *   **Pros**: Achieves optimal O(N) time complexity, as each number is processed exactly once with constant-time dictionary operations. This is significantly faster than a brute-force O(N^2) nested loop approach.
    *   **Constraint Handling**: By checking `freq_map` for `num - k` and `num + k` *before* adding `num` to the map, the algorithm implicitly ensures that `i < j` (i.e., the partner `nums[i]` must have appeared *before* the current `nums[j]`).

---

### 4. Complexity

*   **Time Complexity**:
    *   The loop iterates `N` times, where `N` is the length of `nums`.
    *   Inside the loop, dictionary operations (`freq_map[key]`, `freq_map[num] += 1`) have an average time complexity of O(1).
    *   Therefore, the overall average time complexity is **O(N)**.

*   **Space Complexity**:
    *   The `freq_map` stores at most `U` unique numbers, where `U <= N`.
    *   In the worst case (all numbers in `nums` are unique), the `freq_map` will store `N` entries.
    *   Therefore, the overall space complexity is **O(N)**.

---

### 5. Edge Cases & Correctness

*   **Empty `nums`**: If `nums` is empty, the loop won't execute, `count` remains `0`, and `0` is returned. This is correct.
*   **`k > 0` (Assumption)**: For positive `k`, `num - k` and `num + k` are distinct values. The logic correctly adds `freq_map[num - k]` and `freq_map[num + k]` to `count`, effectively capturing both `nums[j] - nums[i] = k` and `nums[i] - nums[j] = k` (which is `nums[j] - nums[i] = -k`). This correctly counts pairs `(i, j)` where `i < j`.
    *   *Self-correction/Clarification*: Many "K-Difference" problems (e.g., LeetCode 2006) specify `k >= 1`. If `k` is strictly positive, the code is correct.
*   **`k = 0` (Potential Issue)**: If `k` *could* be `0`, the current code would double-count pairs where `nums[i] == nums[j]`. This is because `num - k` and `num + k` would both equal `num`. In such a scenario, `freq_map[num - k]` and `freq_map[num + k]` would both refer to `freq_map[num]`, leading to `2 * freq_map[num]` being added instead of `freq_map[num]`. However, given standard problem constraints where `k` is usually positive, this is often not an actual bug.
*   **Negative numbers in `nums`**: Python dictionaries (and `defaultdict`) handle negative keys seamlessly, so this works correctly.
*   **Duplicate numbers in `nums`**: The use of `freq_map` correctly accounts for duplicate numbers. If `nums = [1, 1, 3], k = 2`:
    *   `num = 1` (first): `freq_map = {1:1}`. `count=0`.
    *   `num = 1` (second): `count += freq_map[-1]` (0) + `freq_map[3]` (0). `freq_map = {1:2}`. `count=0`.
    *   `num = 3`: `count += freq_map[1]` (2) + `freq_map[5]` (0). `count = 2`. (`(nums[0], nums[2])` and `(nums[1], nums[2])`). `freq_map = {1:2, 3:1}`.
    *   Result: 2. This is correct as `(nums[0], nums[2])` and `(nums[1], nums[2])` are two distinct pairs satisfying the condition.

---

### 6. Improvements & Alternatives

*   **Handling `k = 0` (if allowed)**: If `k` can be zero, add a conditional check:
    ```python
    if k == 0:
        count += freq_map[num] # Only add once
    else:
        count += freq_map[num - k]
        count += freq_map[num + k]
    ```
    This ensures correct counting for `k=0` while maintaining efficiency for `k > 0`.
*   **Two-Pointer Approach (after sorting)**:
    *   Sort `nums` (O(N log N) time).
    *   Use two pointers to find pairs in O(N) time.
    *   **Pros**: Lower space complexity (O(1) if sorting in-place).
    *   **Cons**: Higher time complexity for sorting. More complex to handle duplicate numbers and count distinct `(i,j)` pairs. Generally less suitable for this exact problem definition which cares about `i < j` and values rather than just unique pairs of values.
*   **Readability**: The current code is very readable due to clear variable names and concise logic. The comments explaining `num - k` and `num + k` are helpful for understanding the core mechanism.

---

### 7. Security/Performance Notes

*   **Performance**: The solution is performant, achieving optimal O(N) time complexity. The overhead of Python dictionaries is generally acceptable for typical competitive programming constraints. For extremely large input arrays or memory-constrained environments, the O(N) space complexity might be a concern.
*   **Security**: There are no apparent security vulnerabilities in this code. It processes numerical data internally and does not interact with external systems or user-controlled inputs in a way that could lead to exploits like injection attacks.

### Code:
```python
from collections import defaultdict
from typing import List

class Solution:
    def countKDifference(self, nums: List[int], k: int) -> int:
        count = 0
        freq_map = defaultdict(int)

        for num in nums:
            # Check if num - k exists in the frequency map
            # If it does, it means we found a previous number 'x' (at index i < current j)
            # such that x = num - k, which implies |num - x| = k.
            count += freq_map[num - k]
            
            # Check if num + k exists in the frequency map
            # If it does, it means we found a previous number 'y' (at index i < current j)
            # such that y = num + k, which implies |num - y| = |-k| = k.
            count += freq_map[num + k]
            
            # Add the current number to the frequency map
            freq_map[num] += 1
            
        return count
```
