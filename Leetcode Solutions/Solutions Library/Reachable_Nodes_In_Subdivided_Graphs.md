## Reachable Nodes In Subdivided Graphs
**Language:** python
**Tags:** python,object-oriented,graph,dijkstra's algorithm,priority queue

### Description:
This Python code implements a solution to count reachable nodes in a special type of graph. The graph starts with `n` original nodes and a set of `edges`. Each edge `[u, v, cnt]` between original nodes `u` and `v` signifies that `cnt` *new, subdivided* nodes are inserted between `u` and `v`. This creates a path `u -> s1 -> s2 -> ... -> scnt -> v` where `s_i` are the new nodes, making the total path length `cnt + 1`. The goal is to find the total number of unique nodes (original and subdivided) reachable from node 0 within `maxMoves` steps.

---

### 1. Overview & Intent

The code aims to solve the "Reachable Nodes In Subdivided Graph" problem.
It needs to:
*   Determine the shortest path (in terms of moves) from the starting node (node 0) to all *original* nodes.
*   Count all *original* nodes that are reachable within `maxMoves`.
*   For each original edge `[u, v, cnt]`, calculate how many *subdivided* nodes along that specific edge are reachable within `maxMoves` from node 0, considering paths through both `u` and `v`.
*   Sum these counts to get the total unique reachable nodes.

---

### 2. How It Works

The solution leverages Dijkstra's algorithm to find shortest paths to original nodes, then processes edges to count subdivided nodes.

1.  **Graph Representation**:
    *   An adjacency list `adj` is built from the input `edges`.
    *   For an edge `(u, v, cnt)`, `adj[u]` stores `(v, cnt)` and `adj[v]` stores `(u, cnt)`. This means for each neighbor `v` of `u`, we also store the `subdivision_count` for the edge `(u, v)`.

2.  **Dijkstra's Algorithm Initialization**:
    *   A dictionary `dist` is initialized, mapping each original node `i` to `float('inf')`, representing an unknown distance.
    *   `dist[0]` is set to `0` as it's the starting node.
    *   A min-priority queue `pq` is initialized with `(0, 0)`, representing `(distance, node)`.

3.  **Run Dijkstra's Algorithm**:
    *   The standard Dijkstra loop is executed:
        *   Pop the node `u` with the smallest distance `d` from `pq`.
        *   If `d` is greater than `dist[u]`, it means a shorter path to `u` has already been found, so skip.
        *   For each neighbor `v` of `u` with `cnt` subdivisions on their connecting edge:
            *   The cost to traverse the *entire* path between `u` and `v` (including all `cnt` subdivided nodes and reaching `v`) is `cnt + 1` steps.
            *   If `dist[u] + (cnt + 1)` is less than `dist[v]`, a shorter path to `v` is found. Update `dist[v]` and push `(dist[v], v)` to `pq`.
    *   After Dijkstra completes, `dist[i]` will hold the minimum number of moves required to reach *original* node `i` from node 0.

4.  **Calculate Total Reachable Nodes**:
    *   `reachable_count` is initialized to `0`.
    *   **Original Nodes**: Iterate through all `n` original nodes. If `dist[i] <= maxMoves`, it means original node `i` is reachable. Increment `reachable_count`.
    *   **Subdivided Nodes**: Iterate through the *original list of edges* `(u, v, cnt)`:
        *   `reach_from_u`: Calculate how many steps one can take *into* the `u-v` path starting from `u`. This is `maxMoves - dist[u]`. Use `max(0, ...)` to ensure it's not negative.
        *   `reach_from_v`: Similarly, calculate steps *into* the `u-v` path starting from `v`. This is `maxMoves - dist[v]`.
        *   The total number of *unique* subdivided nodes reachable along this specific edge is `min(cnt, reach_from_u + reach_from_v)`.
            *   We can't reach more than `cnt` subdivided nodes, as that's all that exists.
            *   `reach_from_u + reach_from_v` represents the total "reach" into the subdivided path from both ends. Each unit of reach corresponds to visiting one unique subdivided node.
        *   Add this `reachable_subdivided_on_edge` count to `reachable_count`.
    *   Finally, return `reachable_count`.

---

### 3. Key Design Decisions

*   **Dijkstra's Algorithm**: Chosen because it efficiently finds the shortest path in a graph with non-negative edge weights. Here, each "move" has a weight of 1, and the total steps to traverse an edge `u-v` (with `cnt` subdivisions) is `cnt + 1`, which is always non-negative.
*   **Adjacency List with `subdivision_count`**: This is a standard and efficient way to represent the graph. Storing `cnt` directly with the neighbor simplifies the Dijkstra step.
*   **`dist` Dictionary**: Used to store the minimum moves to reach each *original* node. This allows for quick lookups during Dijkstra and for the final calculation. A list could also be used since node IDs are `0` to `n-1`.
*   **`min(cnt, reach_from_u + reach_from_v)` for Subdivided Nodes**: This is the crucial part for correctly counting subdivided nodes:
    *   `reach_from_u` and `reach_from_v` ensure we only count nodes we can actually step onto from either end within `maxMoves`.
    *   The `min(cnt, ...)` ensures we don't overcount if `reach_from_u + reach_from_v` is greater than the actual number of subdivisions `cnt`. This elegantly handles cases where an edge is fully traversed from both ends.
*   **Iterating through `edges` for Subdivided Nodes**: By iterating through the *original* `edges` list, we guarantee that each unique path segment `(u,v)` and its `cnt` subdivided nodes are considered exactly once, preventing double-counting.

---

### 4. Complexity

Let `N` be the number of original nodes and `E` be the number of original edges.

*   **Time Complexity**:
    *   Building the adjacency list: `O(E)`
    *   Dijkstra's Algorithm: `O(E log N)` using a binary heap (Python's `heapq`). Each edge relaxation might involve a heap push/pop, and each node is processed once.
    *   Counting original reachable nodes: `O(N)`
    *   Counting subdivided reachable nodes: `O(E)` (iterating through the original edges once).
    *   **Total**: `O(E log N + N)`. Given typical constraints, this is dominated by Dijkstra.

*   **Space Complexity**:
    *   Adjacency list `adj`: `O(N + E)` (stores `N` keys and `2E` neighbor entries).
    *   Distance dictionary `dist`: `O(N)`
    *   Priority queue `pq`: `O(N)` in the worst case (all nodes pushed onto the queue).
    *   **Total**: `O(N + E)`.

---

### 5. Edge Cases & Correctness

*   **`maxMoves = 0`**:
    *   `dist[0]` is 0, others `inf`. `reachable_count` will be 1 (for node 0).
    *   `reach_from_u` and `reach_from_v` will be `max(0, 0 - dist[u])` (which is 0 for `u=0` and potentially negative for `u!=0` but capped at 0).
    *   `reachable_subdivided_on_edge` will be `min(cnt, 0 + 0) = 0`. Correctly handles this case.
*   **Disconnected Graph**: Dijkstra naturally handles this. Nodes unreachable from 0 will have `dist[i] = float('inf')`, causing `maxMoves - dist[i]` to be negative and capped at 0, thus not contributing any reachable nodes from their side.
*   **`n = 1` (single node)**: `edges` will be empty. `dist[0]` remains 0. `reachable_count` becomes 1. The loops for edges won't run. Correct.
*   **`cnt = 0` for an edge**:
    *   Dijkstra considers the path length as `dist[u] + 0 + 1 = dist[u] + 1`. This is correct, as `u` and `v` are directly connected with 1 move.
    *   For subdivided nodes, `reachable_subdivided_on_edge = min(0, reach_from_u + reach_from_v)`, which is `0`. Correct, as there are no subdivided nodes.
*   **`maxMoves` extremely large**: If `maxMoves` is large enough to traverse all edges completely, `reach_from_u + reach_from_v` will likely be greater than `cnt`, so `min(cnt, ...)` will cap it at `cnt`, correctly counting all subdivided nodes on that edge.
*   **Large `N`, `E`, `maxMoves`, `cnt`**: Python's arbitrary precision integers handle large move counts and total reachable nodes. `float('inf')` for distances also works as expected. The complexity is efficient enough for typical competitive programming constraints.

---

### 6. Improvements & Alternatives

*   **`dist` data structure**: While `dict` is perfectly fine for sparse node IDs or small `N`, a `list` `[float('inf')] * n` could be marginally faster for lookups and updates if `N` is large and node IDs are dense (0 to `n-1`), which is the case here. This is a minor stylistic/micro-optimization.
*   **Clarity of `cnt+1`**: The current comments are good. Emphasizing that `cnt+1` represents moving *through all `cnt` subdivisions and landing on node `v`* is key.
*   **No other major algorithmic improvements**: Dijkstra is optimal for this type of shortest path problem. The two-phase counting (original then subdivided) is a logical and efficient way to handle the problem constraints.

---

### 7. Security/Performance Notes

*   **Security**: The code operates on graph data and involves standard algorithms; there are no inherent security vulnerabilities.
*   **Performance**: The solution is efficient, leveraging Dijkstra's algorithm for the most computationally intensive part. The time complexity `O(E log N + N)` is generally acceptable for typical competitive programming constraints where `N` and `E` are up to 10^4. Python's `heapq` is a C-implemented min-heap, providing good performance for the priority queue operations.
*   The use of `collections.defaultdict` is efficient for building the adjacency list as it handles missing keys gracefully without explicit checks.

### Code:
```python
class Solution:
    def reachableNodes(self, edges: List[List[int]], maxMoves: int, n: int) -> int:
        import collections
        import heapq

        # 1. Build Adjacency List for the original graph
        # adj[u] will store (v, subdivision_count)
        adj = collections.defaultdict(list)
        for u, v, cnt in edges:
            adj[u].append((v, cnt))
            adj[v].append((u, cnt))

        # 2. Dijkstra Initialization
        # dist[i] stores the shortest distance from node 0 to original node i
        dist = {i: float('inf') for i in range(n)}
        dist[0] = 0
        
        # Priority queue stores (distance, node)
        pq = [(0, 0)] 

        # 3. Run Dijkstra's Algorithm
        while pq:
            d, u = heapq.heappop(pq)

            # If we found a shorter path to u already, skip
            if d > dist[u]:
                continue

            # Explore neighbors of u
            for v, cnt in adj[u]:
                # The cost to traverse the entire path between u and v
                # (including cnt new nodes and 2 original nodes) is cnt + 1
                # Each step on this path costs 1.
                
                # If a shorter path to v is found through u
                if dist[u] + cnt + 1 < dist[v]:
                    dist[v] = dist[u] + cnt + 1
                    heapq.heappush(pq, (dist[v], v))

        # 4. Calculate Total Reachable Nodes
        reachable_count = 0

        # Count original nodes reachable within maxMoves
        for i in range(n):
            if dist[i] <= maxMoves:
                reachable_count += 1

        # Count subdivided nodes reachable within maxMoves
        # For each original edge [u, v, cnt]
        for u, v, cnt in edges:
            # Number of steps we can take into the u-v path from u
            # This is maxMoves - dist[u], but not less than 0
            reach_from_u = max(0, maxMoves - dist[u])
            
            # Number of steps we can take into the u-v path from v
            # This is maxMoves - dist[v], but not less than 0
            reach_from_v = max(0, maxMoves - dist[v])
            
            # The total number of unique subdivided nodes reachable along this edge
            # is the minimum of the actual number of subdivisions (cnt)
            # and the sum of steps we can take from both ends.
            reachable_subdivided_on_edge = min(cnt, reach_from_u + reach_from_v)
            
            reachable_count += reachable_subdivided_on_edge
            
        return reachable_count
```
