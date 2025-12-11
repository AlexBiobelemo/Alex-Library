## Stone Game III
**Language:** python
**Tags:** python,dynamic programming,game theory,minimax

### Description:
This code implements a dynamic programming solution to determine the winner of a variation of the Stone Game.

### 1. Overview & Intent

*   **Problem:** Alice and Bob play a game with a row of stones, each having a value. They take turns, with Alice starting. In each turn, a player can take 1, 2, or 3 stones from the *beginning* of the row. The goal is to maximize one's own total stone value.
*   **Objective:** Determine if Alice wins, Bob wins, or it's a tie, assuming both play optimally. The winner is the one with the higher total score.
*   **Approach:** The code uses dynamic programming to calculate the maximum possible score difference (Alice's score - Bob's score) that the *current player* can achieve from any given point in the game, assuming optimal play from both sides.

### 2. How It Works

1.  **Dynamic Programming State:**
    *   `dp[i]` represents the maximum score *difference* (Alice's total score - Bob's total score) that the player whose turn it is can achieve, starting from `stoneValue[i]` to the end of the row.
    *   Since Alice wants to maximize this difference and Bob wants to minimize it (from Alice's perspective, or maximize his own difference), `dp[i]` represents the maximum net gain for the current player.

2.  **Base Case:**
    *   `dp` is initialized with `n + 1` zeros. `dp[n]` naturally becomes `0`, meaning if there are no stones left, the score difference is 0.

3.  **Iteration (Bottom-Up):**
    *   The loop iterates backward from `i = n - 1` down to `0`. This ensures that `dp` values for future states (`dp[i+1]`, `dp[i+2]`, `dp[i+3]`) are already computed when calculating `dp[i]`.
    *   For each `i`, the current player (Alice in the first turn, or the player whose turn it is) considers three options:

        *   **Take 1 stone:**
            *   The player gets `stoneValue[i]`.
            *   The remaining game starts from `i + 1`. The value `dp[i+1]` represents the maximum score difference *the next player* (Bob) can achieve from `i+1`. From the *current player's* (Alice's) perspective, this next player's optimal play will *reduce* Alice's net difference.
            *   So, the difference for taking 1 stone is `stoneValue[i] - dp[i + 1]`.

        *   **Take 2 stones (if available):**
            *   The player gets `stoneValue[i] + stoneValue[i + 1]`.
            *   The remaining game starts from `i + 2`.
            *   The difference for taking 2 stones is `(stoneValue[i] + stoneValue[i + 1]) - dp[i + 2]`.

        *   **Take 3 stones (if available):**
            *   The player gets `stoneValue[i] + stoneValue[i + 1] + stoneValue[i + 2]`.
            *   The remaining game starts from `i + 3`.
            *   The difference for taking 3 stones is `(stoneValue[i] + stoneValue[i + 1] + stoneValue[i + 2]) - dp[i + 3]`.

    *   `dp[i]` is set to the maximum of these three possible differences, representing the optimal choice for the current player.

4.  **Final Result:**
    *   After the loop, `dp[0]` contains the maximum score difference Alice can achieve from the very beginning of the game.
    *   If `dp[0] > 0`, Alice wins.
    *   If `dp[0] < 0`, Bob wins.
    *   If `dp[0] == 0`, it's a tie.

### 3. Key Design Decisions

*   **Dynamic Programming:** Essential for solving two-player games with optimal play, due to overlapping subproblems (the same subgame state can be reached via different previous moves) and optimal substructure (an optimal solution for the whole game depends on optimal solutions for subgames).
*   **Bottom-Up Approach:** Iterating backward ensures that all required future `dp` states (`dp[i+k]`) are already computed before `dp[i]` is calculated.
*   **Score Difference State:** Defining `dp[i]` as the *difference* (current player's score - opponent's score) is a standard and effective technique for minimax problems, simplifying the recurrence relation to `current_gain - dp[next_state]`.
*   **Boundary Handling:** The `(dp[i + k] if i + k <= n else 0)` expression correctly handles cases where `i + k` goes beyond `n` (no stones left), effectively adding `0` to the opponent's score.

### 4. Complexity

*   **Time Complexity:** O(N)
    *   The main loop runs `n` times.
    *   Inside the loop, a constant number of operations (up to 3 stone-taking options, each involving arithmetic and array access) are performed.
    *   Therefore, the total time complexity is linear with respect to the number of stones.
*   **Space Complexity:** O(N)
    *   An array `dp` of size `n + 1` is used to store the dynamic programming states.
    *   Therefore, the total space complexity is linear with respect to the number of stones.

### 5. Edge Cases & Correctness

*   **`n = 1` (single stone):**
    *   The loop runs for `i = 0`.
    *   Only "Take 1 stone" option is valid. `current_stones_sum = stoneValue[0]`.
    *   `diff_if_take_1 = stoneValue[0] - dp[1]` (which is `0`). So `dp[0] = stoneValue[0]`.
    *   This is correct: Alice takes the only stone, her score is `stoneValue[0]`, Bob's is `0`.
*   **`n = 0` (empty `stoneValue` list):**
    *   `n` is 0. `dp` is `[0]`. The `for` loop `range(n - 1, -1, -1)` (i.e., `range(-1, -1, -1)`) does not execute.
    *   `dp[0]` remains `0`. The code correctly returns "Tie".
*   **All positive/negative stone values:** The logic correctly applies, as `max` will always select the best possible outcome for the current player regardless of value signs.
*   The use of `current_max_diff = -float('inf')` ensures that the `max` operation correctly captures the best option, even if all options result in negative differences.

### 6. Improvements & Alternatives

*   **Readability (Inner Loop):** The repetitive calculation of `current_stones_sum` and the three distinct `if` blocks for taking 1, 2, or 3 stones could be refactored into a more concise loop:
    ```python
    current_max_diff = -float('inf')
    current_stones_sum = 0
    for k in range(1, 4): # k represents number of stones to take
        if i + k - 1 < n: # Check if k stones are available
            current_stones_sum += stoneValue[i + k - 1] # Add the k-th stone value
            # Calculate difference: current sum - optimal difference for next player
            diff_if_take_k = current_stones_sum - (dp[i + k] if i + k <= n else 0)
            current_max_diff = max(current_max_diff, diff_if_take_k)
        else:
            break # No more stones to take for this 'k' or higher 'k'
    dp[i] = current_max_diff
    ```
    This makes the intent clearer and reduces repetition.
*   **Space Optimization (O(1)):** Since `dp[i]` only depends on `dp[i+1]`, `dp[i+2]`, and `dp[i+3]`, the space complexity can be reduced to O(1). We only need to store the last three calculated `dp` values and one for the current calculation. For typical competitive programming constraints (N up to 50,000), O(N) space is usually acceptable, but O(1) is technically possible.

### 7. Security/Performance Notes

*   **Performance:** The O(N) time complexity is optimal for this problem, as each stone value must be considered at least once. Python's list operations are generally efficient.
*   **Security:** There are no inherent security vulnerabilities in this pure algorithmic code. It doesn't handle external input or sensitive data in a way that would introduce risks.

### Code:
```python
class Solution:
    def stoneGameIII(self, stoneValue: List[int]) -> str:
        n = len(stoneValue)
        
        dp = [0] * (n + 1)
        
        for i in range(n - 1, -1, -1):
            current_max_diff = -float('inf')
            current_stones_sum = 0
            
            # Option 1: Take 1 stone
            current_stones_sum += stoneValue[i]
            diff_if_take_1 = current_stones_sum - (dp[i + 1] if i + 1 <= n else 0)
            current_max_diff = max(current_max_diff, diff_if_take_1)
            
            # Option 2: Take 2 stones
            if i + 1 < n:
                current_stones_sum += stoneValue[i + 1]
                diff_if_take_2 = current_stones_sum - (dp[i + 2] if i + 2 <= n else 0)
                current_max_diff = max(current_max_diff, diff_if_take_2)
            
            # Option 3: Take 3 stones
            if i + 2 < n:
                current_stones_sum += stoneValue[i + 2]
                diff_if_take_3 = current_stones_sum - (dp[i + 3] if i + 3 <= n else 0)
                current_max_diff = max(current_max_diff, diff_if_take_3)
            
            dp[i] = current_max_diff
            
        if dp[0] > 0:
            return "Alice"
        elif dp[0] < 0:
            return "Bob"
        else:
            return "Tie"
```
