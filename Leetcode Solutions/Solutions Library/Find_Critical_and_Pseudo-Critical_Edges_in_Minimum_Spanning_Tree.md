## Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree
**Language:** python
**Tags:** python,oop,kruskal's algorithm,disjoint set union,minimum spanning tree

### Description:
This code identifies critical and pseudo-critical edges in a connected, undirected graph. It leverages Kruskal's algorithm and a Disjoint Set Union (DSU) data structure as its core components.

---

### 1. Overview & Intent

The primary goal of this code is to categorize edges in a given graph into two types:
*   **Critical Edges:** These are edges whose removal would either increase the weight of the Minimum Spanning Tree (MST) or disconnect the graph entirely, making it impossible to form an MST. Any MST of the graph *must* include all critical edges.
*   **Pseudo-Critical Edges:** These are edges that are not critical, but can still be part of *some* MSTs. Forcing the inclusion of a pseudo-critical edge will still result in an MST with the same minimum total weight as the original graph's MST.

The algorithm uses a brute-force approach, repeatedly running a modified Kruskal's algorithm to test each edge's impact.

### 2. How It Works

The solution is built around a `DSU` class and a `kruskal` helper function within the `Solution` class.

*   **DSU (Disjoint Set Union) Class:**
    *   `__init__(self, n)`: Initializes `n` disjoint sets, where each element `i` is its own parent (`self.parent[i] = i`). `self.count` tracks the number of disjoint sets.
    *   `find(self, i)`: Finds the representative (root) of the set containing element `i`. It employs **path compression**: during the traversal, it updates the parent of `i` directly to the root, flattening the tree for faster future lookups.
    *   `union(self, i, j)`: Merges the sets containing `i` and `j`. It finds their roots (`root_i`, `root_j`). If they are different, it sets `root_i`'s parent to `root_j` and decrements `self.count`. Returns `True` if a union occurred (i.e., they were in different sets), `False` otherwise.

*   **Solution Class - `findCriticalAndPseudoCriticalEdges`:**
    1.  **Augment and Sort Edges:**
        *   Each edge `[u, v, w]` is augmented with its original index, becoming `[u, v, w, original_index]`. This index is crucial for referring back to specific edges later.
        *   The `augmented_edges` list is then sorted by edge weight `w` in ascending order. This is a prerequisite for Kruskal's algorithm.
    2.  **`kruskal` Helper Function:**
        *   This function implements Kruskal's algorithm to find the MST weight of a graph given a sorted list of edges.
        *   It takes `n_nodes`, `sorted_augmented_edges`, and two optional parameters:
            *   `exclude_original_idx`: If specified, the edge with this original index is completely skipped during MST construction.
            *   `include_original_idx`: If specified, the edge with this original index is *forced* to be included first, provided it doesn't form a cycle immediately (i.e., its endpoints are not already connected).
        *   It initializes a new `DSU` instance.
        *   If `include_original_idx` is set, it finds and processes that specific edge first.
        *   It then iterates through the *remaining* sorted edges, using `dsu.union(u, v)` to add an edge if it connects two previously disjoint components. It accumulates `mst_weight` and `edges_count`.
        *   An optimization stops the loop once `n_nodes - 1` edges (indicating a spanning tree) have been added.
        *   Returns `mst_weight` if an MST of `n_nodes - 1` edges is formed; otherwise, returns `float('inf')` to signify a disconnected graph or failure to form an MST.
    3.  **Calculate Base MST Weight:**
        *   The `kruskal` function is called with no exclusions or forced inclusions to find the `base_mst_weight` of the original graph.
    4.  **Find Critical Edges:**
        *   It iterates through each `original_index` from `0` to `len(edges) - 1`.
        *   For each `i`, it calls `kruskal` again, explicitly *excluding* the edge at `original_idx = i`.
        *   If the returned `mst_weight_without_edge` is greater than `base_mst_weight` (or `float('inf')`), then edge `i` is critical.
    5.  **Find Pseudo-Critical Edges:**
        *   It iterates through each `original_index` from `0` to `len(edges) - 1`, skipping any edges already identified as critical.
        *   For each remaining `i`, it calls `kruskal` again, explicitly *forcing the inclusion* of the edge at `original_idx = i`.
        *   If the returned `mst_weight_with_forced_edge` is *equal* to `base_mst_weight`, then edge `i` is pseudo-critical.
    6.  **Return:** Returns a list containing two lists: `[critical_edges, pseudo_critical_edges]`.

### 3. Key Design Decisions

*   **Disjoint Set Union (DSU):**
    *   **Purpose:** Efficiently tracks connected components and detects cycles when adding edges in Kruskal's algorithm. `find` (with path compression) and `union` are nearly constant-time amortized operations.
    *   **Decision:** A standard DSU implementation is used.
    *   **Trade-off:** The DSU implementation lacks "union by rank" or "union by size," which would further optimize tree height balancing. While path compression significantly improves performance, adding rank/size could offer marginal gains in worst-case scenarios for the `union` operation.

*   **Kruskal's Algorithm:**
    *   **Purpose:** Builds an MST by iteratively adding the lowest-weight edges that do not form a cycle.
    *   **Decision:** Chosen for its suitability with DSU and its straightforward logic for processing edges by weight.
    *   **Trade-off:** Kruskal's requires sorting all edges initially (`O(E log E)`), which can be a bottleneck for very dense graphs. Prim's algorithm, for example, might be faster for dense graphs if implemented with a Fibonacci heap.

*   **Brute-Force Edge Testing:**
    *   **Purpose:** To identify critical and pseudo-critical edges, the algorithm individually tests each edge's impact on the MST.
    *   **Decision:** This approach is conceptually simple and easy to implement.
    *   **Trade-off:** This leads to `O(E)` separate Kruskal's runs, making the overall complexity higher than more advanced methods that might find these edges in a single or fewer passes.

*   **Augmenting Edges with Original Indices:**
    *   **Purpose:** After sorting, the original positions of edges are lost. The added `original_index` allows unique identification and selective exclusion/inclusion of specific edges.
    *   **Decision:** Essential for the `exclude_original_idx` and `include_original_idx` parameters in the `kruskal` helper.

### 4. Complexity

Let `N` be the number of nodes and `E` be the number of edges.

*   **DSU Operations:** With path compression, both `find` and `union` operations have an amortized time complexity of `O(α(N))`, where `α` is the inverse Ackermann function, which is practically a very small constant (less than 5 for any realistic `N`).
*   **Augmenting Edges:** `O(E)`
*   **Sorting Edges:** `O(E log E)`
*   **`kruskal` Helper Function:**
    *   DSU initialization: `O(N)`
    *   Iterating through `E` edges, each involving a `find` and `union`: `O(E * α(N))`
    *   Total for one `kruskal` call (assuming edges are already sorted): `O(N + E * α(N))`
*   **Overall Time Complexity:**
    1.  `O(E)` for augmenting edges.
    2.  `O(E log E)` for sorting edges.
    3.  `O(N + E * α(N))` for the `base_mst_weight` calculation.
    4.  `O(E)` iterations for critical edges, each calling `kruskal`: `E * O(N + E * α(N))`.
    5.  `O(E)` iterations for pseudo-critical edges, each calling `kruskal`: `E * O(N + E * α(N))`.
    *   Total: `O(E log E + E * (N + E * α(N)))`. Since `E * α(N)` usually dominates `N`, this simplifies to `O(E log E + E^2 * α(N))`.
    *   Given `α(N)` is a very small constant, this is effectively `O(E log E + E^2)`.
*   **Space Complexity:**
    *   `self.parent` in DSU: `O(N)`
    *   `augmented_edges`: `O(E)`
    *   Recursion stack for `DSU.find` (due to path compression, typically shallow, but worst-case `O(N)` without iterative rewrite).
    *   Total: `O(N + E)`

### 5. Edge Cases & Correctness

*   **Disconnected Graph:** If the graph cannot form an MST (e.g., it has isolated components), `kruskal` correctly returns `float('inf')` because it won't be able to connect `n_nodes - 1` edges. This naturally handles cases where removing a critical edge disconnects the graph.
*   **Graph with `n=1` node:** `DSU(1)` works. `n_nodes - 1` edges required is 0. `base_mst_weight` would be 0. Empty `critical_edges` and `pseudo_critical_edges` is correct.
*   **Empty `edges` list:** Loops for critical/pseudo-critical edges will not run, returning empty lists, which is correct.
*   **Parallel Edges:** Kruskal's algorithm naturally handles parallel edges. When sorted by weight, it considers the cheapest version first. If a cheaper parallel edge connects two components, it's chosen; if a more expensive one would form a cycle, it's skipped. The original indices ensure distinct tracking.
*   **DSU Pathological Chains:** While path compression significantly mitigates it, a very long chain of parents in DSU (especially without union by rank/size) could theoretically lead to deep recursion for `find`. Python's default recursion limit is usually sufficient, but iterative DSU `find` can prevent potential stack overflow for extremely large `N`.

### 6. Improvements & Alternatives

*   **DSU Optimization (Union by Rank/Size):** Implementing "union by rank" or "union by size" alongside path compression in the `DSU` class would further optimize the `union` operation, potentially making it slightly faster in practice by keeping the trees flatter.
*   **`kruskal` Helper Optimization:**
    *   **Forced Edge Lookup:** The `for edge in sorted_augmented_edges: if edge[3] == include_original_idx:` loop to find the `forced_edge` in `kruskal` is `O(E)`. This is done repeatedly for each pseudo-critical check. A simple optimization would be to create a mapping (e.g., `original_idx_to_edge_map = {edge[3]: edge for edge in augmented_edges}`) once after augmentation. Then, `forced_edge = original_idx_to_edge_map.get(include_original_idx)`. This reduces lookup to `O(1)`.
    *   **Avoid Redundant Edge Skipping:** The check `if original_idx == include_original_idx: continue` within the main loop of `kruskal` is fine, but it assumes the `forced_edge` was indeed processed first. It could be cleaner to remove the `forced_edge` from `sorted_augmented_edges` before the main loop or pass a filtered list.
*   **Faster Critical/Pseudo-Critical Detection:** The current approach involves `O(E)` Kruskal runs. More advanced algorithms can find critical (bridge) and pseudo-critical edges more efficiently:
    *   **Critical Edges:** Identifying bridges in the graph's Biconnected Components (using Tarjan's or similar algorithms) can help, but needs careful adaptation for MSTs. A more direct method is to build an MST, then for each edge not in the MST, find the cycle it creates; if all edges on that cycle are strictly heavier, it's critical.
    *   **Pseudo-Critical Edges:** For a non-critical edge `(u, v)` with weight `w` to be pseudo-critical, it must be possible to substitute it for an edge `(x, y)` on the unique path between `u` and `v` in an MST such that `w = weight(x, y)`. This involves building an MST, then for each non-MST edge, finding the heaviest edge on the path it creates in the MST (e.g., using LCA and tree queries). This could bring complexity closer to `O(E log E)` or `O(E * α(N))`.
*   **Readability:**
    *   Add type hints for the `kruskal` helper function's parameters and return value.
    *   Extract the `forced_edge` lookup logic as suggested above.
    *   Consider an iterative implementation of `DSU.find` to avoid potential recursion depth issues, especially in languages with strict recursion limits.

### 7. Security/Performance Notes

*   **Performance Bottleneck:** The `O(E^2)` dominant factor from running `E` Kruskal's calls means the solution might be too slow for very large inputs. If `E` is in the order of `N^2` (dense graph), the complexity approaches `O(N^4)`. For typical competitive programming limits like `N=100`, `E=N*(N-1)/2 = 4950`, `E^2` is around `2.4 * 10^7`, which is acceptable. However, for `N=500`, `E ~ 125,000`, `E^2` is `1.5 * 10^{10}`, which would definitely TLE.
*   **Python Recursion Limit:** As noted, the recursive `DSU.find` could, in extreme pathological cases without union by rank/size, hit Python's default recursion limit (usually 1000 or 3000). While path compression usually prevents this, for very large `N`, an iterative `find` or increasing the recursion limit (`sys.setrecursionlimit()`) might be necessary.

### Code:
```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.count = n # Number of disjoint sets

    def find(self, i):
        if self.parent[i] == i:
            return i
        self.parent[i] = self.find(self.parent[i])
        return self.parent[i]

    def union(self, i, j):
        root_i = self.find(i)
        root_j = self.find(j)
        if root_i != root_j:
            self.parent[root_i] = root_j
            self.count -= 1
            return True
        return False

class Solution:
    def findCriticalAndPseudoCriticalEdges(self, n: int, edges: List[List[int]]) -> List[List[int]]:

        # 1. Augment edges with their original indices
        # Each edge becomes [u, v, w, original_index]
        augmented_edges = []
        for i, edge in enumerate(edges):
            augmented_edges.append(edge + [i])

        # Sort augmented edges by weight. This is crucial for Kruskal's.
        # We will pass this sorted list to the kruskal helper function.
        augmented_edges.sort(key=lambda x: x[2])

        # Helper function for Kruskal's algorithm
        # Returns the MST weight, or float('inf') if an MST cannot be formed.
        # n_nodes: total number of vertices
        # sorted_augmented_edges: the list of edges already sorted by weight, with original indices
        # exclude_original_idx: the original index of an edge to explicitly exclude from consideration
        # include_original_idx: the original index of an edge to explicitly include first
        def kruskal(n_nodes, sorted_augmented_edges, exclude_original_idx=-1, include_original_idx=-1):
            dsu = DSU(n_nodes)
            mst_weight = 0
            edges_count = 0

            # If an edge must be included, process it first
            if include_original_idx != -1:
                # Find the specific edge to include by its original index
                forced_edge = None
                for edge in sorted_augmented_edges:
                    if edge[3] == include_original_idx:
                        forced_edge = edge
                        break
                
                if forced_edge: # This edge should always be found if include_original_idx is valid
                    u, v, w, _ = forced_edge
                    if dsu.union(u, v): # Add if it connects two previously disconnected components
                        mst_weight += w
                        edges_count += 1
                    # If union fails, it means adding this edge creates a cycle.
                    # In such a case, this forced edge cannot be part of an MST that has minimum weight
                    # (unless it's the only way to connect components, which is handled by the main loop).
                    # For the purpose of finding pseudo-critical, we just don't add its weight if it's redundant.

            # Iterate through all other edges (already sorted by weight)
            for u, v, w, original_idx in sorted_augmented_edges:
                # Skip the excluded edge
                if original_idx == exclude_original_idx:
                    continue
                # Skip the included edge if it was already processed (to avoid double counting/processing)
                if original_idx == include_original_idx:
                    continue

                if dsu.union(u, v): # If adding this edge doesn't create a cycle
                    mst_weight += w
                    edges_count += 1
                    if edges_count == n_nodes - 1: # Optimization: MST formed
                        break

            # An MST must connect all n_nodes vertices using n_nodes - 1 edges.
            # If fewer edges are used, the graph is disconnected or an MST couldn't be formed.
            if edges_count == n_nodes - 1:
                return mst_weight
            else:
                return float('inf') # Indicate that an MST could not be formed

        # 2. Calculate the base MST weight of the original graph
        base_mst_weight = kruskal(n, augmented_edges)

        critical_edges = []
        pseudo_critical_edges = []

        # 3. Find Critical Edges
        # An edge is critical if its removal increases the MST weight or disconnects the graph.
        # We iterate through the original edges using their original indices (0 to len(edges)-1).
        for i in range(len(edges)):
            current_edge_original_idx = i # The original index of the edge

            mst_weight_without_edge = kruskal(n, augmented_edges, exclude_original_idx=current_edge_original_idx)

            # If removing this edge makes the MST weight higher, or disconnects the graph (indicated by float('inf'))
            if mst_weight_without_edge > base_mst_weight:
                critical_edges.append(current_edge_original_idx)

        # 4. Find Pseudo-Critical Edges
        # An edge is pseudo-critical if it's not critical, and forcing its inclusion
        # results in an MST with the same weight as the base MST.
        for i in range(len(edges)):
            current_edge_original_idx = i # The original index of the edge

            # Skip edges that are already identified as critical
            if current_edge_original_idx in critical_edges:
                continue

            mst_weight_with_forced_edge = kruskal(n, augmented_edges, include_original_idx=current_edge_original_idx)

            # If forcing this edge yields an MST with the base weight, it's pseudo-critical
            if mst_weight_with_forced_edge == base_mst_weight:
                pseudo_critical_edges.append(current_edge_original_idx)

        return [critical_edges, pseudo_critical_edges]
```
