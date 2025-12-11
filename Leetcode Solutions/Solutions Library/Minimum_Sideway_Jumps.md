## Minimum Sideway Jumps
**Language:** python
**Tags:** python,dynamic programming,list,object-oriented programming

### Description:
This code solves the "Minimum Side Jumps" problem using dynamic programming. It determines the minimum number of side jumps a frog needs to make to reach the end of a path, given a series of obstacles.

---

### 1. Overview & Intent

*   **Problem:** A frog starts at a specific lane (lane 2) on a 3-lane path. It needs to traverse a path of `n` points. At each point, there might be obstacles on one or more lanes. The frog can move forward one point on its current lane, or perform a "side jump" to another lane at the *same point* if that lane is not blocked.
*   **Goal:** Find the minimum total number of side jumps required to reach the end of the path (point `n`).
*   **Approach:** Dynamic Programming is used to calculate the minimum jumps to reach each lane at each point along the path.

---

### 2. How It Works

The algorithm uses dynamic programming to track the minimum jumps.

1.  **State Initialization:**
    *   `dp` array stores the minimum jumps needed to reach the *current point* on each of the three lanes.
    *   Lanes are 0-indexed (0, 1, 2) corresponding to problem's 1-indexed lanes (1, 2, 3).
    *   At the starting point (point 0), the frog is on lane 2 (index 1) with 0 jumps. To be on lane 1 (index 0) or lane 3 (index 2) at point 0, it implicitly requires 1 side jump.
    *   `dp = [1, 0, 1]` reflects this initial state.

2.  **Iterating Through Points:**
    *   The code iterates from `i = 1` (the first point after the start) up to `n` (the end point).
    *   For each point `i`, a `new_dp` array is created, initialized with `float('inf')` to represent unreachable states.

3.  **Step 1: Moving Forward & Applying Obstacles (Direct Progression):**
    *   For each lane `j` (0, 1, 2):
        *   If `obstacles[i]` (the obstacle at the current point `i`) is on lane `j+1`, then `new_dp[j]` is set to `float('inf')` because that lane is blocked.
        *   Otherwise, the frog can move directly forward from point `i-1` on lane `j` to point `i` on lane `j`. The cost (`new_dp[j]`) is simply inherited from the cost to reach lane `j` at point `i-1` (`dp[j]`).

4.  **Step 2: Performing Side Jumps (Cost Propagation):**
    *   This is the critical part for updating minimum jumps via side jumps *at the current point `i`*.
    *   The logic runs for two passes (`for _ in range(2)`). This is a simplified form of Bellman-Ford relaxation for a graph with 3 nodes (the 3 lanes) where edge weights are 1 (one jump). Two passes ensure that all possible jump combinations (e.g., L1 -> L2 -> L3, which takes 2 jumps) are considered and propagated correctly.
    *   In each pass, for every target lane `j`:
        *   If lane `j` is not blocked at point `i`:
            *   It considers jumping from any other lane `k` (where `k != j`).
            *   If lane `k` is also not blocked at point `i`:
                *   It updates `new_dp[j]` by taking the minimum of its current value and the cost to reach lane `k` (`new_dp[k]`) plus one jump (`+ 1`). This allows a frog to jump from `k` to `j`.

5.  **Update `dp`:**
    *   After processing both direct progression and all possible side jumps for point `i`, `dp` is updated to `new_dp` to prepare for the next point `i+1`.

6.  **Final Result:**
    *   After iterating through all points up to `n`, the `dp` array holds the minimum jumps to reach each lane at point `n`. The overall minimum is found by `min(dp)`.

---

### 3. Key Design Decisions

*   **Dynamic Programming:** The problem exhibits optimal substructure (the optimal path to `i` depends on optimal paths to `i-1`) and overlapping subproblems (calculating min jumps to different lanes at point `i` reuses min jump costs from `i-1`). DP is a natural fit.
*   **State Definition `dp[j]`:** A concise state that captures the minimum cost (jumps) to be on lane `j` at the current point. This allows for simple transitions.
*   **Space Optimization:** Only two `dp` arrays (`dp` for `i-1` and `new_dp` for `i`) are needed at any given time. This keeps space complexity constant.
*   **Lane Indexing:** Using 0-indexed lanes internally (`0, 1, 2`) simplifies array access, with a clear conversion from the problem's 1-indexed obstacles (`obstacles[i] == j + 1`).
*   **Two Passes for Side Jumps:** This is crucial for correctness. For 3 lanes, a single pass might not fully propagate costs (e.g., if lane 1 updates from lane 2, and lane 2 updates from lane 3, lane 1 won't "see" the cost from lane 3 in a single pass if the updates happen in a specific order). Two passes guarantee all direct and indirect (e.g., L1->L2->L3 or L3->L2->L1) jump costs are propagated, similar to `V-1` relaxations in Bellman-Ford for `V` nodes.
*   **`float('inf')` for Blocked/Unreachable States:** A standard way to represent impossibly high costs in pathfinding algorithms.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The main loop iterates `N` times (from point 1 to point `n`).
    *   Inside the loop:
        *   The first inner loop for direct progression runs 3 times (constant).
        *   The nested loops for side jumps run `2 * 3 * 3 = 18` times (constant).
    *   Since all operations inside the main loop are constant time, the overall time complexity is linear with respect to `N`, the number of points.
*   **Space Complexity: O(1)**
    *   The `dp` and `new_dp` arrays each store only 3 integer values.
    *   This storage is constant regardless of the input size `N`.

---

### 5. Edge Cases & Correctness

*   **Starting Position:** The frog starts at lane 2 (index 1) with 0 jumps. `dp = [1, 0, 1]` correctly models this: 0 jumps to be on lane 2, 1 jump to *start* on lane 1 or 3 (if necessary).
*   **Obstacles at Point 0:** The problem implicitly states obstacles array is of length `n+1`, so `obstacles[0]` usually refers to the starting point which is handled by initial `dp`. The loop starts from `i=1`.
*   **Obstacles at Current Point:** `if obstacles[i] == j + 1:` correctly blocks paths, setting `new_dp[j]` to infinity.
*   **No Obstacles:** If `obstacles` contains only `0`s, `new_dp` will be updated from `dp` without `inf`s, and `min(dp)` will correctly return 0.
*   **All Lanes Blocked at a Point:** If, hypothetically, all three lanes were blocked at point `i`, then `new_dp` for that point would become `[inf, inf, inf]`. If this persists to `n`, the final `min(dp)` would be `float('inf')`, correctly indicating an impossible path. (However, typical problem constraints prevent all lanes from being blocked at once).
*   **Correctness of Two Passes:** As discussed, for a 3-node graph with unit edge weights (representing a jump), two passes are sufficient to find the shortest path between any two nodes. This ensures that a jump from L1 to L3 (via L2) is properly accounted for, as it requires two side jumps.

---

### 6. Improvements & Alternatives

*   **Readability (Constants):**
    *   Define constants for lane indices (`LANE1 = 0`, `LANE2 = 1`, `LANE3 = 2`). This maps more clearly to the problem statement's 1-indexed lanes and avoids "magic numbers."
    *   Example: `dp = [1, 0, 1]` could be `dp = {LANE1: 1, LANE2: 0, LANE3: 1}` if using a dictionary, or simply comment `dp[LANE1] = 1, dp[LANE2]=0, ...`.
*   **Minor Optimization (Side Jumps):** While the two passes are correct, for only 3 lanes, one could also explicitly compute `min(dp[0]+1, dp[1]+1, dp[2]+1)` for each target lane, assuming no obstacles. Then, update based on obstacles. However, the current approach is generalized and quite robust.
*   **Code Structure:** The current structure is clear, separating forward movement from side jumps. No major structural changes are needed.

---

### 7. Security/Performance Notes

*   **Security:** This code has no direct security implications as it's a purely algorithmic calculation without external input handling, file operations, or network communication.
*   **Performance:** The solution is optimal in terms of both time complexity `O(N)` and space complexity `O(1)`. No further significant performance gains can be achieved for this problem using a dynamic programming approach. The constant factor is very small.

### Code:
```python
class Solution:
    def minSideJumps(self, obstacles: List[int]) -> int:
        # dp[j] represents the minimum side jumps to reach the current point on lane j.
        # Lanes are 1-indexed in problem, but we use 0-indexed for convenience:
        # 0 for lane 1, 1 for lane 2, 2 for lane 3.

        # Initial state at point 0:
        # The frog starts at point 0 in the second lane (index 1) with 0 side jumps.
        # To be on lane 1 (index 0) or lane 3 (index 2) at point 0, it requires 1 side jump.
        # This initial state correctly sets the cost to "be on" each lane at point 0.
        dp = [1, 0, 1] 

        n = len(obstacles) - 1

        # Iterate through points from 1 to n
        for i in range(1, n + 1):
            # Create a new dp array for the current point i, initialized to infinity
            # This will store the minimum jumps to reach point i on each lane.
            new_dp = [float('inf')] * 3

            # Step 1: Consider moving forward from point i-1 to point i on the same lane.
            # Also, apply obstacles at point i.
            for j in range(3): # j is 0-indexed lane (0, 1, 2)
                if obstacles[i] == j + 1: # If lane j+1 has an obstacle at point i
                    new_dp[j] = float('inf') # This lane is blocked, cannot be reached directly
                else:
                    # If lane j is not blocked, the frog can move forward from point i-1 on lane j
                    # The cost is the same as reaching point i-1 on lane j.
                    new_dp[j] = dp[j]

            # Step 2: Consider side jumps at point i.
            # A side jump can happen from any unblocked lane to any other unblocked lane at the same point i.
            # Since a frog can jump between any two lanes (not just adjacent),
            # we need to propagate the minimum cost across all unblocked lanes.
            # Two passes are sufficient for 3 lanes to ensure all possible jumps are considered
            # (similar to a simplified Bellman-Ford algorithm for a graph with 3 nodes).
            for _ in range(2): 
                for j in range(3): # For each target lane j
                    # If lane j is not blocked at point i (i.e., it's a valid target for a jump)
                    if obstacles[i] != j + 1: 
                        # Try jumping from other lanes k (k != j)
                        for k in range(3): # For each potential source lane k
                            # If k is a different lane and also not blocked at point i (i.e., a valid source for a jump)
                            if j != k and obstacles[i] != k + 1:
                                # Update new_dp[j] if jumping from lane k + 1 jump is cheaper
                                new_dp[j] = min(new_dp[j], new_dp[k] + 1)
            
            # Update dp for the next iteration (to point i+1)
            dp = new_dp

        # The minimum side jumps to reach any lane at point n is the minimum value in the final dp array.
        return min(dp)

```
