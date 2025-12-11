## Maximum and Minimum Sums of at Most Size K Subsequences
**Language:** python
**Tags:** python,sorting,dynamic programming,combinatorics,modular arithmetic

### Description:
This code calculates the sum of `(minimum element + maximum element)` for all possible non-empty subsequences of a given array `nums` whose length is at most `k`, all modulo `10^9 + 7`.

## 1. Overview & Intent

The primary goal of this code is to solve a combinatorial sum problem efficiently. Given an array `nums` and an integer `k`, it computes the sum of `(min_val + max_val)` for every subsequence of `nums` that has a length between 1 and `k` (inclusive). All calculations are performed modulo `10^9 + 7` to prevent integer overflow.

## 2. How It Works

1.  **Sorting**: The input array `nums` is first sorted in ascending order. This is a critical step as it simplifies the process of identifying minimum and maximum elements within subsequences.
2.  **Modular Arithmetic Utilities**:
    *   A `MOD` constant (`10^9 + 7`) is defined.
    *   Factorials (`fact`) and their modular inverses (`invFact`) up to `n` (length of `nums`) are precomputed. This allows for `O(1)` calculation of binomial coefficients (nCr) modulo `MOD`. Fermat's Little Theorem is used for modular inverse.
    *   A helper function `nCr_mod_p(n_val, r_val)` calculates `C(n_val, r_val) % MOD`.
3.  **Precomputing Subsequence Counts (Dynamic Programming)**:
    *   An array `sum_combinations_upto_k_minus_1` is created. `sum_combinations_upto_k_minus_1[m]` stores the sum `sum_{p=0}^{k-1} C(m, p) % MOD`. This represents the total number of ways to choose *at most* `k-1` elements from `m` distinct items.
    *   This array is populated using a recurrence relation: `S(m+1, k-1) = (2 * S(m, k-1) - C(m, k-1)) % MOD`, where `S(m, k-1)` is the sum `sum_{p=0}^{k-1} C(m, p)`. The base case `S(0, k-1)` is 1 (representing `C(0,0)`).
4.  **Calculating Total Sum**:
    *   The code iterates through each element `nums[j]` in the sorted array.
    *   **Contribution as Minimum**: For `nums[j]` to be the minimum in a subsequence, all other `p` elements chosen for that subsequence must come from `nums[j+1:]` (elements greater than or equal to `nums[j]`). There are `n - 1 - j` such elements available. The number of ways to choose `p` elements such that the total subsequence length `(1 + p)` is at most `k` (i.e., `p <= k-1`) is `sum_{p=0}^{k-1} C(n-1-j, p)`. This value is directly retrieved from `sum_combinations_upto_k_minus_1[n - 1 - j]`.
    *   **Contribution as Maximum**: Similarly, for `nums[j]` to be the maximum, the `p` other elements must come from `nums[0:j]` (elements smaller than or equal to `nums[j]`). There are `j` such elements available. The number of ways to choose `p` elements such that `p <= k-1` is `sum_{p=0}^{k-1} C(j, p)`. This value is retrieved from `sum_combinations_upto_k_minus_1[j]`.
    *   The current element `nums[j]` contributes `nums[j] * (count_min_j + count_max_j)` to the total sum. This is because `nums[j]` is added to the sum once for every subsequence where it acts as the minimum, and once for every subsequence where it acts as the maximum.
    *   All contributions are accumulated into `total_sum` modulo `MOD`.

## 3. Key Design Decisions

*   **Sorting `nums`**: This is fundamental. It allows us to efficiently count subsequences where an element `nums[j]` is guaranteed to be the minimum or maximum by only considering elements to its right or left, respectively. Without sorting, this counting would be far more complex.
*   **Precomputation of Factorials and Inverse Factorials**: This is a standard optimization for combinatorial problems where `nCr` needs to be computed many times modulo a prime number. It reduces `nCr` calculation from `O(N)` (naively) to `O(1)` after an `O(N)` setup.
*   **Dynamic Programming for `sum_combinations_upto_k_minus_1`**: Instead of recalculating `sum_{p=0}^{k-1} C(m,p)` multiple times in the main loop, this array precomputes all necessary sums using an efficient `O(N)` recurrence relation. This avoids redundant computations and keeps the main loop `O(N)`. The recurrence `S(m+1, k-1) = (2 * S(m, k-1) - C(m, k-1))` is a clever way to derive the next sum from the previous one.
*   **Iterating by Element Contribution**: Instead of trying to generate all `k`-length subsequences and summing `(min+max)`, the code cleverly calculates how many times each `nums[j]` serves as a minimum and how many times it serves as a maximum. This approach is much more efficient as it processes each `nums[j]` once.

## 4. Complexity

*   **Time Complexity**:
    *   `nums.sort()`: `O(N log N)`
    *   Factorial and Inverse Factorial precomputation: `O(N)` (for loop) + `O(log MOD)` (for `pow` modular inverse). Dominated by `O(N)`.
    *   `sum_combinations_upto_k_minus_1` array precomputation: `O(N)` (for loop, each `nCr_mod_p` is `O(1)` after precomputation).
    *   Main loop to calculate `total_sum`: `O(N)` (for loop, each step is `O(1)` lookups).
    *   **Total Time Complexity: `O(N log N)`**, dominated by the initial sort.

*   **Space Complexity**:
    *   `fact` array: `O(N)`
    *   `invFact` array: `O(N)`
    *   `sum_combinations_upto_k_minus_1` array: `O(N)`
    *   **Total Space Complexity: `O(N)`**.

## 5. Edge Cases & Correctness

*   **`k = 0`**: The code explicitly handles this by returning 0, which is correct as no subsequences are allowed.
*   **`k = 1`**: Subsequences of length 1 are just the individual elements.
    *   `sum_combinations_upto_k_minus_1[m]` becomes `C(m, 0) = 1` for all `m >= 0`.
    *   For each `nums[j]`: `count_min_j` will be `1` (it's the only element from `nums[j:]` in a 1-length subsequence), and `count_max_j` will be `1` (similarly for `nums[:j]`).
    *   `total_sum` becomes `sum(nums[j] * (1 + 1)) = sum(2 * nums[j])`. This is correct, as for a single-element subsequence `[x]`, `min=x`, `max=x`, so `min+max = 2x`.
*   **`n = 0` (empty `nums`)**: `len(nums)` is 0. All loops will not run, `total_sum` remains 0. Correct.
*   **`n = 1` (single element `nums = [x]`)**:
    *   `sum_combinations_upto_k_minus_1[0]` is initialized to 1.
    *   The loop for `m_val` (from `0` to `n-2`) won't run.
    *   For `j=0`, `nums[0]=x`. `count_min_j = sum_combinations_upto_k_minus_1[0] = 1`. `count_max_j = sum_combinations_upto_k_minus_1[0] = 1`.
    *   `total_sum = 2x % MOD`. Correct for `[x]` subsequence.
*   **`k > n`**: The logic gracefully handles this. `C(N, R)` correctly returns 0 if `R > N`. The recurrence for `sum_combinations_upto_k_minus_1` will also behave correctly, effectively summing `C(m,p)` only for `p <= m`.
*   **Modular Arithmetic**: All additions, multiplications, and subtractions (with `+ MOD`) are correctly applied modulo `MOD`, preventing overflow and ensuring correct results in the finite field.

## 6. Improvements & Alternatives

*   **Docstrings**: Add comprehensive docstrings to the `Solution` class and `minMaxSums` method to clearly explain their purpose, arguments, and return value. This significantly improves maintainability and understanding.
*   **Comments**: While existing comments are good, more detailed explanations for the recurrence relation `S(m+1, k-1) = ...` or the precise interpretation of `count_min_j` and `count_max_j` could further enhance clarity for a reader less familiar with combinatorial identities.
*   **Class Structure**: For competitive programming, wrapping in a `Solution` class is standard. In a larger application, the modular arithmetic utilities (`nCr_mod_p`, factorial precomputation) could be extracted into a separate utility class or module if they are used elsewhere.
*   **Optimization for `k` being very small or very large**:
    *   If `k=1`, the logic simplifies significantly to just `sum(2 * nums[j])`.
    *   If `k >= n`, then any subsequence (of length 1 to `n`) is valid, and `sum_{p=0}^{k-1} C(m,p)` effectively becomes `sum_{p=0}^m C(m,p) = 2^m`. This optimization is already implicitly handled by the `nCr_mod_p` returning 0 for `r > n`.

## 7. Security/Performance Notes

*   **Modular Arithmetic Robustness**: The implementation correctly uses `pow(base, exp, mod)` for modular exponentiation, which is secure and efficient. The `(val + MOD) % MOD` pattern correctly handles potential negative results from Python's `%` operator with negative numbers, ensuring results are always positive.
*   **Memory Usage**: For very large `N` (e.g., `N > 10^6`), the `O(N)` space for `fact`, `invFact`, and `sum_combinations_upto_k_minus_1` could become a concern, potentially leading to a Memory Limit Exceeded (MLE) error in environments with tight memory constraints. However, for typical competitive programming limits (`N` up to `2*10^5`), `O(N)` space is usually acceptable.
*   **Large Integer Values**: Python handles arbitrary-precision integers, so `nums[j]` values themselves or intermediate products `nums[j] * count` will not overflow Python's internal integer types before the `% MOD` operation, which is a robustness advantage over languages like C++ or Java where one must explicitly use `long long`.

### Code:
```python
from typing import List

class Solution:
    def minMaxSums(self, nums: List[int], k: int) -> int:
        n = len(nums)
        MOD = 10**9 + 7

        nums.sort()

        # Precompute factorials and inverse factorials modulo MOD
        # fact[i] stores i! % MOD
        # invFact[i] stores (i!)^(-1) % MOD
        fact = [1] * (n + 1)
        invFact = [1] * (n + 1)
        for i in range(1, n + 1):
            fact[i] = (fact[i-1] * i) % MOD
        
        # Modular inverse for fact[n] using Fermat's Little Theorem: a^(MOD-2) % MOD
        invFact[n] = pow(fact[n], MOD - 2, MOD)
        # Compute inverse factorials for smaller numbers
        for i in range(n - 1, -1, -1):
            invFact[i] = (invFact[i+1] * (i+1)) % MOD

        # Helper function to calculate nCr % MOD
        def nCr_mod_p(n_val, r_val):
            if r_val < 0 or r_val > n_val:
                return 0
            return fact[n_val] * invFact[r_val] % MOD * invFact[n_val - r_val] % MOD

        # sum_combinations_upto_k_minus_1[m] will store the sum:
        # S(m, k-1) = sum_{p=0}^{k-1} C(m, p)
        # This represents the number of ways to choose at most k-1 elements from m items.
        # We use the recurrence relation: S(m+1, k-1) = (2 * S(m, k-1) - C(m, k-1)) % MOD
        sum_combinations_upto_k_minus_1 = [0] * n

        # Base case for m=0: S(0, k-1) = sum_{p=0}^{k-1} C(0, p)
        # Since k is a positive integer, k >= 1, so k-1 >= 0.
        # C(0,0) = 1, and C(0, p) = 0 for p > 0.
        # Thus, S(0, k-1) = 1.
        if k >= 1:
            sum_combinations_upto_k_minus_1[0] = 1
        else: # k=0, no subsequences are allowed, so the sum is 0.
            return 0

        # Populate the sum_combinations_upto_k_minus_1 array using the recurrence
        for m_val in range(n - 1):
            # The term C(m_val, k-1) needs to be subtracted
            val_to_subtract = nCr_mod_p(m_val, k - 1)
            sum_combinations_upto_k_minus_1[m_val + 1] = (2 * sum_combinations_upto_k_minus_1[m_val] - val_to_subtract + MOD) % MOD
        
        total_sum = 0
        # Iterate through each element nums[j] to calculate its contribution
        for j in range(n):
            # Contribution when nums[j] is the minimum element:
            # We fix nums[j] as the minimum. The other elements must be chosen from nums[j+1:]
            # There are (n - 1 - j) elements available after nums[j].
            # If we choose 'p' elements from these, the subsequence length is 1 + p.
            # We need 1 + p <= k, which means p <= k-1.
            # The number of ways to choose 'p' elements from (n-1-j) is C(n-1-j, p).
            # So, the total number of subsequences where nums[j] is minimum is sum_{p=0}^{k-1} C(n-1-j, p).
            # This value is stored in sum_combinations_upto_k_minus_1[n - 1 - j].
            count_min_j = sum_combinations_upto_k_minus_1[n - 1 - j]

            # Contribution when nums[j] is the maximum element:
            # We fix nums[j] as the maximum. The other elements must be chosen from nums[0:j]
            # There are 'j' elements available before nums[j].
            # If we choose 'p' elements from these, the subsequence length is 1 + p.
            # We need 1 + p <= k, which means p <= k-1.
            # The number of ways to choose 'p' elements from 'j' is C(j, p).
            # So, the total number of subsequences where nums[j] is maximum is sum_{p=0}^{k-1} C(j, p).
            # This value is stored in sum_combinations_upto_k_minus_1[j].
            count_max_j = sum_combinations_upto_k_minus_1[j]

            # Add nums[j] * (count_min_j + count_max_j) to the total sum
            total_sum = (total_sum + nums[j] * (count_min_j + count_max_j)) % MOD
        
        return total_sum
```
