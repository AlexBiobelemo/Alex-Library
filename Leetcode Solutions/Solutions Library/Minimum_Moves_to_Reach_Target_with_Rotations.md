## Minimum Moves to Reach Target with Rotations
**Language:** python
**Tags:** python,bfs,queue,graph,grid

### Description:
This code implements a Breadth-First Search (BFS) algorithm to find the minimum number of moves required for a 2x1 snake to navigate a grid from a starting position to a target position.

---

### 1. Overview & Intent

*   **Problem:** Find the minimum number of moves to guide a 2-cell snake from `(0,0)` (horizontal) to `(n-1, n-2)` (horizontal) in an `n x n` grid.
*   **Constraints:**
    *   The snake occupies two adjacent cells.
    *   Cells marked `1` are obstacles; cells marked `0` are empty.
    *   The snake cannot move into or through obstacle cells.
    *   Possible moves: right, down, clockwise rotation, counter-clockwise rotation.
*   **Goal:** Return the minimum number of moves, or `-1` if the target is unreachable.
*   **Approach:** Uses Breadth-First Search (BFS) because it guarantees finding the shortest path in an unweighted graph (where each move has a cost of 1).

### 2. How It Works

1.  **State Representation:** Each possible configuration of the snake is a `state` defined by a tuple `(r, c, orientation)`:
    *   `(r, c)`: The row and column of the top-leftmost cell of the snake.
    *   `orientation`: `0` for horizontal (occupies `(r, c)` and `(r, c+1)`), `1` for vertical (occupies `(r, c)` and `(r+1, c)`).

2.  **Initialization:**
    *   `n`: The size of the grid.
    *   `start_state`: `(0, 0, 0)` (snake at `(0,0)` and `(0,1)`, horizontal).
    *   `target_r, target_c, target_orientation`: `(n-1, n-2, 0)` (snake at `(n-1, n-2)` and `(n-1, n-1)`, horizontal).
    *   `q`: A `deque` (double-ended queue) storing `(state, moves)` tuples. Initialized with `(start_state, 0)`.
    *   `visited`: A `set` to keep track of already explored states, preventing cycles and redundant computations. Initialized with `start_state`.

3.  **BFS Loop:**
    *   The `while q:` loop continues as long as there are states to explore.
    *   `q.popleft()`: Retrieves the current state `(r, c, orientation)` and the `moves` taken to reach it.
    *   **Target Check:** If the current state matches the `target_state`, the `moves` count is returned, as this is the shortest path found.

4.  **Exploring Possible Moves:** For the current state, the code checks four possible next moves:
    *   **Move Right:**
        *   **Horizontal:** Snake `(r,c), (r,c+1)` moves to `(r,c+1), (r,c+2)`. Requires `(r,c+2)` to be within bounds and empty.
        *   **Vertical:** Snake `(r,c), (r+1,c)` moves to `(r,c+1), (r+1,c+1)`. Requires `(r,c+1)` and `(r+1,c+1)` to be within bounds and empty.
    *   **Move Down:**
        *   **Horizontal:** Snake `(r,c), (r,c+1)` moves to `(r+1,c), (r+1,c+1)`. Requires `(r+1,c)` and `(r+1,c+1)` to be within bounds and empty.
        *   **Vertical:** Snake `(r,c), (r+1,c)` moves to `(r+1,c), (r+2,c)`. Requires `(r+2,c)` to be within bounds and empty.
    *   **Rotate Clockwise (Horizontal to Vertical):**
        *   Snake `(r,c), (r,c+1)` rotates to `(r,c), (r+1,c)`. Requires `(r+1,c)` and `(r+1,c+1)` to be within bounds and empty. This allows the snake to pivot at `(r,c)`.
    *   **Rotate Counter-Clockwise (Vertical to Horizontal):**
        *   Snake `(r,c), (r+1,c)` rotates to `(r,c), (r,c+1)`. Requires `(r,c+1)` and `(r+1,c+1)` to be within bounds and empty. This allows the snake to pivot at `(r,c)`.

5.  **State Management:** For each valid `next_state`:
    *   It checks if `next_state` has already been `visited`.
    *   If not, it's added to `visited` and pushed onto the `q` with an incremented `moves` count.

6.  **Unreachable Target:** If the `q` becomes empty and the target state was never reached, it means the target is unreachable, and `-1` is returned.

### 3. Key Design Decisions

*   **Breadth-First Search (BFS):** Chosen because the problem asks for the *minimum* number of moves. BFS explores states level by level, guaranteeing the first time the target is reached is via the shortest path in terms of moves.
*   **State Representation `(r, c, orientation)`:** This is crucial for defining a unique position and configuration of the snake.
    *   `r, c`: Represents the anchor point (top-left) of the snake.
    *   `orientation`: Distinguishes between horizontal and vertical positions using minimal memory.
*   **`collections.deque` for Queue:** `deque` provides `O(1)` time complexity for `append()` and `popleft()`, which are the primary operations for a BFS queue. A standard Python `list` would have `O(N)` for `pop(0)`.
*   **`set` for `visited`:** `set` provides `O(1)` average time complexity for adding elements and checking membership (`in` operator), which is essential for efficient state tracking in BFS.
*   **Grid Obstacle Representation (0/1):** `0` for empty cells and `1` for obstacles is standard and easy to work with for conditional checks.

### 4. Complexity

*   **`n`**: The dimension of the `n x n` grid.

*   **Time Complexity: O(N^2)**
    *   **Number of States:** The snake's top-left cell `(r, c)` can be at `N * N` positions. For each position, there are 2 orientations (horizontal/vertical). So, the total number of unique states is `N * N * 2`.
    *   **Operations per State:** For each state, we explore a constant number of possible moves (up to 4: right, down, rotate clockwise, rotate counter-clockwise). Each move check involves constant time operations (boundary checks, grid access, `set` lookup/insertion).
    *   Therefore, the total time complexity is roughly `(N * N * 2) * Constant`, which simplifies to `O(N^2)`.

*   **Space Complexity: O(N^2)**
    *   **`visited` set:** In the worst case, it can store all possible `N * N * 2` states.
    *   **`q` (deque):** In the worst case, the queue can hold a significant portion of the total states (e.g., states at the widest level of the BFS tree).
    *   Both dominate the space complexity, resulting in `O(N^2)`.

### 5. Edge Cases & Correctness

*   **Target Unreachable:** The BFS correctly handles this by returning `-1` if the queue becomes empty and the target state has not been found.
*   **Smallest Grid (n=2):**
    *   `start_state = (0, 0, 0)` (snake at `(0,0), (0,1)` horizontal).
    *   `target_state = (1, 0, 0)` (snake at `(1,0), (1,1)` horizontal).
    *   The `if r + 1 < n ...` and similar boundary checks correctly prevent out-of-bounds access even for small `n`. The logic for `c+2` or `r+2` ensures these checks are correct for the full extent of the snake's new position.
*   **Completely Blocked Paths:** If the `grid` is full of obstacles, the BFS will explore only a few initial states (or just the start state if `(0,0)` or `(0,1)` is blocked) and then correctly terminate, returning -1.
*   **Initial State Blocked:** If `grid[0][0]` or `grid[0][1]` is `1`, the initial state is invalid. The problem statement usually implies the initial state is always valid. If not, an initial check could be added (though the BFS would likely immediately fail to find any valid next moves anyway).
*   **Move Conditions Precision:** The conditions for each move (e.g., `grid[r+1][c] == 0 and grid[r+1][c+1] == 0` for horizontal move down) accurately reflect the space required by the snake to perform the action.

### 6. Improvements & Alternatives

*   **Readability:**
    *   **Constants for Orientation:** Define `HORIZONTAL = 0` and `VERTICAL = 1` at the top of the class for better clarity than magic numbers.
    *   **Helper Functions for Move Checks:** Extract the complex `if` conditions for valid moves into separate, well-named helper methods (e.g., `_can_move_right(r, c, orientation, grid, n)`). This would make the main BFS loop much cleaner and easier to read.
    *   **Clearer Variable Names:** While `r, c` are common, more descriptive names like `current_row`, `current_col`, `next_row`, `next_col` could enhance readability, especially for complex conditional statements.
*   **Minor Performance (Optimization for Sparse Obstacles):**
    *   For very sparse grids where the target might be very far, an A* search with a well-chosen heuristic (e.g., Manhattan distance of the snake's "head" to the target, combined with orientation difference penalties) *could* potentially prune the search space faster than plain BFS. However, for typical grid sizes (N <= 100), BFS is usually sufficient and simpler to implement correctly. Given the graph's structure (grid traversal), BFS is generally efficient enough.
*   **No Major Algorithmic Alternatives:** For finding the *shortest path* in an unweighted graph, BFS is the optimal algorithm. Other graph traversal algorithms like Dijkstra's or A* would be overkill or require more complex setup for this unweighted scenario.

### 7. Security/Performance Notes

*   **Security:** This algorithm has no inherent security implications. It's a pure computational problem.
*   **Performance:**
    *   The current implementation is well-optimized for typical competitive programming constraints. The use of `deque` and `set` for BFS operations ensures efficient `O(1)` average time for additions, removals, and lookups.
    *   The `O(N^2)` complexity is acceptable for grid sizes up to a few hundred (`N=200` would mean `40,000` states, roughly `160,000` operations, still very fast). Beyond that, `N^2` can become slow. However, competitive programming problems involving `N x N` grids usually cap `N` at values where `N^2` or `N^3` solutions are feasible.

### Code:
```python
from collections import deque

class Solution:
    def minimumMoves(self, grid: List[List[int]]) -> int:
        n = len(grid)
        
        # State: (r, c, orientation)
        # (r, c) is the top-leftmost cell of the snake.
        # orientation: 0 for horizontal (occupies (r,c) and (r,c+1))
        #              1 for vertical (occupies (r,c) and (r+1,c))
        
        # Initial state: (0, 0, 0) - snake at (0,0) and (0,1), horizontal
        start_state = (0, 0, 0)
        
        # Target state: (n-1, n-2, 0) - snake at (n-1, n-2) and (n-1, n-1), horizontal
        target_r, target_c, target_orientation = n - 1, n - 2, 0
        
        q = deque([(start_state, 0)]) # ( (r, c, orientation), moves )
        visited = {start_state}
        
        while q:
            (r, c, orientation), moves = q.popleft()
            
            # Check if target reached
            if (r, c, orientation) == (target_r, target_c, target_orientation):
                return moves
            
            # --- Possible moves ---
            
            # 1. Move right
            if orientation == 0: # Horizontal: (r, c) and (r, c+1)
                # New state: (r, c+1, 0)
                # Requires (r, c+2) to be valid and empty
                if c + 2 < n and grid[r][c+2] == 0:
                    next_state = (r, c + 1, 0)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
            else: # Vertical: (r, c) and (r+1, c)
                # New state: (r, c+1, 1)
                # Requires (r, c+1) and (r+1, c+1) to be valid and empty
                if c + 1 < n and grid[r][c+1] == 0 and grid[r+1][c+1] == 0:
                    next_state = (r, c + 1, 1)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
                        
            # 2. Move down
            if orientation == 0: # Horizontal: (r, c) and (r, c+1)
                # New state: (r+1, c, 0)
                # Requires (r+1, c) and (r+1, c+1) to be valid and empty
                if r + 1 < n and grid[r+1][c] == 0 and grid[r+1][c+1] == 0:
                    next_state = (r + 1, c, 0)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
            else: # Vertical: (r, c) and (r+1, c)
                # New state: (r+1, c, 1)
                # Requires (r+2, c) to be valid and empty
                if r + 2 < n and grid[r+2][c] == 0:
                    next_state = (r + 1, c, 1)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
            
            # 3. Rotate clockwise (Horizontal to Vertical)
            # From (r, c, 0) (occupies (r,c) and (r,c+1)) to (r, c, 1) (occupies (r,c) and (r+1,c))
            # Requires (r+1, c) and (r+1, c+1) to be valid and empty
            if orientation == 0:
                # Check bounds for (r+1, c) and (r+1, c+1)
                if r + 1 < n and c + 1 < n and \
                   grid[r+1][c] == 0 and grid[r+1][c+1] == 0:
                    next_state = (r, c, 1)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
                        
            # 4. Rotate counter-clockwise (Vertical to Horizontal)
            # From (r, c, 1) (occupies (r,c) and (r+1,c)) to (r, c, 0) (occupies (r,c) and (r,c+1))
            # Requires (r, c+1) and (r+1, c+1) to be valid and empty
            if orientation == 1:
                # Check bounds for (r, c+1) and (r+1, c+1)
                if r + 1 < n and c + 1 < n and \
                   grid[r][c+1] == 0 and grid[r+1][c+1] == 0:
                    next_state = (r, c, 0)
                    if next_state not in visited:
                        visited.add(next_state)
                        q.append((next_state, moves + 1))
                        
        return -1 # Target not reachable
```
