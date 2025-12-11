## Find the Maximum Factor Score of Array
**Language:** python
**Tags:** python,object-oriented programming,prefix sum,number theory

### Description:
This code calculates the maximum possible "score" from an array of integers, where the score for an array is defined as the product of its Greatest Common Divisor (GCD) and Least Common Multiple (LCM). The algorithm considers two scenarios: using the original array, or using the array after removing exactly one element.

## 1. Overview & Intent

*   **Goal:** Find the maximum score, `GCD(subarray) * LCM(subarray)`, where the `subarray` is either the original `nums` array or `nums` with one element removed.
*   **Input:** A list of integers `nums`.
*   **Output:** An integer representing the maximum score found.
*   **Core Idea:** Leverage prefix and suffix arrays to efficiently compute GCD and LCM for subarrays created by removing a single element.

## 2. How It Works

The solution proceeds in several logical steps:

1.  **Helper Functions:**
    *   `gcd(a, b)`: A wrapper around `math.gcd` to compute the greatest common divisor.
    *   `lcm(a, b)`: Computes the least common multiple using the formula `(a * b) // gcd(a, b)`, with a special case for `0` inputs to return `0`.

2.  **Base Cases:**
    *   If `nums` is empty (`n == 0`), returns `0`.
    *   If `nums` has one element (`n == 1`), returns `nums[0] * nums[0]` (as GCD and LCM of a single element `X` are both `X`).

3.  **Prefix & Suffix Computations:**
    *   **`prefix_gcd` / `prefix_lcm`:** Two arrays are created to store the cumulative GCD and LCM from the left (index `0` up to `i`). `prefix_gcd[i]` holds `gcd(nums[0], ..., nums[i])`, and similarly for LCM.
    *   **`suffix_gcd` / `suffix_lcm`:** Two arrays are created to store the cumulative GCD and LCM from the right (index `n-1` down to `i`). `suffix_gcd[i]` holds `gcd(nums[i], ..., nums[n-1])`, and similarly for LCM.
    *   These arrays are populated in `O(N)` time.

4.  **Calculate Maximum Score:**
    *   `max_factor_score` is initialized to `0`.
    *   **Case 1: No element removed.**
        *   The score for the entire array is `prefix_gcd[n-1] * prefix_lcm[n-1]`. `max_factor_score` is updated with this value.
    *   **Case 2: Remove one element.**
        *   The code iterates through each possible index `i` (from `0` to `n-1`), simulating the removal of `nums[i]`.
        *   **If `i == 0` (removing the first element):** The remaining array is `nums[1:]`. Its GCD and LCM are directly available from `suffix_gcd[1]` and `suffix_lcm[1]`.
        *   **If `i == n - 1` (removing the last element):** The remaining array is `nums[:n-1]`. Its GCD and LCM are directly available from `prefix_gcd[n-2]` and `prefix_lcm[n-2]`.
        *   **If `i` is in the middle:** The remaining array effectively splits into `nums[:i]` and `nums[i+1:]`.
            *   The GCD of the combined array is `gcd(prefix_gcd[i-1], suffix_gcd[i+1])`.
            *   The LCM of the combined array is `lcm(prefix_lcm[i-1], suffix_lcm[i+1])`.
        *   For each scenario, the `current_gcd_val * current_lcm_val` is calculated and used to update `max_factor_score` if it's higher.

5.  **Return:** The final `max_factor_score`.

## 3. Key Design Decisions

*   **Prefix/Suffix Arrays:** This is the most critical design choice.
    *   **Benefit:** Enables `O(1)` (or `O(log V)` considering `gcd`/`lcm` complexity) calculation of GCD/LCM for subarrays after removing an element. Without them, each removal would require re-calculating GCD/LCM over `O(N)` elements, leading to an overall `O(N^2 log V)` time complexity.
    *   **Trade-off:** Requires `O(N)` additional space for four auxiliary arrays.
*   **`lcm` Implementation:** Uses the mathematical property `lcm(a, b) = (a * b) // gcd(a, b)`. The explicit check for `a == 0 or b == 0` to return `0` is important for consistency, as `lcm(X, 0)` is generally `0`.
*   **Iterative Maximum:** The `max_factor_score` is updated iteratively, ensuring the global maximum is captured.

## 4. Complexity

*   **Time Complexity:**
    *   `gcd(a, b)` and `lcm(a, b)` operations take `O(log(min(a, b)))` time. Let `V` be the maximum value in `nums`. These operations take approximately `O(log V)`.
    *   Populating `prefix_gcd`, `prefix_lcm`, `suffix_gcd`, `suffix_lcm` arrays: `4 * (N-1)` operations, each involving a `gcd`/`lcm` call. This is `O(N log V)`.
    *   Iterating through `N` elements to simulate removals: `N` iterations, each involving a few `gcd`/`lcm` calls (constant number of calls for each iteration). This is `O(N log V)`.
    *   **Total Time Complexity: O(N log V)**, where `N` is the length of `nums` and `V` is the maximum value in `nums`.

*   **Space Complexity:**
    *   `nums`: `O(N)` (input array).
    *   `prefix_gcd`, `prefix_lcm`, `suffix_gcd`, `suffix_lcm`: Each of these takes `O(N)` space.
    *   **Total Space Complexity: O(N)**.

## 5. Edge Cases & Correctness

*   **`n = 0` (empty array):** Correctly returns `0`.
*   **`n = 1` (single element array):** Correctly returns `nums[0] * nums[0]`.
*   **Arrays containing `0`:**
    *   `math.gcd(X, 0)` returns `X`.
    *   The custom `lcm(a, b)` returns `0` if either `a` or `b` is `0`. This means if `0` is part of the final subarray, its LCM (and thus the score) will be `0`. This behavior is generally acceptable for problems involving GCD/LCM, implying a score of `0` if a `0` is present.
    *   If `0` is removed, the score can be non-zero from the remaining elements. This is correctly handled.
*   **Large Numbers:** Python's arbitrary-precision integers automatically handle large values, so overflow of `a * b` in `lcm` is not a concern for correctness, although very large numbers could impact performance.
*   **All elements are identical (e.g., `[5, 5, 5]`):** GCD will be `5`, LCM will be `5`. Score `25`. Correctly handled.
*   **Negative Numbers:** The problem typically implies positive integers for GCD/LCM contexts. `math.gcd` works with negative numbers (returns positive GCD). The `lcm` formula with `a*b` would behave based on Python's multiplication of negatives. Assuming `nums` contains non-negative integers as is typical for these problems.

## 6. Improvements & Alternatives

*   **`gcd` Helper Redundancy:** The `gcd` helper function is a direct wrapper around `math.gcd`. It could be removed, and `math.gcd` called directly for slightly cleaner code.
*   **`lcm` function (Python 3.9+):** For Python versions 3.9 and above, `math.lcm` is available. Using it would simplify the `lcm` implementation and potentially be more optimized.
*   **Clarity on `lcm(0, X)`:** The current `lcm` implementation returns `0` if any input is `0`. This is a common convention, but it's worth noting explicitly if the problem statement suggests a different handling of `0` in LCM calculations (e.g., `lcm(0, X)` being `X`).
*   **Early Exit for Zeros (Potential):** If the problem guarantees `nums` contains only positive integers, the `lcm` function's `if a == 0 or b == 0: return 0` check could be removed. If the problem implies that having *any* zero in the array results in a total score of 0 (and removing it is the only way to get a non-zero score), one could potentially check for `0` in `nums` at the start and handle that case more specifically. However, the current approach is robust.

## 7. Security/Performance Notes

*   **Security:** This code is purely computational and does not interact with external systems, user input in a way that could be exploited, or sensitive data. Thus, security considerations are not applicable.
*   **Performance:**
    *   The `O(N log V)` time complexity is efficient for typical constraints (e.g., `N` up to `10^5`, `V` up to `10^9` or `10^{18}`).
    *   While Python's arbitrary-precision integers prevent overflow, calculations involving extremely large numbers (many digits, which can happen with `lcm`s of large primes) can be slower than fixed-width integer operations in languages like C++ or Java. However, for typical competitive programming constraints, this is usually acceptable.

### Code:
```python
import math

class Solution:
    def maxScore(self, nums: List[int]) -> int:
        n = len(nums)

        def gcd(a, b):
            return math.gcd(a, b)

        def lcm(a, b):
            if a == 0 or b == 0:
                return 0
            return (a * b) // gcd(a, b)

        if n == 0:
            return 0
        if n == 1:
            return nums[0] * nums[0]

        prefix_gcd = [0] * n
        prefix_lcm = [0] * n

        prefix_gcd[0] = nums[0]
        prefix_lcm[0] = nums[0]
        for i in range(1, n):
            prefix_gcd[i] = gcd(prefix_gcd[i-1], nums[i])
            prefix_lcm[i] = lcm(prefix_lcm[i-1], nums[i])

        suffix_gcd = [0] * n
        suffix_lcm = [0] * n

        suffix_gcd[n-1] = nums[n-1]
        suffix_lcm[n-1] = nums[n-1]
        for i in range(n - 2, -1, -1):
            suffix_gcd[i] = gcd(suffix_gcd[i+1], nums[i])
            suffix_lcm[i] = lcm(suffix_lcm[i+1], nums[i])

        max_factor_score = 0

        # Case 1: No element removed (original array)
        max_factor_score = prefix_gcd[n-1] * prefix_lcm[n-1]

        # Case 2: Remove one element
        for i in range(n):
            current_gcd_val = 0
            current_lcm_val = 0

            if i == 0: # Removing the first element, array is nums[1:]
                current_gcd_val = suffix_gcd[1]
                current_lcm_val = suffix_lcm[1]
            elif i == n - 1: # Removing the last element, array is nums[:n-1]
                current_gcd_val = prefix_gcd[n-2]
                current_lcm_val = prefix_lcm[n-2]
            else: # Removing an element in the middle
                current_gcd_val = gcd(prefix_gcd[i-1], suffix_gcd[i+1])
                current_lcm_val = lcm(prefix_lcm[i-1], suffix_lcm[i+1])

            max_factor_score = max(max_factor_score, current_gcd_val * current_lcm_val)

        return max_factor_score
```
