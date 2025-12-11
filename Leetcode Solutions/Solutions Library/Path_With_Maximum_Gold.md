## Path With Maximum Gold
**Language:** python
**Tags:** python,oop,dfs,backtracking,set

### Description:
This code finds the maximum amount of gold that can be collected from a grid, starting from any cell with gold. It uses a Depth-First Search (DFS) with backtracking to explore all possible paths.

---

### 1. Overview & Intent

*   **Problem**: Given a grid where each cell `(i, j)` contains an integer representing the amount of gold, find the maximum amount of gold you can collect.
*   **Rules**:
    *   You can start at any cell that has gold (`> 0`).
    *   You can move one step in any of the four cardinal directions (up, down, left, right).
    *   You cannot visit the same cell more than once *in the same path*.
    *   You cannot visit cells with 0 gold.
*   **Goal**: Return the maximum total gold collected from any valid path.

---

### 2. How It Works

The solution employs a Depth-First Search (DFS) algorithm with backtracking.

1.  **Initialization**:
    *   `m, n`: Dimensions of the grid.
    *   `self.max_gold`: A global variable (within the class instance) initialized to 0, which will store the maximum gold found across all paths.

2.  **`dfs(r, c, current_gold, visited)` Function**:
    *   This recursive helper function explores paths starting from `(r, c)`.
    *   `current_gold`: The total gold collected *up to the current cell* in the active path.
    *   `visited`: A `set` to keep track of cells already visited *in the current path* to prevent cycles.
    *   **Update Max**: It immediately updates `self.max_gold` with `max(self.max_gold, current_gold)`. This ensures that even short paths update the global maximum.
    *   **Mark Visited**: The current cell `(r, c)` is added to the `visited` set.
    *   **Explore Neighbors**: It iterates through the four possible directions (up, down, left, right). For each neighbor `(nr, nc)`:
        *   **Boundary Check**: Ensures `(nr, nc)` is within the grid boundaries.
        *   **Gold Check**: Ensures `grid[nr][nc]` contains gold (`> 0`).
        *   **Visited Check**: Ensures `(nr, nc)` has not been visited in the current path.
        *   If all conditions are met, it recursively calls `dfs(nr, nc, current_gold + grid[nr][nc], visited)`.
    *   **Backtracking**: After exploring all paths originating from the current cell, `(r, c)` is removed from the `visited` set. This is crucial as it allows other independent paths (starting from different initial cells) to visit this cell.

3.  **Main Loop**:
    *   The code iterates through every cell `(r, c)` in the grid.
    *   If a cell `grid[r][c]` contains gold (`> 0`), it initiates a new DFS traversal from that cell.
    *   Crucially, for each new top-level DFS call, a **new empty `visited` set** (`set()`) is passed. This ensures that each starting cell explores paths independently without prior `visited` states interfering.

4.  **Result**: Finally, `self.max_gold` (the maximum value updated during all DFS traversals) is returned.

---

### 3. Key Design Decisions

*   **Depth-First Search (DFS)**: Chosen because the problem requires exploring all possible paths from various starting points to find the maximum. DFS naturally lends itself to exploring one path fully before backtracking.
*   **Backtracking with `visited` Set**:
    *   The `visited` set is essential to ensure that each cell is collected at most once within a *single* path.
    *   The backtracking step (`visited.remove((r, c))`) is vital. It "resets" the visited state for the current cell once all paths stemming from it have been explored. This allows the same physical cell to be part of *multiple different, independent paths* starting from other initial cells.
*   **Start DFS from Every Gold Cell**: Since there's no single designated starting point, the algorithm must try every cell containing gold as a potential starting point for a maximum gold path.
*   **Global `self.max_gold`**: Using a class member variable to store the maximum gold simplifies updating the global maximum from anywhere within the recursive calls.

---

### 4. Complexity

*   **Time Complexity**: $O(M \times N \times 4^{M \times N})$
    *   The outer loops iterate $M \times N$ times, starting a DFS from each gold cell.
    *   For each DFS, it explores all possible simple paths (paths without repeated vertices). In a grid, the number of simple paths can be exponential.
    *   A path can have a maximum length of $M \times N$ cells. At each step, there are up to 4 directions to explore. This leads to an exponential worst-case complexity, roughly $O(k^V)$ where $V = M \times N$ is the number of vertices (cells) and $k=4$ is the maximum degree (number of neighbors).
    *   The $M \times N$ factor comes from the initial iteration over all possible starting cells.
*   **Space Complexity**: $O(M \times N)$
    *   **Recursion Stack**: In the worst case, a path can visit all $M \times N$ cells, leading to a recursion depth of $M \times N$.
    *   **`visited` Set**: The `visited` set stores the coordinates of cells in the current path. Its maximum size is also $M \times N$.

---

### 5. Edge Cases & Correctness

*   **Empty Grid**: The code implicitly assumes `grid` is non-empty (`len(grid)` and `len(grid[0])` would raise `IndexError`). Standard problem constraints usually guarantee non-empty grids.
*   **Grid with All Zeros**: `self.max_gold` will remain 0, which is correct as no gold can be collected.
*   **Single Gold Cell**: The DFS will start from this cell, `self.max_gold` will be updated to its value, and then the function returns. Correct.
*   **Scattered Gold (no path possible)**: Each gold cell will be processed individually. `self.max_gold` will correctly reflect the value of the largest single gold cell.
*   **Large Gold Values**: Python integers handle arbitrary precision, so `current_gold` will not overflow regardless of the sum.
*   **Correctness of `visited` and Backtracking**: The use of a `set()` for each top-level DFS ensures independence between paths starting from different cells. The `visited.remove()` ensures that a cell can be part of distinct paths if those paths originate from different starting points.

---

### 6. Improvements & Alternatives

*   **Performance (Bitmask for `visited`)**: If `M * N` is small enough (e.g., up to 64 cells), a bitmask could represent the `visited` state instead of a `set` of tuples. This could offer marginal speedup due to potentially faster bitwise operations than hash table lookups, but the fundamental exponential complexity remains. For Python, `set` is generally efficient enough.
*   **Readability/Encapsulation**:
    *   Instead of `self.max_gold`, the `dfs` function could return the maximum gold found *from that specific branch*, and the main loop would then aggregate these maximums. However, the current approach of updating a class member is also common and acceptable for such problems.
    *   Passing `grid` as an explicit argument to `dfs` could make the function more self-contained, though accessing it from the enclosing scope is perfectly valid Python.
*   **Dynamic Programming/Memoization**: For problems where the "visited" state is simpler (e.g., only `(r, c)` matters), DP/memoization could optimize repeated subproblems. However, here, the `visited` set is part of the state, making it `(r, c, frozenset(visited))`. Storing and retrieving memoized results for such complex states is generally too costly in space and time to offer a significant improvement over simple backtracking DFS for these constraints.

---

### 7. Security/Performance Notes

*   **Recursion Limit**: Python has a default recursion limit (e.g., 1000 or 3000). For a grid of `15x15 = 225` cells, the maximum recursion depth is 225, which is well within Python's default limits. For significantly larger grids, this limit might need to be increased (`sys.setrecursionlimit()`), but that usually indicates the need for an iterative DFS implementation or a different algorithm.
*   **Python `set` Performance**: Python's `set` is implemented as a hash table, providing average $O(1)$ time complexity for adding, removing, and checking membership. This is efficient for managing the `visited` cells.
*   **Mutability of `visited`**: Passing the `visited` set by reference and modifying it efficiently avoids costly set copying during recursive calls, which would significantly increase the space and time complexity. The backtracking mechanism correctly manages its state.

### Code:
```python
class Solution:
    def getMaximumGold(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        self.max_gold = 0

        def dfs(r, c, current_gold, visited):
            # Update the global maximum gold found so far
            self.max_gold = max(self.max_gold, current_gold)

            # Mark the current cell as visited for this path
            visited.add((r, c))

            # Explore all four possible directions
            for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                nr, nc = r + dr, c + dc

                # Check boundary conditions, ensure cell has gold, and has not been visited in the current path
                if 0 <= nr < m and 0 <= nc < n and grid[nr][nc] > 0 and (nr, nc) not in visited:
                    dfs(nr, nc, current_gold + grid[nr][nc], visited)

            # Backtrack: Remove the current cell from the visited set
            # This allows other paths (starting from different cells) to potentially visit this cell
            visited.remove((r, c))

        # Iterate through every cell in the grid
        for r in range(m):
            for c in range(n):
                # If a cell contains gold, it can be a starting point for a path
                if grid[r][c] > 0:
                    # Start a new DFS path from this cell
                    # A new 'visited' set is created for each top-level DFS call to ensure independent paths
                    dfs(r, c, grid[r][c], set())

        return self.max_gold
```
