## Restore the Array from Adjacent Pairs
**Language:** python
**Tags:** python,oop,graph,adjacency list,graph traversal

### Description:
This Python code provides a solution to reconstruct an array given a list of its adjacent pairs. The core idea relies on the property that in a linear array, only the two endpoints have a single neighbor; all intermediate elements have exactly two neighbors.

---

### 1. Overview & Intent

*   **Goal**: Given `adjacentPairs`, which represents connections between elements in an original (unknown) array, reconstruct that array.
*   **Input**: A list of `[u, v]` pairs, where `u` and `v` are adjacent elements in the original array. Each pair represents an undirected edge.
*   **Output**: A `List[int]` representing the original array in its correct order.
*   **Assumptions**: The input `adjacentPairs` always corresponds to a single, linear array (no cycles, no disconnected components, no branching paths).

---

### 2. How It Works

The solution proceeds in three main steps:

1.  **Build Adjacency List**:
    *   An adjacency list (`adj`) is created using `collections.defaultdict(list)`.
    *   For each `(u, v)` pair in `adjacentPairs`, `v` is added to `u`'s neighbors and `u` is added to `v`'s neighbors. This effectively models the array elements as nodes in an undirected graph and the adjacent pairs as edges.

2.  **Find a Starting Endpoint**:
    *   The code iterates through all nodes in the built adjacency list.
    *   An element that is an "endpoint" of the original array will have only one neighbor (degree 1) in the graph. The first such node found is chosen as `start_node`.
    *   The total length of the original array (`n`) is calculated as `len(adjacentPairs) + 1`. This is because `p` pairs imply `p+1` elements.
    *   The first two elements of the `result` array are initialized: `result[0]` is the `start_node`, and `result[1]` is its *only* neighbor.

3.  **Reconstruct the Array**:
    *   A loop iterates from the third element (`i=2`) up to the end of the `result` array.
    *   In each iteration `i`, it determines `result[i]` based on the two preceding elements: `result[i-2]` (`prev`) and `result[i-1]` (`current`).
    *   Since `current` is an internal node (or the second-to-last node), it will have exactly two neighbors. One of these neighbors is `prev` (the element we just came from).
    *   The *other* neighbor of `current` must be `result[i]`. The code identifies this "other" neighbor and assigns it to `result[i]`.

---

### 3. Key Design Decisions

*   **Adjacency List (`collections.defaultdict(list)`)**:
    *   **Decision**: Using a hash map (dictionary) where keys are nodes and values are lists of neighbors. `defaultdict` simplifies adding new nodes without explicit existence checks.
    *   **Trade-off**: Requires O(N) space (where N is the number of elements) to store the graph. This is efficient for sparse graphs and direct access to neighbors.
*   **Finding Degree-1 Node for Start**:
    *   **Decision**: Leveraging the property that array endpoints have a degree of 1.
    *   **Trade-off**: Simple and effective for linear structures. Assumes the input graph *is* a linear array; would fail for cyclic or branching graphs (though problem constraints typically rule this out).
*   **Iterative Reconstruction with `prev` and `current`**:
    *   **Decision**: Reconstructing the path by always looking at the immediately previous and the second-to-last elements to deduce the next.
    *   **Trade-off**: This is a very efficient and direct way to traverse the unique path. It implicitly performs a depth-first search like traversal without explicit recursion or a stack.

---

### 4. Complexity

Let `N` be the number of elements in the original array, which is `len(adjacentPairs) + 1`.

*   **Time Complexity**:
    *   **Step 1 (Build Adjacency List)**: Iterating through `len(adjacentPairs)` pairs. Each append operation is O(1). Total: O(N).
    *   **Step 2 (Find Starting Endpoint)**: Iterating through unique nodes in `adj`. In the worst case, all `N` nodes. Total: O(N).
    *   **Step 3 (Reconstruct Array)**: Loop runs `N-2` times. Inside the loop, neighbor lookups are O(1) (list access) since each node has at most 2 neighbors. Total: O(N).
    *   **Overall Time Complexity**: **O(N)**.

*   **Space Complexity**:
    *   **Adjacency List (`adj`)**: Stores `N` nodes and `2 * (N-1)` edges (each edge is stored twice for an undirected graph). Total: O(N).
    *   **Result Array (`result`)**: Stores `N` elements. Total: O(N).
    *   **Overall Space Complexity**: **O(N)**.

---

### 5. Edge Cases & Correctness

*   **Smallest Array (N=2)**: If `adjacentPairs = [[1, 2]]`.
    *   `adj = {1: [2], 2: [1]}`. `start_node` could be 1 (or 2). `n=2`.
    *   `result = [1, 2]`. The loop `range(2, 2)` does not run. Correctly returns `[1, 2]`.
*   **Correctness of Reconstruction Logic**: The crucial part is `if neighbors[0] == prev: result[i] = neighbors[1] else: result[i] = neighbors[0]`.
    *   This logic correctly identifies the *other* neighbor of `current` (which is `result[i-1]`) that is not `prev` (which is `result[i-2]`). Since every intermediate node has exactly two neighbors, this ensures forward movement along the unique path of the array.
*   **Invalid/Degenerate Input**:
    *   **Empty `adjacentPairs`**: The code would attempt `result[1] = adj[start_node][0]` with `start_node = -1` (if no degree 1 node found) or an empty `adj` if `start_node` somehow became 0. This would lead to an `IndexError` or `KeyError`. *However, typical problem constraints guarantee at least one pair, or specify how to handle empty inputs.* Assuming valid input according to the problem's implicit guarantees (i.e., `len(adjacentPairs) >= 1`).
    *   **Graph with cycles or branches**: If the input doesn't form a simple linear array (e.g., `[[1,2],[2,3],[3,1]]` forms a cycle), no node would have degree 1, `start_node` would remain `-1`, and the code would fail. Again, problem constraints usually ensure valid input for "restore array".

---

### 6. Improvements & Alternatives

*   **Readability of Next Element Logic**:
    *   The `if/else` to find the "other" neighbor is concise, but for clarity, one could use `(neighbor for neighbor in neighbors if neighbor != prev).__next__()` or `list(filter(lambda x: x != prev, neighbors))[0]`. While slightly less performant, it explicitly states "find the neighbor that isn't the previous one". The current approach is more performant given the guarantee of exactly two neighbors.
*   **Robustness for Invalid Input**:
    *   Add checks for `start_node == -1` after the loop to find it, raising an error or returning an empty list if no valid start is found. This makes the function more robust to inputs that don't represent a linear array.
*   **Alternative Reconstruction (DFS/BFS)**:
    *   Once the adjacency list and a starting node are identified, a standard Depth-First Search (DFS) or Breadth-First Search (BFS) could also be used to traverse the graph and reconstruct the array. However, the current iterative approach is essentially a specialized and highly optimized DFS for this specific problem structure, avoiding explicit stack/queue management.

---

### 7. Security/Performance Notes

*   **Performance**: The solution is highly efficient, achieving optimal O(N) time and space complexity. No obvious performance bottlenecks exist.
*   **Security**: There are no inherent security vulnerabilities in this algorithm. It operates on integer lists and doesn't interact with external systems or sensitive data.

### Code:
```python
import collections
from typing import List

class Solution:
    def restoreArray(self, adjacentPairs: List[List[int]]) -> List[int]:
        # Step 1: Build Adjacency List
        # Use a defaultdict to easily append neighbors
        adj = collections.defaultdict(list)
        for u, v in adjacentPairs:
            adj[u].append(v)
            adj[v].append(u)

        # Step 2: Find a Starting Endpoint
        # An endpoint will have only one neighbor in the adjacency list.
        start_node = -1
        for node in adj:
            if len(adj[node]) == 1:
                start_node = node
                break
        
        # The number of elements in the original array is len(adjacentPairs) + 1
        n = len(adjacentPairs) + 1
        result = [0] * n
        
        # Initialize the first two elements of the result array
        result[0] = start_node
        # The second element is the only neighbor of the start_node
        result[1] = adj[start_node][0]

        # Step 3: Reconstruct the Array
        # Iterate from the third element up to the end
        for i in range(2, n):
            prev = result[i-2]    # The element two steps back
            current = result[i-1] # The immediately preceding element
            
            # Find the neighbors of the current element
            neighbors = adj[current]
            
            # The next element in the array is the neighbor of 'current'
            # that is NOT 'prev' (to avoid going backward).
            if neighbors[0] == prev:
                result[i] = neighbors[1]
            else:
                result[i] = neighbors[0]
                
        return result
```
