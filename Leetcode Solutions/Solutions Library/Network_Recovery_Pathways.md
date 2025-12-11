## Network Recovery Pathways
**Language:** python
**Tags:** python,oop,graph,dijkstra,binary search

### Description:
 This solution effectively tackles a complex graph problem using a combination of binary search and a modified Dijkstra's algorithm.

---

### 1. Overview & Intent

This code aims to find the **maximum possible minimum edge cost** `X` such that there exists a path from node `0` to node `n-1` (where `n` is the total number of nodes) satisfying three conditions:

1.  **Edge Cost Threshold**: Every edge along this path must have a cost greater than or equal to `X`.
2.  **Total Path Cost**: The sum of costs of all edges in this path must not exceed `k`.
3.  **Intermediate Node Online Status**: All intermediate nodes (i.e., not node `0` or `n-1`) on the path must be marked as "online" (`online[v] == True`).

The problem essentially asks us to maximize the "quality" of the weakest link in a path, while ensuring the path remains "affordable" and "available".

### 2. How It Works

The solution employs a classic "binary search on the answer" pattern combined with Dijkstra's algorithm.

*   **Binary Search (`findMaxPathScore` function):**
    *   It determines a search range for the potential maximum minimum edge cost. The lower bound is `0` (an edge can have zero cost), and the upper bound `max_edge_cost` (the largest cost of any edge in the graph).
    *   It iteratively picks a `mid` value within this range.
    *   It calls a helper function `check(mid)` to determine if a valid path exists where *all* edges have a cost of at least `mid`.
    *   If `check(mid)` returns `True` (a path exists), it means `mid` is a possible answer, and we try to find an even larger minimum edge cost by setting `low = mid + 1`.
    *   If `check(mid)` returns `False`, `mid` is too high, so we need to try a smaller value by setting `high = mid - 1`.
    *   The binary search converges to the largest `mid` for which `check(mid)` returns `True`.

*   **`check(min_edge_cost_threshold)` Helper Function:**
    *   This function implements a **modified Dijkstra's algorithm**. Its purpose is to find the minimum total path cost from node `0` to `n-1` given the specified `min_edge_cost_threshold`.
    *   It initializes `dist` array with `infinity` for all nodes except `dist[0] = 0`.
    *   A `min-priority queue` (`heapq`) is used to store `(current_total_cost, node)`, always extracting the node reachable with the lowest cumulative cost so far.
    *   When exploring neighbors `v` from current node `u`:
        *   **Constraint 1 (Intermediate Node Online Status):** It checks if `v` is an intermediate node (`v != 0` and `v != n-1`) and if it's `online[v]`. If not, this path through `v` is invalid, and `v` is skipped.
        *   **Constraint 2 (Edge Cost Threshold):** It checks if the cost of the edge `(u, v)` is less than `min_edge_cost_threshold`. If it is, this edge is invalid for the current threshold, and `v` is skipped.
        *   **Constraint 3 (Total Path Cost `k`):** During path exploration, if `current_total_cost` already exceeds `k`, further exploration from this path is pruned, as any extension will also exceed `k`.
        *   If all constraints are met, it updates `dist[v]` if a shorter path to `v` is found and pushes `(new_total_cost, v)` to the priority queue.
    *   Finally, it returns `True` if `dist[n-1]` (the minimum total cost to reach the destination) is less than or equal to `k`, indicating a valid path exists under the given `min_edge_cost_threshold`. Otherwise, it returns `False`.

### 3. Key Design Decisions

*   **Binary Search on the Answer**: This is crucial. The property "a path exists with all edge costs >= X and total cost <= K" is monotonic with respect to `X`. If it's true for `X`, it's also true for any `X' < X`. This monotonicity makes binary search a viable and efficient strategy.
*   **Dijkstra's Algorithm**: Necessary because we need to find the *minimum total path cost* (not just *any* path) to satisfy the `k` constraint, while also applying specific edge and node filtering. A simple BFS wouldn't work with varying edge weights.
*   **Adjacency List (`adj`)**: A standard and efficient way to represent graphs, allowing quick access to all neighbors of a given node.
*   **Priority Queue (`heapq`)**: Essential for the efficiency of Dijkstra's algorithm, ensuring that nodes are processed in increasing order of their accumulated path cost, thus guaranteeing the shortest path when a node is extracted.
*   **`max_edge_cost` Calculation**: Pre-calculating the maximum edge cost helps to define a tight upper bound for the binary search range, preventing unnecessary iterations.

### 4. Complexity

Let `N` be the number of nodes and `M` be the number of edges.

*   **Time Complexity**:
    *   **Adjacency list construction**: O(M)
    *   **`max_edge_cost` calculation**: O(M)
    *   **`check` function (Dijkstra's)**: In a graph with `N` nodes and `M` edges, Dijkstra's using a binary heap (like `heapq`) is O(M log N).
    *   **Binary search**: The range of possible edge costs can be up to `max_edge_cost`. If `max_edge_cost` is `C_max`, the number of binary search iterations is O(log `C_max`).
    *   **Overall**: O(M log N * log `C_max`). Given typical constraints (e.g., `C_max` up to 10^9, log `C_max` is roughly 30), this is efficient.

*   **Space Complexity**:
    *   **Adjacency list (`adj`)**: O(N + M)
    *   **Distance array (`dist`)**: O(N)
    *   **Priority queue (`pq`)**: In the worst case, can store up to O(M) items if all nodes are visited and all edges lead to new nodes being added to the PQ, but typically O(N) or fewer. More precisely, it's O(N) for number of nodes.
    *   **Overall**: O(N + M)

### 5. Edge Cases & Correctness

*   **No Path Exists**: If node `n-1` is unreachable under any valid `min_edge_cost_threshold` (or `k` constraint), `dist[n-1]` will remain `float('inf')` in `check()`. The binary search will correctly conclude that no such `mid` exists, and `ans` will remain `-1`, which is correct.
*   **Start/End Nodes (0 and n-1)**: The problem statement implies these are not considered "intermediate". The check `if v != 0 and v != n - 1 and not online[v]: continue` correctly implements this by only applying the `online` constraint to true intermediate nodes.
*   **Disconnected Graph**: If `n-1` is not reachable from `0` even without `online` or edge cost constraints, `dist[n-1]` will remain `inf`, and the solution will return `-1`. Correct.
*   **`k` constraint**: If `k` is too small, `dist[n-1]` will exceed `k` or remain `inf`, leading to `check()` returning `False`, correctly guiding the binary search.
*   **Empty `edges`**: If `edges` is empty, `max_edge_cost` will be `0`. If `n > 1`, `check(0)` will correctly show no path from `0` to `n-1` (unless `0` and `n-1` are the same node, `n=1`). `ans` remains `-1`. If `n=1`, `dist[0]=0`, `check(0)` returns `True` if `k>=0`, `ans` becomes `0`. This is also correct, as a single node path has 0 cost and 0 minimum edge cost.
*   **Graph with only 1 node (`n=1`)**: The code handles this correctly. `n-1` is `0`. `dist[0]` is initialized to `0`. `check(mid)` will immediately return `dist[0] <= k`, which is `0 <= k`. If `k >= 0`, `ans` will be `0`. This is correct as a path from node `0` to `0` has total cost `0` and effectively no edges, so the minimum edge cost can be considered `0`.

### 6. Improvements & Alternatives

*   **Readability/Clarity**:
    *   **Constants for Start/End Nodes**: Define `SOURCE_NODE = 0` and `DEST_NODE = n - 1`. This makes the `if v != SOURCE_NODE and v != DEST_NODE and not online[v]:` line more semantic.
    *   **Docstrings**: Add comprehensive docstrings to the class, `findMaxPathScore` method, and especially the `check` helper function, explaining their purpose, parameters, and return values.
    *   **Variable Names**: Consider renaming `min_edge_cost_threshold` to `current_min_edge_cost` or `edge_threshold` for brevity in context.

*   **Minor Optimizations (Not Critical)**:
    *   The `max_edge_cost` could be initialized to -1 (or `float('-inf')`) if costs can be negative, but problem usually implies non-negative costs. For non-negative costs, `0` is fine as it allows `0`-cost edges to be considered for the `max_edge_cost` variable as well.
    *   The `if current_total_cost > k: continue` check in Dijkstra is good pruning.

*   **Alternative Approaches (Less Efficient or More Complex for this problem)**:
    *   **Flow Algorithms**: Could potentially be framed as a min-cost max-flow variant or similar, but would likely be over-engineered and less intuitive than the binary search + Dijkstra approach for this specific problem.
    *   **Bellman-Ford / SPFA**: Not suitable here if edge weights are non-negative, as Dijkstra is more efficient. If negative edge weights were allowed (they are not, implied by "cost"), then Bellman-Ford or SPFA might be needed, but the current problem structure doesn't suggest negative cycles.

### 7. Security/Performance Notes

*   **Large Inputs**: The algorithm's complexity `O(M log N * log C_max)` handles moderately large graphs. `N` up to `10^5`, `M` up to `10^5` with `C_max` up to `10^9` would be roughly `10^5 * log(10^5) * log(10^9)` which is about `10^5 * 17 * 30`, approximately `5 * 10^7` operations. This is generally acceptable within typical time limits (1-2 seconds) for competitive programming.
*   **Memory Usage**: `O(N+M)` for adjacency list and `dist` array is also standard and efficient. For large graphs, ensure `N` and `M` do not exceed memory limits.
*   **Integer Overflow**: Python's arbitrary-precision integers avoid typical overflow issues seen in languages like C++/Java for `total_cost` or `dist` values, which is a robustness advantage here, especially if `k` or `edge_costs` are very large. `float('inf')` for distances is also robust.

---

Overall, this is a well-designed and correctly implemented solution that effectively combines two powerful algorithmic techniques to solve a nuanced graph problem. The code is clean and follows good practices for clarity, although minor readability enhancements could be made.

### Code:
```python
import heapq
from typing import List

class Solution:
    def findMaxPathScore(self, edges: List[List[int]], online: List[bool], k: int) -> int:
        n = len(online)
        adj = [[] for _ in range(n)]
        max_edge_cost = 0

        # Build adjacency list and find the maximum edge cost for binary search range
        for u, v, cost in edges:
            adj[u].append((v, cost))
            max_edge_cost = max(max_edge_cost, cost)

        # Helper function to check if a path exists from 0 to n-1
        # with all edges having cost >= min_edge_cost_threshold
        # and total path cost <= k, with intermediate nodes online.
        def check(min_edge_cost_threshold: int) -> bool:
            # dist[i] stores the minimum total cost to reach node i from node 0
            dist = [float('inf')] * n
            dist[0] = 0
            
            # Priority queue stores (current_total_cost, node)
            # It will always extract the path with the smallest total cost
            pq = [(0, 0)]

            while pq:
                current_total_cost, u = heapq.heappop(pq)

                # If we found a shorter path to u already, skip this one
                if current_total_cost > dist[u]:
                    continue
                
                # If the current path's total cost already exceeds k,
                # any further extension will also exceed k, so prune.
                if current_total_cost > k:
                    continue

                for v, cost in adj[u]:
                    # Constraint 1: Intermediate nodes must be online
                    # Nodes 0 and n-1 are always online and are not considered "intermediate"
                    # for the purpose of this constraint.
                    if v != 0 and v != n - 1 and not online[v]:
                        continue

                    # Constraint 2: Edge cost must be at least the threshold
                    if cost < min_edge_cost_threshold:
                        continue

                    new_total_cost = current_total_cost + cost

                    # If a shorter path to v is found, update and push to priority queue
                    if new_total_cost < dist[v]:
                        dist[v] = new_total_cost
                        heapq.heappush(pq, (new_total_cost, v))
            
            # A valid path exists if the minimum total cost to reach n-1 is within k
            return dist[n - 1] <= k

        # Binary search for the maximum possible minimum edge cost
        # The possible range for the minimum edge cost is [0, max_edge_cost]
        low = 0
        high = max_edge_cost
        ans = -1 # Default if no valid path exists

        while low <= high:
            mid = low + (high - low) // 2
            if check(mid):
                # If a path exists with all edges >= mid, then mid is a possible answer.
                # Try to find a larger minimum edge cost.
                ans = mid
                low = mid + 1
            else:
                # If no such path exists, we need to try a smaller minimum edge cost.
                high = mid - 1

        return ans
```
