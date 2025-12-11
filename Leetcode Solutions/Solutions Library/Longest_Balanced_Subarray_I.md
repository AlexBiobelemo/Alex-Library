## Longest Balanced Subarray I
**Language:** python
**Tags:** python,oop,set,brute force,subarray

### Description:
### 1. Overview & Intent

This code defines a class `Solution` with a method `longestBalanced`.
*   **Intent:** The primary goal of `longestBalanced` is to find the length of the *longest sub-array* within a given list of integers (`nums`) such that the number of *distinct even integers* in that sub-array is equal to the number of *distinct odd integers*.
*   **Input:** A list of integers (`nums`).
*   **Output:** An integer representing the maximum length found, or `0` if no such balanced sub-array exists (other than an empty one).

### 2. How It Works

The function employs a brute-force approach to check all possible sub-arrays:

*   **Outer Loop (`i`):** Iterates through each possible starting index of a sub-array, from `0` to `n-1`.
*   **Inner Loop (`j`):** For each starting index `i`, this loop extends the current sub-array to the right, iterating through all possible ending indices `j`, from `i` to `n-1`.
*   **Sub-array Processing:**
    *   For each sub-array `nums[i:j+1]`, it maintains two `set` objects: `distinct_evens` and `distinct_odds`.
    *   As `j` extends the sub-array, the current number `nums[j]` is added to the appropriate set based on its parity (even or odd).
    *   **Balance Check:** After adding `nums[j]`, it compares the lengths of the two sets (`len(distinct_evens) == len(distinct_odds)`).
    *   **Max Length Update:** If the lengths are equal, it means the current sub-array `nums[i:j+1]` is "balanced." The length of this sub-array (`j - i + 1`) is then compared with `max_len`, and `max_len` is updated if the current sub-array is longer.
*   **Return Value:** After checking all possible sub-arrays, the accumulated `max_len` is returned.

### 3. Key Design Decisions

*   **Data Structures:**
    *   **`set` for `distinct_evens` and `distinct_odds`**: This is a crucial choice. Sets inherently store only unique elements. This allows for efficient tracking of *distinct* numbers, as `add()` operations are O(1) on average, and `len()` is O(1).
*   **Algorithm:**
    *   **Brute-force nested loops**: The solution iterates through all `n * (n+1) / 2` possible sub-arrays. This is simple to understand and implement, ensuring all combinations are checked.
    *   **Trade-off**: While correct, this approach can be inefficient for larger inputs, as discussed in the complexity section.

### 4. Complexity

*   **Time Complexity: O(n^2)**
    *   The outer loop runs `n` times.
    *   The inner loop runs up to `n` times for each outer loop iteration (on average `n/2` times).
    *   Inside the inner loop, set operations (`add`, `len`) take O(1) time on average.
    *   Therefore, the total time complexity is roughly `n * n * O(1) = O(n^2)`.
*   **Space Complexity: O(n)**
    *   In the worst case, the `distinct_evens` and `distinct_odds` sets could store up to `n` distinct numbers (e.g., if all numbers in the input array are unique).
    *   These sets are re-initialized for each `i`, but for a given `i`, they grow with `j` up to `O(n)`.

### 5. Edge Cases & Correctness

The code handles several edge cases correctly:

*   **Empty `nums` (`n=0`):** The loops won't execute, and `max_len` (initialized to 0) will be returned. Correct, as there are no sub-arrays.
*   **`nums` with a single element (`n=1`):** `max_len` will remain 0. A single element cannot form a balanced sub-array where distinct evens and odds are equal (unless both are 0, which isn't the case for a non-empty sub-array). Correct.
*   **All numbers are even or all numbers are odd:** In this case, one of the sets will always be empty (or have length 0 after initial element), while the other will grow. `len(distinct_evens)` will never equal `len(distinct_odds)` (unless both are 0). `max_len` will remain 0. Correct.
*   **`nums` where all elements are the same (e.g., `[1, 1, 1]`):** `distinct_odds` will have length 1, `distinct_evens` will have length 0. `max_len` will remain 0. Correct.
*   **`nums = [1, 2]`:**
    *   `i=0, j=0`: `[1]`, evens=0, odds=1. No.
    *   `i=0, j=1`: `[1, 2]`, evens=1, odds=1. Yes! `max_len = 2`.
    *   Returns 2. Correct.

The logic holds for these scenarios, making the solution robust for varied inputs.

### 6. Improvements & Alternatives

*   **Performance (for larger N):**
    *   For the constraints where `N` is large (e.g., N > 2000), an `O(N^2)` solution would typically exceed time limits.
    *   While problems involving "distinct elements" can be tricky to optimize beyond `O(N^2)` for arbitrary sub-arrays, this specific problem (equality of *counts* of distinct elements) is particularly challenging for common `O(N)` techniques like sliding window or prefix sums because the "distinct" property is dynamic and non-local.
    *   **Advanced Approach (Difficult):** A more advanced solution would likely require a sophisticated data structure (like a segment tree or Fenwick tree) combined with coordinate compression or a similar technique to handle the distinct counts efficiently for all sub-arrays in less than `O(N^2)` time. Such an approach would be significantly more complex than the current solution and is typically reserved for highly constrained competitive programming problems.
    *   **Conclusion on Improvement:** Given the problem definition, the `O(N^2)` brute-force with sets is the most straightforward and often intended solution for moderate `N` values. Significant performance improvements (to, say, `O(N log N)` or `O(N)`) are not trivial and would require a different algorithmic paradigm.

*   **Readability:** The code is already highly readable, with clear variable names and a straightforward flow. No major improvements needed here.

### 7. Security/Performance Notes

*   **Performance Bottleneck:** The primary concern is the `O(N^2)` time complexity. If `nums` contains `N` elements, and `N` is large (e.g., `N = 10^5`), the number of operations (`10^{10}`) will be too high for typical execution time limits (usually around `10^8` operations per second). This code would likely result in a "Time Limit Exceeded" (TLE) error in such scenarios.
*   **No Security Issues:** This code processes numerical data and does not interact with external systems, files, or user input in a way that would introduce common security vulnerabilities (e.g., injection, unauthorized access).

### Code:
```python
import collections

class Solution:
    def longestBalanced(self, nums: List[int]) -> int:
        max_len = 0
        n = len(nums)

        for i in range(n):
            distinct_evens = set()
            distinct_odds = set()
            for j in range(i, n):
                num = nums[j]
                if num % 2 == 0:
                    distinct_evens.add(num)
                else:
                    distinct_odds.add(num)
                
                if len(distinct_evens) == len(distinct_odds):
                    max_len = max(max_len, j - i + 1)
        
        return max_len
```
