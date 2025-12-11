## Shortest Path to Get All Keys
**Language:** python
**Tags:** python,bfs,bitmask,shortest path

### Description:
This code implements a Breadth-First Search (BFS) algorithm to find the shortest path to collect all keys in a 2D grid. The problem is a variation of a shortest path problem on a graph where the "state" of a node includes not just its coordinates but also the set of keys collected so far.

---

### 1. Overview & Intent

The primary goal of the `shortestPathAllKeys` method is to determine the minimum number of moves required to start at a specified position (`@`), traverse a grid, collect all available keys (`a` through `f`), and reach any location having collected all keys. The grid can contain:
*   `'.'` - Empty space.
*   `'#'` - Wall (impassable).
*   `'@'` - Starting position.
*   `'a'` to `'f'` - Keys.
*   `'A'` to `'F'` - Locks, which can only be passed if the corresponding lowercase key has been collected.

The solution aims for the shortest path, hence the use of BFS which guarantees optimality on an unweighted graph.

---

### 2. How It Works

The algorithm proceeds as follows:

1.  **Initialization:**
    *   It first determines the grid dimensions (`m`, `n`).
    *   It scans the grid to find the starting position (`start_r`, `start_c`) and counts the total number of *distinct* keys (`total_keys`). The `total_keys` is inferred by finding the highest key character (e.g., if 'c' is present, `total_keys` is 3, implying 'a', 'b', 'c' must be collected).
    *   A `target_keys_mask` is calculated. This is a bitmask representing the state where all `total_keys` have been collected (e.g., `111` for 3 keys).
    *   A `deque` (`q`) is initialized for BFS, storing tuples `(row, col, keys_mask, moves)`. The initial state is `(start_r, start_c, 0, 0)`.
    *   A `set` (`visited`) stores `(row, col, keys_mask)` tuples to prevent revisiting states and cycles.
    *   `dirs` defines the four possible movement directions.

2.  **BFS Traversal:**
    *   The algorithm enters a `while q:` loop, processing states one by one.
    *   In each iteration, it `popleft()`s a state `(r, c, keys_mask, moves)`.
    *   **Goal Check:** If the current `keys_mask` equals `target_keys_mask`, it means all keys have been collected, and `moves` is the shortest path, so it returns `moves`.
    *   **Explore Neighbors:** For each of the four possible directions:
        *   Calculate the new coordinates `(nr, nc)`.
        *   **Boundary Check:** Ensure `(nr, nc)` is within grid boundaries.
        *   **Wall Check:** If `grid[nr][nc]` is `'#'`, it's a wall, so skip.
        *   **Lock Check:** If `grid[nr][nc]` is an uppercase letter (a lock):
            *   Calculate the corresponding `lock_bit` (e.g., 'A' -> bit 0, 'B' -> bit 1).
            *   If the current `keys_mask` does *not* have this `lock_bit` set (meaning the key for this lock hasn't been collected), skip this path.
        *   **Key Collection:** If `grid[nr][nc]` is a lowercase letter (a key):
            *   Calculate the corresponding `key_bit`.
            *   Update `new_keys_mask` by setting this `key_bit` (using bitwise OR).
        *   **Visited Check & Enqueue:** If the new state `(nr, nc, new_keys_mask)` has not been visited before:
            *   Add it to `visited`.
            *   Add it to the `q` with an incremented `moves` count (`moves + 1`).

3.  **No Path Found:**
    *   If the `while q:` loop finishes without `target_keys_mask` ever being reached, it means it's impossible to collect all keys, so the method returns `-1`.

---

### 3. Key Design Decisions

*   **Breadth-First Search (BFS):** Chosen because it guarantees the shortest path in an unweighted graph, which is exactly what's needed for "minimum number of moves."
*   **State Representation:** The most critical design decision is representing the state not just by `(row, col)` but by `(row, col, keys_mask)`.
    *   `keys_mask`: An integer used as a bitmask to efficiently track which keys have been collected. Each bit corresponds to a unique key (e.g., bit 0 for 'a', bit 1 for 'b', etc.). This allows for:
        *   Compact storage (a single integer).
        *   O(1) time complexity for adding/checking keys (bitwise OR and AND operations).
*   **`visited` Set:** Essential for preventing infinite loops in grids with cycles and for ensuring optimality by not re-exploring paths to an already-visited state (position + keys collected) if a shorter path to that state has already been found.
*   **`total_keys` Calculation:** The method cleverly determines the number of keys `k` by finding the maximum key character present. This assumes keys are contiguous (e.g., if 'c' is present, 'a' and 'b' are implicitly relevant keys to collect). This allows dynamically building the `target_keys_mask`.

---

### 4. Complexity

Let `M` be the number of rows, `N` be the number of columns, and `K` be the total number of distinct keys in the grid (typically up to 6 in such problems, corresponding to 'a' through 'f').

*   **Time Complexity:**
    *   The total number of possible states is `M * N` (grid positions) * `2^K` (possible key combinations).
    *   Each state is enqueued and dequeued at most once.
    *   Processing each state involves checking up to 4 neighbors, performing boundary checks, character comparisons, and bitwise operations, all of which are O(1) operations.
    *   Therefore, the overall time complexity is **O(M * N * 2^K)**.

*   **Space Complexity:**
    *   The `visited` set stores `(row, col, keys_mask)` for each unique state encountered.
    *   The `deque` `q` can, in the worst case, hold a significant portion of the possible states.
    *   Thus, the space complexity is also **O(M * N * 2^K)**.

---

### 5. Edge Cases & Correctness

*   **No Keys in Grid:**
    *   `total_keys` will be 0, `target_keys_mask` will be `(1 << 0) - 1 = 0`.
    *   The initial `keys_mask` is 0.
    *   The very first `if keys_mask == target_keys_mask:` check will evaluate to `0 == 0`, returning `0` moves. This is correct as no keys need collecting.
*   **Start Position is a Key:**
    *   The initial `keys_mask` is 0. If the starting cell contains a key, that key is only "collected" when moving *to* that cell. The `new_keys_mask` logic handles this: if `grid[nr][nc]` is a key, `new_keys_mask` is updated *after* determining the move to `(nr, nc)`. So, to collect a key at `@`, one must move to an adjacent cell, then conceptually move *back* to the key (or just move to an adjacent cell if the key isn't at `@`), making the collection cost at least 1 move. The logic correctly counts moves between cells.
*   **Unreachable Keys or Locks:**
    *   If a path to all keys is blocked by walls, unreachable locks, or an isolated segment of the grid, the BFS will exhaust the queue without ever reaching `target_keys_mask`. In this scenario, the algorithm correctly returns `-1`.
*   **Grid Size 1x1:**
    *   Handles correctly. If `@` is the only character, `total_keys` is 0, `target_keys_mask` is 0, and `0` moves are returned. If `@` is a key, still 0 moves. The boundary checks `(0 <= nr < m and 0 <= nc < n)` prevent out-of-bounds access.

---

### 6. Improvements & Alternatives

*   **Heuristics (A\* Search):** For very large grids where the number of keys `K` is small, A\* search could potentially improve average-case performance by guiding the search with a heuristic function (e.g., sum of Manhattan distances to uncollected keys). However, designing an admissible and consistent heuristic for "collect all keys" is complex because the state (keys collected) changes, making standard distance heuristics insufficient.
*   **Minor Readability/Performance (Bitwise operations):**
    *   The `ord(cell.lower()) - ord('a')` for locks could be slightly optimized to `ord(cell) - ord('A')` directly, assuming ASCII uppercase chars are consecutive and map directly to keys, avoiding the `lower()` string method call. This is a very minor micro-optimization.
*   **Pre-computing Key/Lock Mappings:** If the key characters were arbitrary (not 'a' through 'f'), a dictionary could map them to bit positions. For fixed 'a'-'f', the `ord()` approach is perfectly fine.
*   **Input Validation:** For a production system, adding checks for an empty grid, irregular row lengths, multiple start points, or invalid characters would make the function more robust.

---

### 7. Security/Performance Notes

*   **Performance Bottleneck:** The primary performance limitation is the `2^K` factor, due to the exponential growth of states with the number of keys. For `K > ~8-10`, this approach becomes computationally infeasible on typical contest time limits. However, for the usual constraints (K <= 6), it is efficient enough.
*   **No Security Concerns:** This algorithm operates purely on in-memory data structures and does not interact with external systems (files, network, user input, etc.). Therefore, there are no inherent security vulnerabilities in the provided code itself.

### Code:
```python
import collections
from typing import List

class Solution:
    def shortestPathAllKeys(self, grid: List[str]) -> int:
        m, n = len(grid), len(grid[0])

        start_r, start_c = -1, -1
        total_keys = 0 # Stores the count of distinct keys (k from problem statement)

        # Find starting position and determine the total number of keys (k)
        for r in range(m):
            for c in range(n):
                if grid[r][c] == '@':
                    start_r, start_c = r, c
                elif grid[r][c].islower():
                    # The problem states "first k letters". So if 'c' is a key, 'a' and 'b' must also be keys.
                    # We can determine k by finding the largest key character.
                    # 'a' corresponds to bit 0, 'b' to bit 1, etc.
                    # total_keys will be 1 for 'a', 2 for 'b', etc.
                    total_keys = max(total_keys, ord(grid[r][c]) - ord('a') + 1)
        
        # The target mask represents having collected all 'k' keys.
        # e.g., if total_keys = 3 (keys 'a', 'b', 'c'), target_keys_mask = 111 (binary) = 7
        target_keys_mask = (1 << total_keys) - 1

        # BFS queue: (row, col, keys_mask, moves)
        q = collections.deque([(start_r, start_c, 0, 0)])
        
        # Visited set: (row, col, keys_mask) to prevent cycles and redundant computations
        visited = set([(start_r, start_c, 0)])

        # Directions for movement: (dr, dc)
        dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)]

        while q:
            r, c, keys_mask, moves = q.popleft()

            # If all keys are collected, return the number of moves
            if keys_mask == target_keys_mask:
                return moves

            # Explore neighbors
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc

                # Check boundary conditions
                if not (0 <= nr < m and 0 <= nc < n):
                    continue
                
                cell = grid[nr][nc]

                # If it's a wall, skip
                if cell == '#':
                    continue

                # If it's a lock
                if cell.isupper():
                    # Calculate the bit for this lock (e.g., 'A' -> bit 0, 'B' -> bit 1)
                    lock_bit = 1 << (ord(cell.lower()) - ord('a'))
                    # If we don't have the corresponding key, skip
                    if (keys_mask & lock_bit) == 0:
                        continue
                
                # Calculate new_keys_mask for the next state
                new_keys_mask = keys_mask
                # If it's a key
                if cell.islower():
                    # Calculate the bit for this key
                    key_bit = 1 << (ord(cell) - ord('a'))
                    # Add this key to our collection
                    new_keys_mask |= key_bit
                
                # If this state (new position and new keys_mask) has not been visited
                if (nr, nc, new_keys_mask) not in visited:
                    visited.add((nr, nc, new_keys_mask))
                    q.append((nr, nc, new_keys_mask, moves + 1))
        
        # If the queue becomes empty and we haven't found all keys, it's impossible
        return -1
```
