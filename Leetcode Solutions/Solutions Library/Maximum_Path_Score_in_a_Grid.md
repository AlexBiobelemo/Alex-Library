## Maximum Path Score in a Grid
**Language:** python
**Tags:** python,dynamic programming,grid traversal,oop

### Description:
This code solves a classic dynamic programming problem: finding the maximum score path in a grid under a total cost constraint.

---

### 1. Overview & Intent

This Python code aims to find the maximum possible "score" achievable by traversing a grid from the top-left cell `(0, 0)` to the bottom-right cell `(m-1, n-1)`. The movement is restricted to only going down or right.

Crucially, each cell in the grid has an associated "score" and "cost" based on its value (0, 1, or 2). There's a maximum total "cost" `k` allowed for any valid path.

The function `maxPathScore` returns the highest score found among all valid paths, or -1 if no path satisfies the cost constraint.

---

### 2. How It Works

The solution uses a 3-dimensional dynamic programming (DP) table:

*   **`dp[r][c][cost]`**: Represents the maximum score achievable to reach cell `(r, c)` with an *exact* total path cost of `cost`.
*   **Initialization**: The `dp` table is initialized with -1, signifying that these states (reaching a cell with a specific cost) are initially unreachable.
*   **Cell Mappings**: Two dictionaries, `cell_scores` and `cell_costs`, define how grid values (0, 1, 2) translate into their respective scores and costs.
*   **Base Case**: The starting cell `(0, 0)` is processed first. Its score and cost are calculated. If the initial cost is within the `k` limit, `dp[0][0][initial_cost]` is updated with the `initial_score`.
*   **Iteration**: The code then iterates through the grid cells row by row, then column by column:
    *   For each cell `(r, c)` and for each possible `current_path_cost` up to `k`:
        *   If `dp[r][c][current_path_cost]` is reachable (not -1), it means we've found a way to reach `(r, c)` with that cost and score.
        *   From this state, the algorithm attempts to move to the adjacent cells: `(r + 1, c)` (down) and `(r, c + 1)` (right).
        *   For each potential move, it calculates the `new_total_cost` and `new_total_score`.
        *   If `new_total_cost` does not exceed `k`, it updates the corresponding `dp` state for the next cell (`dp[r+1][c][new_total_cost]` or `dp[r][c+1][new_total_cost]`). The `max` function ensures we always store the highest score for a given cell and cost.
*   **Final Result**: After filling the entire DP table, the maximum score is found by iterating through all possible costs `0` to `k` at the destination cell `(m-1, n-1)` and taking the maximum value. If `max_final_score` remains -1, it means the destination was unreachable within the cost limit.

---

### 3. Key Design Decisions

*   **Dynamic Programming State (`dp[r][c][cost]`)**: This is the core decision. The problem's constraints (path from A to B, with a resource limit `k` affecting path choices) strongly suggest DP. Including `cost` in the state is essential to track the budget and ensure no path exceeds `k`.
*   **Initialization with -1**: Using -1 (or `float('-inf')`) as an indicator for unreachable states is critical when scores can be non-negative. This allows `max()` comparisons to correctly propagate reachable scores and ignore unreachable paths.
*   **Dictionaries for `cell_scores` and `cell_costs`**:
    *   **Pros**: Clear, extensible. If new cell types (e.g., value 3) were introduced, they could be added easily without changing the core logic.
    *   **Cons**: Slightly more overhead than direct array lookups, but negligible for 3 fixed values.
*   **Iterative DP Traversal**: The chosen iteration order (`r` -> `c` -> `current_path_cost`) ensures that when computing `dp[r][c]`, all necessary `dp` values from `(r-1, c)` and `(r, c-1)` (and potentially earlier costs for `(r,c)` itself, though not strictly needed here) are already computed. This bottom-up approach is standard for grid DP.

---

### 4. Complexity

*   **Time Complexity**: `O(m * n * k)`
    *   The initialization of the `dp` table takes `O(m * n * k)` time.
    *   The main nested loops iterate `m` times (rows), `n` times (columns), and `k + 1` times (for costs). Inside the innermost loop, operations are constant time.
    *   The final loop to find the maximum score takes `O(k)` time.
    *   Therefore, the dominant factor is `O(m * n * k)`.
*   **Space Complexity**: `O(m * n * k)`
    *   The `dp` table is the primary consumer of memory, storing `m * n * (k + 1)` integer values.
    *   The `cell_scores` and `cell_costs` dictionaries take `O(1)` space (fixed size).
    *   Thus, the total space complexity is `O(m * n * k)`.

---

### 5. Edge Cases & Correctness

*   **1x1 Grid**:
    *   `m=1, n=1`. The base case `dp[0][0][initial_cost]` is correctly set.
    *   The main loops `for r in range(m)` and `for c in range(n)` run for `r=0, c=0`. The inner conditions `r + 1 < m` and `c + 1 < n` will be false, so no updates to other cells occur.
    *   The final loop correctly accesses `dp[0][0]` to find the `max_final_score`. Correct.
*   **`k = 0` (Zero Cost Limit)**:
    *   If `grid[0][0]` has `cell_costs[grid[0][0]] > 0`, then `initial_cost <= k` (i.e., `initial_cost <= 0`) will be false, and `dp[0][0][0]` will remain -1. The final result will be -1, which is correct as no path is possible.
    *   If `grid[0][0]` has `cell_costs[grid[0][0]] == 0`, `dp[0][0][0]` is set. Subsequent cells with cost 0 can be reached. Correctly handles paths with only 0-cost cells.
*   **No Path to Destination within Cost `k`**:
    *   If all possible paths to `(m-1, n-1)` exceed `k`, or if `(m-1, n-1)` is simply unreachable, all entries `dp[m-1][n-1][cost]` will remain -1.
    *   The final `max_final_score` will correctly remain -1.
*   **Grid contains values other than 0, 1, 2**: The current code assumes grid values are 0, 1, or 2 based on `cell_scores` and `cell_costs`. If other values are possible, it would lead to a `KeyError`. This is an input validation concern, not a logical correctness issue within the defined scope.

---

### 6. Improvements & Alternatives

*   **Space Optimization**:
    *   The `dp` state for `(r, c)` only depends on `(r-1, c)` and `(r, c-1)`. This means we don't need to store the entire `m` rows.
    *   We can optimize the space complexity from `O(m * n * k)` to `O(n * k)` by only keeping track of the current row and the previous row's DP states.
    *   Further optimization to `O(k)` is possible if processing is done carefully, effectively using `dp[c][cost]` to represent the current column and updating it based on `dp[c-1][cost]` (for left neighbor) and the previous row's `dp[c][cost]` (for upper neighbor). However, this would complicate the transition logic significantly.
    *   For the current problem constraints (`m, n <= 100, k <= 1000`), `100 * 100 * 1000 = 10^7` DP states are manageable in terms of memory for typical systems, so space optimization might not be strictly necessary but is a good practice.
*   **Readability of Cell Mappings**: While the dictionaries are clear, for very simple mappings like this, one could also use two small arrays (lists) `scores = [0, 1, 2]` and `costs = [0, 1, 1]` indexed directly by `grid[r][c]` value. This would be slightly faster if the grid values are always within a small, contiguous range.
*   **Early Exit**: If `k` is very small and the initial cell `(0,0)` itself costs more than `k`, we could return -1 immediately. The current code handles this by not updating `dp[0][0][initial_cost]`.

---

### 7. Security/Performance Notes

*   **Performance**: As noted in complexity, `O(m * n * k)` can become slow for extremely large inputs. For typical competitive programming limits like `m, n ~ 100` and `k ~ 1000`, it results in around `10^7` operations, which is generally acceptable (within a few seconds). However, if `m, n, k` were `1000` each, it would be `10^9` operations and `10^9` memory cells, which would be too slow and too much memory.
*   **No Security Concerns**: The code operates purely on numerical inputs and does not involve external interactions, file I/O, network communication, or user-supplied executable code, thus presenting no direct security vulnerabilities.

### Code:
```python
import collections
from typing import List

class Solution:
    def maxPathScore(self, grid: List[List[int]], k: int) -> int:
        m = len(grid)
        n = len(grid[0])

        # dp[r][c][cost] stores the maximum score to reach (r, c) with a total path cost of 'cost'.
        # Initialize with -1 to indicate unreachable states.
        dp = [[[-1] * (k + 1) for _ in range(n)] for _ in range(m)]

        cell_scores = {0: 0, 1: 1, 2: 2}
        cell_costs = {0: 0, 1: 1, 2: 1}

        # Base case: starting cell (0, 0)
        initial_score = cell_scores[grid[0][0]]
        initial_cost = cell_costs[grid[0][0]]

        if initial_cost <= k:
            dp[0][0][initial_cost] = initial_score

        # Iterate through the grid cells
        for r in range(m):
            for c in range(n):
                for current_path_cost in range(k + 1):
                    if dp[r][c][current_path_cost] != -1:  # If current state is reachable
                        current_path_score = dp[r][c][current_path_cost]

                        # Try moving down to (r + 1, c)
                        if r + 1 < m:
                            next_cell_val = grid[r + 1][c]
                            cost_to_add = cell_costs[next_cell_val]
                            score_to_add = cell_scores[next_cell_val]
                            new_total_cost = current_path_cost + cost_to_add

                            if new_total_cost <= k:
                                dp[r + 1][c][new_total_cost] = max(
                                    dp[r + 1][c][new_total_cost],
                                    current_path_score + score_to_add
                                )

                        # Try moving right to (r, c + 1)
                        if c + 1 < n:
                            next_cell_val = grid[r][c + 1]
                            cost_to_add = cell_costs[next_cell_val]
                            score_to_add = cell_scores[next_cell_val]
                            new_total_cost = current_path_cost + cost_to_add

                            if new_total_cost <= k:
                                dp[r][c + 1][new_total_cost] = max(
                                    dp[r][c + 1][new_total_cost],
                                    current_path_score + score_to_add
                                )

        # After filling the DP table, find the maximum score at the bottom-right corner
        max_final_score = -1
        for cost in range(k + 1):
            max_final_score = max(max_final_score, dp[m - 1][n - 1][cost])

        return max_final_score
```
