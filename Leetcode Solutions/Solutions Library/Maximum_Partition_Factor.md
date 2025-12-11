## Maximum Partition Factor
**Language:** python
**Tags:** python,object-oriented programming,binary search,bfs,bipartite graph

### Description:
This code aims to solve a problem related to partitioning points based on a distance constraint. It uses a combination of graph theory (bipartite graphs) and binary search.

---

### 1. Overview & Intent

The primary goal of the `maxPartitionFactor` method is to find the largest possible integer `k_factor` such that a given set of 2D `points` can be divided into exactly two non-empty groups (`S1` and `S2`), with the condition that the Manhattan distance between any two points within the *same* group is always greater than or equal to `k_factor`.

---

### 2. How It Works

The solution employs a classic "binary search on the answer" strategy, where the "answer" is the `k_factor`.

*   **Base Case Handling:** If there are 2 or fewer points (`n <= 2`), it's impossible to form two *non-empty* groups under the problem's definition or trivial, so it immediately returns `0`.
*   **Binary Search Setup:**
    *   It determines a search range for `k_factor`: `low` starts at `0`.
    *   `high` is initialized to one greater than the maximum possible Manhattan distance between any pair of points in the input. This provides a sufficiently large upper bound for the binary search.
*   **`check(k_factor)` Function (The Core Logic):**
    *   This helper function determines if a given `k_factor` is achievable.
    *   **Graph Construction:** It builds an undirected graph where each point is a node. An edge is added between two points `p1` and `p2` if their Manhattan distance `dist(p1, p2)` is *less than* `k_factor`.
        *   The rationale: If `dist(p1, p2) < k_factor`, then `p1` and `p2` *cannot* be in the same group (because the condition is `dist >= k_factor` for points in the same group). Therefore, they *must* be in different groups. This is the definition of an edge in a bipartite graph.
    *   **Bipartiteness Check:** It then performs a Breadth-First Search (BFS) to check if the constructed graph is bipartite.
        *   It uses a `colors` array to assign points to one of two "colors" (groups, `0` or `1`).
        *   Starting with an uncolored node, it assigns it `color 0` and adds it to a queue.
        *   It iteratively processes nodes from the queue: for each neighbor `v` of the current node `u`, if `v` is uncolored, it assigns `v` the opposite color of `u` and enqueues `v`.
        *   If `v` is already colored and has the *same* color as `u`, then the graph is not bipartite, and `k_factor` is *not* achievable.
        *   If the BFS completes without conflicts for all connected components, the graph is bipartite.
    *   **Non-empty Group Guarantee:** Since `n > 2`, if the graph is bipartite, it's always possible to form two non-empty groups. If the graph has edges, the bipartite coloring naturally creates two non-empty sets. If the graph has no edges (all distances are `>= k_factor`), any split into one point in `S1` and the remaining `n-1` points in `S2` will satisfy the conditions.
*   **Binary Search Loop:**
    *   The `while low < high` loop repeatedly tests `mid = low + (high - low) // 2`.
    *   If `check(mid)` is `True` (meaning `mid` is achievable), it stores `mid` as a potential answer (`ans = mid`) and tries for a larger `k_factor` by setting `low = mid + 1`.
    *   If `check(mid)` is `False`, it means `mid` is too high, so it tries a smaller `k_factor` by setting `high = mid`.
*   **Result:** The loop converges to the maximum achievable `k_factor`, which is then returned as `ans`.

---

### 3. Key Design Decisions

*   **Problem Transformation to Bipartite Graph:** This is the most critical insight. The constraint "any two points in the same group have `dist >= k_factor`" is cleverly inverted to "if `dist < k_factor`, points *must* be in different groups." This maps perfectly to the definition of an edge in a bipartite graph where connected nodes belong to different partitions.
*   **Binary Search on `k_factor`:** The property that if a `k_factor` is achievable, all smaller `k_factors` are also achievable (monotonicity) makes binary search an efficient way to find the maximum `k_factor`.
*   **Manhattan Distance:** The problem implicitly or explicitly defines distance as Manhattan distance, correctly implemented as `abs(p1[0] - p2[0]) + abs(p1[1] - p2[1])`.
*   **BFS for Bipartiteness:** A standard and efficient algorithm for coloring a graph to check if it's bipartite. DFS would also work.

---

### 4. Complexity

Let `N` be the number of points.
Let `D_max` be the maximum possible Manhattan distance between any two points (at most `4 * 10^9` given typical coordinate ranges).

*   **`check(k_factor)` Function:**
    *   **Graph Construction:** Iterates through all pairs of points: `O(N^2)` operations to calculate distances and potentially add edges. In the worst case, the graph can be dense (`O(N^2)` edges).
    *   **Bipartiteness Check (BFS):** For a graph with `V` vertices and `E` edges, BFS is `O(V + E)`. Here `V = N`, and `E` can be up to `O(N^2)`. So, the BFS is `O(N + N^2) = O(N^2)`.
    *   **Total for `check(k_factor)`:** `O(N^2)`.
*   **Binary Search:**
    *   The search range for `k_factor` is `[0, D_max]`.
    *   Number of iterations: `log(D_max)`.
*   **Overall Time Complexity:** `O(N^2 * log(D_max))`.
*   **Space Complexity:**
    *   `adj` (adjacency list): `O(N + E)`, which is `O(N^2)` in the worst case (dense graph).
    *   `colors` array: `O(N)`.
    *   `q` (BFS queue): `O(N)` in the worst case.
    *   **Total Space Complexity:** `O(N^2)`.

---

### 5. Edge Cases & Correctness

*   **`n <= 2`:** Correctly handled by returning `0`. For `n=0` or `n=1`, two non-empty groups are impossible. For `n=2`, the problem statement specifically defines the partition factor as `0`.
*   **All points are identical:** The distance between any two points is `0`.
    *   `check(0)` would return `True` (no edges added, empty graph is bipartite).
    *   `check(k_factor > 0)` would return `False` (all points form a clique, not bipartite if `N > 2`).
    *   The binary search correctly converges to `0`.
*   **All points are very far apart:** If `dist(p1, p2) >= k_factor` for *all* pairs, the graph will have no edges. An empty graph is bipartite. `check` will return `True`. The logic for ensuring non-empty groups (`n > 2`) handles this by allowing an arbitrary split (e.g., one point in S1, rest in S2).
*   **`high` bound calculation:** Calculating `high` by iterating through all pairs `O(N^2)` provides a tighter bound than a generic `4 * 10^9`, but the overall complexity remains dominated by `check(k_factor)`. It's correct and effective.
*   **Integer Overflow for `mid`:** `low + (high - low) // 2` is used, which correctly prevents overflow compared to `(low + high) // 2` if `low` and `high` were extremely large and sum could exceed max int (though less of an issue in Python).
*   **Guaranteed Non-Empty Groups:** As discussed, for `N > 2`, a bipartite coloring of the constructed graph (or an arbitrary split if the graph is empty) will always yield two non-empty groups.

---

### 6. Improvements & Alternatives

*   **Pre-calculating Distances:** All `N(N-1)/2` pairwise distances are calculated multiple times (once for `high` and once for each `check` call). These could be pre-calculated and stored in an `O(N^2)` matrix, reducing distance calculations inside `check` to `O(1)` lookups. This doesn't change the overall `O(N^2 * log(D_max))` time complexity but might offer a small constant factor speedup if distance calculation itself is slow (not an issue for Manhattan distance).
*   **Adjacency Matrix for Graph:** While `O(N^2)` space, it could be slightly faster for dense graphs than adjacency lists due to cache locality. However, for a generic graph problem, adjacency lists are usually preferred.
*   **Optimized `high` bound (Minor):** Instead of an `O(N^2)` pass to find `max_dist` for `high`, one could simply set `high = 4 * 10^9 + 1` (or `2 * 10^9 + 1` if coordinates are maxed in one direction). This removes an `O(N^2)` operation but might make the binary search range larger, slightly increasing `log(D_max)`. The current approach is more precise for `high`.
*   **DFS for Bipartiteness:** A Depth-First Search would work identically to BFS for checking bipartiteness and would have the same complexity.

---

### 7. Security/Performance Notes

*   **Performance Bottleneck:** The `O(N^2 * log(D_max))` complexity means this solution can become slow for larger `N`.
    *   For `N = 500`, `N^2 = 250,000`. `log(4*10^9)` is about `32`. Total operations: `250,000 * 32 = 8 * 10^6`, which is very fast.
    *   For `N = 2000`, `N^2 = 4,000,000`. Total operations: `4,000,000 * 32 = 1.28 * 10^8`, which might be acceptable but is pushing typical time limits (usually around `10^8` operations per second).
    *   For `N > 2000`, this approach would likely time out.
*   **Input Size Limits:** The performance heavily depends on the constraints of `N` specified in the problem statement. The current solution is well-suited for moderate `N` values (e.g., up to `N ~ 1000-2000`).
*   **No Security Concerns:** The code doesn't interact with external systems, files, or user input in a way that would introduce security vulnerabilities. It's purely an algorithmic problem.

### Code:
```python
class Solution:
    def maxPartitionFactor(self, points: List[List[int]]) -> int:
        n = len(points)

        # According to the problem statement, for n=2, the partition factor is defined as 0.
        # For n < 2, it's impossible to form two non-empty groups, so 0 is the only sensible answer.
        # The problem implies n >= 2 based on the "split into exactly two non-empty groups" phrasing.
        if n <= 2:
            return 0

        # Helper function to check if a partition factor 'k_factor' is achievable
        # This means we can split points into two non-empty groups S1, S2 such that
        # for any p1, p2 in S1, dist(p1, p2) >= k_factor, and similarly for S2.
        # This is equivalent to checking if the graph formed by points where
        # an edge (u, v) exists if dist(u, v) < k_factor is bipartite.
        def check(k_factor: int) -> bool:
            adj = [[] for _ in range(n)]
            for i in range(n):
                for j in range(i + 1, n):
                    p1 = points[i]
                    p2 = points[j]
                    dist = abs(p1[0] - p2[0]) + abs(p1[1] - p2[1])
                    if dist < k_factor:
                        # If distance is less than k_factor, these two points MUST be in different groups.
                        # Add an edge between them in our graph.
                        adj[i].append(j)
                        adj[j].append(i)

            # Perform BFS/DFS to check for bipartiteness
            colors = [-1] * n  # -1: uncolored, 0: group A, 1: group B

            for i in range(n):
                if colors[i] == -1:  # If point 'i' hasn't been colored yet (start of a new component)
                    q = collections.deque()
                    q.append(i)
                    colors[i] = 0  # Assign the starting node of this component to group A

                    while q:
                        u = q.popleft()
                        for v in adj[u]:
                            if colors[v] == -1:  # If neighbor 'v' is uncolored
                                colors[v] = 1 - colors[u]  # Assign opposite color
                                q.append(v)
                            elif colors[v] == colors[u]:
                                # Conflict: two adjacent nodes have the same color.
                                # The graph is not bipartite, so k_factor is not achievable.
                                return False
            
            # If we reach here, the graph is bipartite.
            # This means we can partition the points into two sets S0 and S1 such that
            # no two points in S0 are connected by an edge, and no two points in S1 are connected.
            # Since n > 2 and the graph is bipartite, we can always ensure both groups are non-empty:
            # - If the graph has no edges (all distances >= k_factor), then any split into
            #   two non-empty groups is valid (e.g., one point in S1, rest in S2).
            # - If the graph has edges, a bipartite coloring will naturally produce two non-empty groups.
            return True

        # Determine the search range for binary search
        low = 0
        high = 0
        # Calculate the maximum possible Manhattan distance to set an upper bound for binary search.
        # Coordinates are between -10^9 and 10^9, so max distance is 4 * 10^9.
        # A tighter bound can be found by iterating through all pairs.
        for i in range(n):
            for j in range(i + 1, n):
                p1 = points[i]
                p2 = points[j]
                dist = abs(p1[0] - p2[0]) + abs(p1[1] - p2[1])
                high = max(high, dist)
        
        # The upper bound for binary search should be max_dist + 1
        # This ensures that 'mid' can potentially reach 'max_dist'.
        high += 1 

        ans = 0
        while low < high:
            mid = low + (high - low) // 2
            if check(mid):
                ans = mid          # 'mid' is achievable, try for a larger factor
                low = mid + 1
            else:
                high = mid         # 'mid' is not achievable, try a smaller one
        
        return ans

import collections
from typing import List
```
