## Count Subarrays With Majority Element I
**Language:** python
**Tags:** python,oop,binary indexed tree,prefix sum

### Description:
This Python code efficiently counts the number of subarrays where a specific `target` value is the majority element. It cleverly transforms the problem into finding subarrays with a positive sum and then leverages a Fenwick Tree (BIT) for an optimal solution.

---

### 1. Overview & Intent

The primary goal of the `countMajoritySubarrays` function is to determine how many subarrays within a given list `nums` satisfy the condition that a specified `target` integer appears more times than all other integers combined within that subarray.

The core idea is to transform the original problem:
1.  Map elements equal to `target` to `1` and all other elements to `-1`.
2.  A subarray now has the `target` as a majority element if and only if the sum of its transformed elements is strictly greater than `0`. This is because `(count_of_target * 1) + (count_of_others * -1) > 0` simplifies to `count_of_target > count_of_others`.
3.  The problem then reduces to finding the number of subarrays with a positive sum in this transformed array.

---

### 2. How It Works

The solution uses the prefix sum technique combined with a Fenwick Tree (BIT) to efficiently count positive-sum subarrays.

1.  **Transformation:**
    *   The input `nums` list is converted into a binary list `b`. Each element `x` in `nums` becomes `1` if `x == target` and `-1` otherwise.

2.  **Prefix Sums & Goal:**
    *   The sum of a subarray `b[i...j]` can be expressed as `prefix_sum[j] - prefix_sum[i-1]`.
    *   We are looking for pairs `(i-1, j)` such that `prefix_sum[j] - prefix_sum[i-1] > 0`, or `prefix_sum[j] > prefix_sum[i-1]`.

3.  **Fenwick Tree (BIT) Application:**
    *   A Fenwick Tree (`bit`) is used to efficiently count how many *previous* prefix sums (i.e., `prefix_sum[i-1]`) are less than the `current_prefix_sum` (i.e., `prefix_sum[j]`).
    *   **Handling Negative Prefix Sums:** The prefix sums can range from `-n` (if all elements are `-1`) to `n` (if all elements are `1`). Since Fenwick Trees are typically 1-indexed and require non-negative indices, an `OFFSET` of `n + 1` is applied to map the range `[-n, n]` to `[1, 2n+1]`. `BIT_SIZE` is set to `2n + 2` to accommodate these shifted indices.
    *   **Initialization:** The `bit` array is initialized with all zeros. An initial `current_prefix_sum` of `0` (representing the prefix sum before the first element) is added to the BIT by calling `update(0 + OFFSET, 1)`.

4.  **Iteration and Counting:**
    *   The code iterates through each element `x` in the transformed list `b`.
    *   In each iteration:
        *   `current_prefix_sum` is updated by adding `x`.
        *   `count_less = query(current_prefix_sum + OFFSET - 1)`: This query asks the BIT for the count of all previously encountered prefix sums `P_prev` such that `P_prev + OFFSET <= (current_prefix_sum + OFFSET - 1)`. This is equivalent to `P_prev < current_prefix_sum`. Each such `P_prev` forms a valid majority subarray ending at the current position.
        *   `ans` is incremented by `count_less`.
        *   `update(current_prefix_sum + OFFSET, 1)`: The current prefix sum is added to the Fenwick Tree for future calculations.

5.  **Return:** Finally, `ans` holds the total count of majority subarrays.

---

### 3. Key Design Decisions

*   **Problem Transformation:** The most crucial decision is to transform the "majority element" problem into a "positive sum subarray" problem. This simplifies the logic significantly by converting element types into numerical values `1` and `-1`.
*   **Prefix Sums:** A standard technique for converting subarray sum queries into two-point lookups (`prefix_sum[j] - prefix_sum[i-1]`).
*   **Fenwick Tree (BIT):** Selected for its efficiency in handling cumulative frequency queries and updates in `O(log N)` time. This allows for an overall optimal solution.
*   **Offsetting for BIT:** Necessary to map potentially negative prefix sums to valid, positive 1-indexed positions in the Fenwick Tree.
*   **`0` Prefix Sum Initialization:** Adding `update(0 + OFFSET, 1)` at the start correctly accounts for subarrays that start from index `0` itself (i.e., `prefix_sum[j] - prefix_sum[-1]`, where `prefix_sum[-1]` is conventionally `0`).

---

### 4. Complexity

*   **Time Complexity:** `O(N log N)`
    *   Converting `nums` to `b`: `O(N)`.
    *   Initializing `bit` array: `O(N)`.
    *   Looping `N` times (for each element in `b`):
        *   Inside the loop, `update` and `query` operations on the Fenwick Tree each take `O(log BIT_SIZE)`.
        *   `BIT_SIZE` is proportional to `N` (`2N + 2`). So, these operations are `O(log N)`.
    *   Total: `O(N) + N * O(log N) = O(N log N)`.

*   **Space Complexity:** `O(N)`
    *   `b` array: `O(N)`.
    *   `bit` array (Fenwick Tree): `O(BIT_SIZE)`, which is `O(N)`.
    *   Total: `O(N)`.

---

### 5. Edge Cases & Correctness

*   **Empty `nums` list (`n == 0`):** The code correctly handles this by returning `0` immediately.
*   **All elements are `target`:** The `b` array will be all `1`s. `current_prefix_sum` will be `0, 1, 2, ..., N`. The `query` operation will correctly count all `N * (N + 1) / 2` possible subarrays, all of which will have a positive sum.
*   **No elements are `target`:** The `b` array will be all `-1`s. `current_prefix_sum` will be `0, -1, -2, ..., -N`. The `query(current_prefix_sum + OFFSET - 1)` will always return `0` because `current_prefix_sum` will never be greater than any previous prefix sum (they are all non-positive and decreasing). The `ans` will remain `0`, which is correct.
*   **Mixed positive and negative sums:** The logic `prefix_sum[j] > prefix_sum[i-1]` inherently and correctly identifies all subarrays that have a positive sum, regardless of the sequence of `1`s and `-1`s.

---

### 6. Improvements & Alternatives

*   **Readability:**
    *   Adding comments explaining the purpose of `OFFSET` and the mapping of prefix sums to BIT indices would enhance clarity.
    *   A docstring for the class and the method would be beneficial.
*   **Alternative Algorithms:**
    *   **Brute Force (O(N^2)):** Iterate all `N * (N+1) / 2` subarrays, calculate sum for each. This would be too slow for larger inputs.
    *   **Divide and Conquer (O(N log N)):** Similar to algorithms for counting inversions or maximum subarray sum, one could design a divide-and-conquer approach. This would likely be more complex to implement than the Fenwick Tree solution for this specific problem.
    *   **Merge Sort based approach (O(N log N)):** A common alternative to BIT for this type of problem where you need to count pairs `(i, j)` satisfying a condition (like `prefix_sum[j] > prefix_sum[i-1]`). You would sort the prefix sums and count pairs during the merge step.

---

### 7. Security/Performance Notes

*   **Performance:** The `O(N log N)` time complexity is optimal for problems involving counting pairs with certain sum/difference conditions over a range. It's efficient enough for typical competitive programming constraints (N up to 10^5 or 10^6).
*   **Integer Overflow:** Python's integers handle arbitrary precision, so there's no risk of overflow for `ans` or `current_prefix_sum`, even if `N` is very large (e.g., `ans` can be up to `N*(N+1)/2`, which for `N=10^5` is approx. `5 * 10^9`).
*   No specific security vulnerabilities are present in this algorithmic code.

### Code:
```python
from typing import List

class Solution:
    def countMajoritySubarrays(self, nums: List[int], target: int) -> int:
        n = len(nums)
        if n == 0:
            return 0

        b = [1 if x == target else -1 for x in nums]

        BIT_SIZE = 2 * n + 2
        OFFSET = n + 1
        
        bit = [0] * BIT_SIZE

        def update(idx, val):
            while idx < BIT_SIZE:
                bit[idx] += val
                idx += idx & (-idx)

        def query(idx):
            res = 0
            while idx > 0:
                res += bit[idx]
                idx -= idx & (-idx)
            return res

        ans = 0
        current_prefix_sum = 0

        update(current_prefix_sum + OFFSET, 1)

        for x in b:
            current_prefix_sum += x
            count_less = query(current_prefix_sum + OFFSET - 1)
            ans += count_less
            update(current_prefix_sum + OFFSET, 1)
            
        return ans
```
