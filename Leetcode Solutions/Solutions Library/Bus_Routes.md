## Bus Routes
**Language:** python
**Tags:** python,oop,bfs,graph

### Description:
This code solves the problem of finding the minimum number of buses required to travel from a `source` stop to a `target` stop. It models the problem as a shortest path search on an implicit graph where nodes are bus routes, and an edge exists between two routes if they share a common stop. A Breadth-First Search (BFS) is employed to find this shortest path.

### 1. Overview & Intent

The primary goal of this code is to determine the fewest bus transfers needed to get from a specified starting bus stop (`source`) to a desired ending bus stop (`target`). Each element in `routes` represents a single bus route, listing all stops it visits. The problem asks for the minimum count of *buses taken*, not the minimum number of stops.

### 2. How It Works

1.  **Base Case**: If `source` and `target` are the same, 0 buses are needed.
2.  **Preprocessing (`stop_to_routes` map)**:
    *   A dictionary `stop_to_routes` is built. Its keys are bus stop IDs, and its values are lists of indices of all routes that service that particular stop. This allows for quick lookups: "Given a stop, which routes can I take from here?"
3.  **Source Check**: If the `source` stop is not serviced by any known route, it's impossible to start, so return -1.
4.  **BFS Initialization**:
    *   A `collections.deque` (queue) `q` is used for the BFS. Each element in the queue is a tuple `(route_idx, buses_taken)`, representing the current route being considered and the number of buses taken to reach this route.
    *   A `set` `visited_routes` keeps track of routes already visited to prevent cycles and redundant processing.
    *   All routes that service the `source` stop are added to the queue, each with `buses_taken = 1` (as taking one of these routes is the first bus). These routes are also marked as visited.
5.  **BFS Traversal**:
    *   The BFS proceeds level by level:
        *   Dequeue `(current_route_idx, buses_taken)`.
        *   **Check for Target**: If the `target` stop is among the stops visited by `current_route_idx`, then the destination has been reached, and `buses_taken` is the minimum number of buses. Return it.
        *   **Explore Next Routes**: Iterate through all stops on the `current_route`. For each `stop`:
            *   Find all `next_route_idx` that service this `stop` using the `stop_to_routes` map.
            *   If a `next_route_idx` has not been visited yet:
                *   Mark it as visited.
                *   Enqueue `(next_route_idx, buses_taken + 1)` (one more bus transfer).
6.  **No Path Found**: If the queue becomes empty and the `target` has not been reached, it means the target is unreachable from the source, so return -1.

### 3. Key Design Decisions

*   **Graph Representation (Implicit)**: Instead of explicitly building a graph of stops or routes, the `stop_to_routes` dictionary and the BFS logic implicitly define a graph where nodes are *routes*, and an edge exists between two routes if they share at least one common stop. This is a common and efficient approach for this type of problem.
*   **Breadth-First Search (BFS)**: BFS is chosen because it guarantees finding the shortest path in an unweighted graph. In this context, each "edge traversal" (transferring from one bus route to another) costs 1 "bus taken," making it an unweighted problem.
*   **`collections.defaultdict(list)` for `stop_to_routes`**: This is an excellent choice for building the mapping. It automatically creates an empty list for a stop if it's accessed for the first time, simplifying the aggregation of routes per stop.
*   **`collections.deque` for the Queue**: `deque` provides `O(1)` append and pop operations from both ends, which is optimal for BFS queues.
*   **`set` for `visited_routes`**: Using a `set` for visited routes ensures `O(1)` average-case time complexity for checking if a route has been visited and for adding new routes. This is crucial for performance and preventing infinite loops in cyclic graphs.

### 4. Complexity

Let `N` be the number of bus routes, `S` be the total number of unique stops across all routes, and `M` be the total number of stops in all routes combined (i.e., `sum(len(route) for route in routes)`). `L_max` is the maximum number of stops in a single route.

*   **Time Complexity**: `O(M + N * L_max)`
    *   **`stop_to_routes` construction**: Iterates through each stop in each route. This takes `O(M)` time.
    *   **BFS initialization**: Iterates through routes associated with the source stop, at most `O(N)` routes.
    *   **BFS traversal**:
        *   Each route is enqueued and dequeued at most once (`N` times).
        *   When a route is dequeued:
            *   Checking `target in routes[current_route_idx]` takes `O(L_max)` in the worst case (if `routes` are lists).
            *   Iterating through all stops in `current_route_idx` takes `O(L_max)`.
            *   For each stop, iterating `stop_to_routes[stop]` to find `next_route_idx`: The total number of `(stop, route_idx)` pairs stored in `stop_to_routes` is `M`. Each such pair is effectively considered at most once across the entire BFS traversal as we explore connections.
        *   Combining these, the total time for the BFS loop is `O(N * L_max + M)`.
    *   **Overall**: `O(M + N * L_max)`. If routes were sets for O(1) lookup, it would be `O(M + N)`.

*   **Space Complexity**: `O(M + N)`
    *   **`stop_to_routes`**: Stores mapping for all `M` stop occurrences to their respective route indices. So, `O(M)` space.
    *   **`q` (queue)**: In the worst case, could hold all `N` route indices, so `O(N)` space.
    *   **`visited_routes`**: In the worst case, could store all `N` route indices, so `O(N)` space.
    *   **`routes`**: The input list itself uses `O(M)` space.
    *   **Overall**: `O(M + N)`.

### 5. Edge Cases & Correctness

*   **`source == target`**: Correctly handled by returning `0` immediately.
*   **`source` not on any route**: Correctly handled by `if source not in stop_to_routes: return -1`.
*   **`target` not reachable**: If the BFS queue becomes empty without finding the target, it correctly returns `-1`.
*   **Single route covers `source` and `target`**: The BFS will immediately find this route, and the `target in routes[current_route_idx]` check will return `1` (one bus), which is correct.
*   **Empty `routes` list**: `stop_to_routes` would be empty. If `source` is not 0 (or a stop on an empty route), `source not in stop_to_routes` handles this. If `source` is 0, and `routes` is `[[]]`, this path still works.
*   **Disconnected graph**: Handled correctly; if `target` is in a component unreachable from `source`, BFS will terminate with an empty queue and return -1.

### 6. Improvements & Alternatives

*   **Performance for `target` check**:
    *   The `target in routes[current_route_idx]` operation takes `O(L_max)` because `routes[current_route_idx]` is a `List`.
    *   **Improvement**: If the `routes` input were pre-processed into a `List[Set[int]]` (i.e., `routes = [set(r) for r in routes]`), then the `target in route_set` check would become `O(1)` on average. The initial conversion would add `O(M)` time, but it could speed up the BFS if `L_max` is large and target checks are frequent.

*   **Readability**: The code is generally clear and well-structured, benefiting from descriptive variable names and type hints.

*   **Alternative Graph Construction**:
    *   One could explicitly build an adjacency list for the "route graph" upfront: `route_graph[route_a_idx] = [route_b_idx, route_c_idx, ...]` where `route_a_idx` shares a stop with `route_b_idx`, etc.
    *   This would involve iterating through all stops, and for each stop, creating edges between all pairs of routes that pass through it. This might be more complex to build (`O(S * (R_max)^2)`) but could make the BFS loop slightly cleaner (direct `for next_route in adj_list[current_route]`). However, the current implicit approach achieves similar performance with less explicit graph construction overhead.

### 7. Security/Performance Notes

*   No obvious security vulnerabilities as the code processes numerical inputs and doesn't interact with external systems or files.
*   Performance considerations are mainly handled by the choice of BFS and efficient data structures (`defaultdict`, `deque`, `set`). These are C-optimized in Python, which contributes to good practical performance. The use of `set` for `visited_routes` is critical; a `list` lookup would degrade performance to `O(N)` per check, leading to a much worse overall time complexity.

### Code:
```python
import collections
from typing import List, Dict

class Solution:
    def numBusesToDestination(self, routes: List[List[int]], source: int, target: int) -> int:
        if source == target:
            return 0

        stop_to_routes: Dict[int, List[int]] = collections.defaultdict(list)
        for i, route in enumerate(routes):
            for stop in route:
                stop_to_routes[stop].append(i)

        if source not in stop_to_routes:
            return -1

        q = collections.deque()
        visited_routes = set()

        for route_idx in stop_to_routes[source]:
            q.append((route_idx, 1))
            visited_routes.add(route_idx)

        while q:
            current_route_idx, buses_taken = q.popleft()

            if target in routes[current_route_idx]:
                return buses_taken

            for stop in routes[current_route_idx]:
                for next_route_idx in stop_to_routes[stop]:
                    if next_route_idx not in visited_routes:
                        visited_routes.add(next_route_idx)
                        q.append((next_route_idx, buses_taken + 1))

        return -1
```
