## Coin Chnage
**Language:** python
**Tags:** python,dynamic programming,object-oriented,list

### Description:
This code snippet implements a classic dynamic programming solution to the "Coin Change" problem.

---

### 1. Overview & Intent

*   **Problem:** Given a list of coin denominations and a total amount, find the minimum number of coins required to make up that amount.
*   **Goal:** Return the minimum count, or -1 if the amount cannot be made using the given coins.
*   **Approach:** Uses a bottom-up dynamic programming approach.

---

### 2. How It Works

The algorithm builds up a solution from smaller subproblems to the final target amount.

*   **Initialization:**
    *   A `dp` array of size `amount + 1` is created.
    *   `dp[i]` will store the minimum number of coins needed to make the sum `i`.
    *   All entries are initialized to `float('inf')`, representing an unachievable state (or a very large number, meaning "not yet found a solution").
    *   `dp[0]` is set to `0`, as 0 coins are needed to make an amount of 0.
*   **Iteration through Amounts:**
    *   The code iterates through each possible `amount` from 1 up to the target `amount` (inclusive). Let's call this `i`.
*   **Iteration through Coins:**
    *   For each `i`, it then iterates through every `coin` in the given `coins` list.
*   **DP State Transition:**
    *   If `i - coin` is a non-negative value (meaning the current `coin` can be used to form `i` from a previously calculated smaller amount `i - coin`), it updates `dp[i]`.
    *   `dp[i]` is set to the minimum of its current value and `1 + dp[i - coin]`. This `1` represents using the current `coin`, and `dp[i - coin]` is the minimum coins needed for the remaining amount.
*   **Result:**
    *   Finally, `dp[amount]` holds the minimum coins for the target `amount`.
    *   If `dp[amount]` is still `float('inf')`, it means the target `amount` cannot be made, so -1 is returned. Otherwise, `dp[amount]` is returned.

---

### 3. Key Design Decisions

*   **Dynamic Programming (Bottom-Up):**
    *   **Optimal Substructure:** The optimal solution for an amount `i` depends on the optimal solutions for smaller amounts `i - coin`.
    *   **Overlapping Subproblems:** The same subproblems (e.g., `dp[5]`) are used multiple times in calculating larger amounts, making memoization (or tabulation in bottom-up DP) efficient.
*   **`dp` Array:**
    *   Serves as a memoization table to store the results of subproblems. This avoids redundant computations.
*   **Initialization with `float('inf')`:**
    *   Crucial for correctly finding the *minimum* number of coins. Any valid count will be less than `infinity`.
    *   Also acts as a sentinel value to detect if an amount is impossible to form.

---

### 4. Complexity

Let `N` be the `amount` and `M` be the number of `coins`.

*   **Time Complexity: O(N * M)**
    *   The outer loop runs `N` times (from 1 to `amount`).
    *   The inner loop runs `M` times (once for each `coin`).
    *   Operations inside the inner loop are constant time.
*   **Space Complexity: O(N)**
    *   A `dp` array of size `amount + 1` is created.

---

### 5. Edge Cases & Correctness

*   **`amount = 0`:**
    *   Correctly handled by `dp[0] = 0`. The loops run from `i=1`, so `dp[0]` is untouched and remains 0.
*   **`amount` cannot be made:**
    *   If `dp[amount]` remains `float('inf')` after all calculations, it correctly returns -1.
*   **`coins` list is empty:**
    *   If `coins` were empty (not typically allowed by problem constraints for `amount > 0`), the inner loop would never run. `dp[amount]` would remain `float('inf')` (for `amount > 0`), correctly returning -1.
*   **`coins` contains 1:**
    *   If a coin with value 1 is present, any non-negative amount can be formed, and the algorithm will find the solution.
*   **Coins larger than `amount`:**
    *   The condition `if i - coin >= 0:` correctly handles this, effectively ignoring coins larger than the current `i`.

---

### 6. Improvements & Alternatives

*   **Readability:**
    *   The variable names (`dp`, `coin`, `amount`, `i`) are clear and self-explanatory. The code is already quite readable.
*   **Performance (Minor):**
    *   The `coins` list could be sorted to potentially prune the inner loop early (e.g., `if coin > i: continue`), but the current `if i - coin >= 0:` check achieves the same effect. Sorting `coins` would not change the overall O(N*M) complexity.
*   **Input Validation:**
    *   For robustness in a production system, one might add checks for `amount < 0` or an empty `coins` list if these are not guaranteed by problem constraints.
*   **Alternative Approach (Top-Down / Memoized Recursion):**
    *   This problem can also be solved using a top-down recursive approach with memoization (e.g., using `functools.lru_cache` in Python). This often reads more naturally as "how many coins for `amount`?" is broken down into "how many for `amount - coin`?". Both approaches have the same time and space complexity.

---

### 7. Security/Performance Notes

*   **Security:** No direct security concerns or vulnerabilities in this algorithm.
*   **Performance (Scale):**
    *   For extremely large `amount` values, the `O(amount)` space complexity could lead to a `MemoryError`.
    *   Similarly, for very large `amount` and `len(coins)`, the `O(amount * len(coins))` time complexity could lead to a "Time Limit Exceeded" error in competitive programming or slow execution in production. This is an inherent limitation of this DP approach for such scales. However, for typical constraints (e.g., `amount` up to 10,000, `coins` up to 100), it performs well.

### Code:
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [float('inf')] * (amount + 1)
        dp[0] = 0

        for i in range(1, amount + 1):
            for coin in coins:
                if i - coin >= 0:
                    dp[i] = min(dp[i], 1 + dp[i - coin])
        
        return dp[amount] if dp[amount] != float('inf') else -1
```
