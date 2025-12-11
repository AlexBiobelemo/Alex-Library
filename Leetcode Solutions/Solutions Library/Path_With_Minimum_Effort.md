## Path With Minimum Effort
**Language:** python
**Tags:** python,oop,dijkstra,graph algorithm,heap

### Description:
This problem is a classic graph traversal challenge, often framed as a variation of Dijkstra's algorithm.

---

### 1. Overview & Intent

This code aims to find a path from the top-left cell `(0, 0)` to the bottom-right cell `(rows-1, cols-1)` in a 2D grid of `heights`. The "effort" for a path is defined as the *maximum absolute difference in heights* between any two adjacent cells along that path. The goal is to find the path that minimizes this maximum effort.

**In essence**: It's a shortest path problem where the cost of an edge isn't added to a sum, but rather determines a *maximum* value that needs to be minimized over the entire path.

---

### 2. How It Works

The solution employs a modified Dijkstra's algorithm (or uniform cost search) using a priority queue.

*   **Initialization**:
    *   A `dist` 2D array is created, initialized with `float('inf')` for all cells, except `dist[0][0]` which is set to `0` (no effort to be at the start). This `dist[r][c]` stores the minimum *maximum effort* required to reach cell `(r, c)` from `(0, 0)`.
    *   A `heapq` (min-priority queue) is initialized with the starting cell: `(0, 0, 0)`, representing `(current_max_effort, row, col)`.
*   **Traversal Loop**:
    *   The algorithm repeatedly extracts the cell `(current_effort, r, c)` with the *smallest `current_effort`* from the priority queue.
    *   **Pruning**: If the `current_effort` extracted from the heap is already greater than `dist[r][c]`, it means a path with less maximum effort to `(r, c)` has already been found and processed. This path is obsolete, so it's skipped.
    *   **Destination Check**: If the extracted cell `(r, c)` is the target cell `(rows-1, cols-1)`, the `current_effort` is the answer, and the algorithm returns it.
    *   **Neighbor Exploration**: For each valid neighbor `(nr, nc)` of the current cell `(r, c)`:
        *   Calculate `edge_effort`: The absolute height difference between `heights[r][c]` and `heights[nr][nc]`.
        *   Calculate `new_max_effort`: This is the crucial step. It's `max(current_effort, edge_effort)`. This ensures that the effort for the path up to `(nr, nc)` (via `(r, c)`) reflects the largest single step taken so far.
        *   **Relaxation**: If `new_max_effort` is less than the currently recorded `dist[nr][nc]`, it means a better path to `(nr, nc)` has been found. Update `dist[nr][nc]` with `new_max_effort` and push `(new_max_effort, nr, nc)` into the priority queue.
*   **Unreachable Destination**: If the loop finishes and the destination is not reached (e.g., if the grid is disconnected, which is usually not the case for such problems), the default `return 0` would be executed.

---

### 3. Key Design Decisions

*   **Algorithm Choice**: The problem of finding the path with the minimum *maximum* edge weight is a classic application where a modified Dijkstra's algorithm (or a shortest path variant for non-negative weights) is highly effective. It guarantees finding the optimal solution. An alternative approach (binary search on the answer combined with BFS/DFS) is also possible but often more complex to implement.
*   **Data Structures**:
    *   `dist` (2D List): A matrix to store the minimum maximum effort to reach each cell. This is the core memoization table, essential for tracking the best path found so far to any cell.
    *   `heapq` (Priority Queue): Crucial for efficiency. It ensures that the algorithm always processes the cell that has been reached with the globally minimum maximum effort first. This greedy choice, combined with the `dist` table, is what makes Dijkstra's correct.
    *   `directions` (List of Tuples): A concise way to represent the four possible cardinal moves (up, down, left, right), making neighbor iteration clean and readable.

---

### 4. Complexity

Let `R` be the number of rows and `C` be the number of columns.
The total number of cells (vertices) `V = R * C`.
The total number of possible moves (edges) `E = 4 * R * C` (each cell has at most 4 neighbors).

*   **Time Complexity**: `O(E log V)` or `O(V log V)` in dense graphs where `E` is proportional to `V`.
    *   Each cell (vertex) `(r, c)` can be pushed to the priority queue multiple times if a path with a smaller maximum effort is found. However, due to the `if current_effort > dist[r][c]: continue` check, each cell is *processed* (extracted from the heap and its neighbors explored) at most once.
    *   Each `heappop` operation takes `O(log K)` time, where `K` is the number of elements in the heap (at most `V`).
    *   Each `heappush` operation also takes `O(log K)`.
    *   In the worst case, every edge is relaxed once. For each edge relaxation, a `heappush` might occur.
    *   Thus, the complexity is dominated by `V` extractions and `E` insertions, leading to `O(V log V + E log V)`. Since `E` is proportional to `V` in a grid, this simplifies to `O(V log V)`.
    *   Therefore, **`O(R * C * log(R * C))`**.
*   **Space Complexity**:
    *   `dist` matrix: `O(R * C)` to store the minimum maximum effort for each cell.
    *   `pq` (priority queue): In the worst case, all `R * C` cells could be in the priority queue simultaneously before being processed. `O(R * C)`.
    *   Total: **`O(R * C)`**.

---

### 5. Edge Cases & Correctness

*   **Single Cell Grid (1x1)**:
    *   `rows = 1, cols = 1`. `dist[0][0]` is `0`. `pq = [(0,0,0)]`.
    *   Pops `(0,0,0)`. `r=0, c=0`. `r == rows-1` and `c == cols-1` is true. Returns `0`. **Correct**. (No effort needed to stay at the start).
*   **Small Grids (e.g., 2x1, 1x2)**:
    *   The logic for calculating `edge_effort` and `new_max_effort` correctly handles direct neighbors and will propagate the max effort to the destination. **Correct**.
*   **All Heights Identical**:
    *   `edge_effort` will always be `0`. `new_max_effort` will always be `0`. The algorithm will correctly return `0`. **Correct**.
*   **Large Height Differences**:
    *   The use of `abs()` correctly calculates the magnitude of height changes. `max()` correctly propagates the largest encountered effort. Python's `int` and `float` types handle large values without overflow issues typical in fixed-size integer languages. **Correct**.
*   **Unreachable Destination**:
    *   The final `return 0` outside the `while` loop is problematic. If `(rows-1, cols-1)` is unreachable from `(0,0)` (e.g., if the grid forms disconnected components), the loop would finish, and `dist[rows-1][cols-1]` would remain `float('inf')`. Returning `0` implies a path with no effort, which is misleading.
    *   **Correction**: A more robust approach would be to `return dist[rows-1][cols-1]` (which would be `float('inf')` if unreachable) or raise an error, or if the problem guarantees reachability, this line is indeed unreachable. Given typical competitive programming constraints, reachability is usually guaranteed.

---

### 6. Improvements & Alternatives

*   **Robustness - Unreachable Destination**:
    *   As noted above, change the final `return 0` to `return dist[rows - 1][cols - 1]` for clarity and correctness in scenarios where the destination might be unreachable. While problem statements often guarantee reachability, this makes the code more robust.
*   **Input Validation**:
    *   For a production-grade solution, consider adding checks at the beginning:
        *   `if not heights or not heights[0]: return 0` (or raise error for invalid input).
        *   `if rows == 1 and cols == 1: return 0` (minor optimization for the base case).
*   **Alternative Algorithm - Binary Search on Answer**:
    *   This problem exhibits monotonicity: if a path exists with maximum effort `X`, then a path also exists for any maximum effort `Y > X`. This property allows for a binary search on the *value of the minimum maximum effort*.
    *   **Steps**:
        1.  Determine the search range for effort: `low = 0`, `high = max_possible_height_difference` (e.g., `max(heights) - min(heights)` or simply 99 for `1 <= heights[i][j] <= 100`).
        2.  While `low <= high`:
            *   Calculate `mid_effort = (low + high) // 2`.
            *   Perform a standard BFS or DFS to check if a path exists from `(0,0)` to `(rows-1, cols-1)` where all steps have an `edge_effort <= mid_effort`.
            *   If a path exists, it means `mid_effort` is achievable, so try for a smaller effort: `ans = mid_effort`, `high = mid_effort - 1`.
            *   If no path exists, `mid_effort` is too small: `low = mid_effort + 1`.
        3.  Return `ans`.
    *   **Complexity**: `O(log(MaxEffortRange) * R * C)`. This can be competitive or even outperform Dijkstra's if `MaxEffortRange` is small relative to `log(R*C)`. For the given constraints (heights up to 100, `R, C` up to 100), both approaches are efficient.

---

### 7. Security/Performance Notes

*   **Memory Usage**: For `R, C = 100`, the `dist` matrix is `100 * 100 = 10,000` cells. The priority queue can also store up to `10,000` elements. Each element is a tuple `(float, int, int)`. This is well within typical memory limits (e.g., 256MB) for competitive programming environments.
*   **No Obvious Security Vulnerabilities**: The code operates on numerical grid data and does not interact with external systems or parse complex user inputs that could lead to injection attacks or other common security flaws.
*   **Integer/Float Overflow**: Python's arbitrary-precision integers and dynamic `float` types prevent typical overflow issues encountered in languages with fixed-size integer types. `float('inf')` is used correctly for initial large values.

### Code:
```python
import heapq
from typing import List

class Solution:
    def minimumEffortPath(self, heights: List[List[int]]) -> int:
        rows = len(heights)
        cols = len(heights[0])

        # dist[r][c] stores the minimum maximum effort to reach (r, c) from (0, 0)
        dist = [[float('inf')] * cols for _ in range(rows)]
        dist[0][0] = 0

        # Priority queue stores tuples of (current_max_effort, row, col)
        # It's ordered by current_max_effort
        pq = [(0, 0, 0)] # (effort_to_reach_this_cell, r, c)

        # Possible moves: right, left, down, up
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

        while pq:
            current_effort, r, c = heapq.heappop(pq)

            # If we've already found a path with less effort to (r, c), skip this one
            if current_effort > dist[r][c]:
                continue

            # If we reached the destination, this is the minimum effort path
            if r == rows - 1 and c == cols - 1:
                return current_effort

            # Explore neighbors
            for dr, dc in directions:
                nr, nc = r + dr, c + dc

                # Check if the neighbor is within grid boundaries
                if 0 <= nr < rows and 0 <= nc < cols:
                    # Calculate the effort required to move from (r, c) to (nr, nc)
                    edge_effort = abs(heights[r][c] - heights[nr][nc])

                    # The new maximum effort for the path to (nr, nc) via (r, c)
                    # is the maximum of the current path's effort and the edge effort
                    new_max_effort = max(current_effort, edge_effort)

                    # If this new path offers a smaller maximum effort to reach (nr, nc)
                    if new_max_effort < dist[nr][nc]:
                        dist[nr][nc] = new_max_effort
                        heapq.heappush(pq, (new_max_effort, nr, nc))

        # This line should theoretically not be reached if a path always exists
        # from (0,0) to (rows-1, cols-1), as per typical problem constraints.
        return 0
```
