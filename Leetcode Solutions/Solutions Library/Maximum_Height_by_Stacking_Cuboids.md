## Maximum Height by Stacking Cuboids
**Language:** python
**Tags:** python,dynamic programming,sorting,longest increasing subsequence

### Description:
This code implements a dynamic programming approach to solve the "Maximum Height by Stacking Cuboids" problem. The core idea is to normalize cuboid orientations and then use a longest increasing subsequence (LIS) variant to find the tallest possible stack.

---

### 1. Overview & Intent

The `maxHeight` method calculates the maximum possible height of a stack of cuboids given a list of cuboid dimensions.

*   **Problem Constraint:** A cuboid `i` can be placed on top of cuboid `j` only if all dimensions of `i` are less than or equal to the corresponding dimensions of `j` (i.e., `width_i <= width_j`, `length_i <= length_j`, `height_i <= height_j`).
*   **Rotation Allowed:** Any cuboid can be rotated to use any of its three dimensions as height, and the remaining two as width and length, provided the stacking constraint is met.
*   **Goal:** Maximize the total height of the stack.

---

### 2. How It Works

The solution proceeds in three main steps:

1.  **Normalize Cuboid Dimensions (Rotation Handling):**
    *   For each cuboid `[w, l, h]`, its dimensions are sorted internally (e.g., `[min_dim, mid_dim, max_dim]`).
    *   This ensures a consistent representation regardless of original orientation. By sorting, we fix the 'smallest', 'middle', and 'largest' dimensions. The largest dimension (`cuboid[2]`) will always be considered the "height" for stacking purposes after this normalization, as it allows for the most flexible placement.

2.  **Sort All Cuboids:**
    *   The entire list of cuboids is sorted. Python's default sort for lists of lists sorts lexicographically (by the first element, then the second, then the third).
    *   This global sorting is critical for the dynamic programming step: it ensures that when we consider `cuboids[i]`, any cuboid `cuboids[j]` (where `j < i`) that *could* potentially be placed underneath `cuboids[i]` will have already appeared in the sorted list and its optimal stack height `dp[j]` will have been computed.

3.  **Dynamic Programming (LIS Variant):**
    *   An array `dp` of size `n` (number of cuboids) is initialized. `dp[i]` stores the maximum height of a stack where `cuboids[i]` is the topmost cuboid.
    *   The algorithm iterates through each cuboid `cuboids[i]` (from `i=0` to `n-1`):
        *   `dp[i]` is initially set to the height of `cuboids[i]` itself (which is `cuboids[i][2]` after sorting its dimensions). This represents a stack with just one cuboid.
        *   It then checks all preceding cuboids `cuboids[j]` (where `j < i`):
            *   If `cuboids[j]` can be placed underneath `cuboids[i]` (i.e., `cuboids[j][0] <= cuboids[i][0]`, `cuboids[j][1] <= cuboids[i][1]`, and `cuboids[j][2] <= cuboids[i][2]`), then `cuboids[i]` can potentially extend the stack ending at `cuboids[j]`.
            *   `dp[i]` is updated to `max(dp[i], dp[j] + cuboids[i][2])`. This means taking the maximum height achieved by placing `cuboids[i]` on top of the best possible stack ending with `cuboids[j]`.
    *   A `max_total_height` variable tracks the overall maximum height found across all `dp[i]` values.

---

### 3. Key Design Decisions

*   **Sorting Internal Dimensions:** This is the most crucial step for handling the "rotation" constraint. By sorting dimensions (e.g., always `[min, mid, max]`), a consistent comparison is possible. The largest dimension `max_dim` becomes the fixed "height" for stacking. This simplifies the 3D stacking check from considering 6 permutations per cuboid to a single canonical form.
*   **Sorting All Cuboids Lexicographically:** This provides the necessary ordering for the dynamic programming approach. If `cuboid_j` can stack under `cuboid_i`, then `cuboid_j` will either be identical to `cuboid_i` or appear earlier in the sorted list. This guarantees that `dp[j]` (the optimal stack ending with `cuboid_j`) is computed before `dp[i]`.
*   **Dynamic Programming (Longest Increasing Subsequence variant):** This is a standard pattern for problems involving finding the longest/tallest sequence of items that adhere to a specific ordering constraint. `dp[i]` effectively stores the maximum height of a "subsequence" ending at `cuboids[i]`. The 3D stacking rule replaces the simple 1D comparison of a typical LIS problem.

---

### 4. Complexity

*   **Time Complexity:**
    *   **Normalizing Cuboids:** `N` cuboids, each sorted in `O(3 log 3)` time. Since `3` is a constant, this is `O(N * 1) = O(N)`.
    *   **Sorting All Cuboids:** `N` cuboids, each comparison takes `O(3)` time (comparing 3 dimensions). So, `O(N log N * 3) = O(N log N)`.
    *   **Dynamic Programming:** Two nested loops, each iterating up to `N` times. Inside the inner loop, operations are constant time (dimension comparison, `max`). So, `O(N * N) = O(N^2)`.
    *   **Overall Time Complexity:** `O(N) + O(N log N) + O(N^2) = O(N^2)`.

*   **Space Complexity:**
    *   **Input `cuboids`:** Modified in-place, but still stores `N` lists of 3 integers. `O(N * 3) = O(N)`.
    *   **`dp` array:** Stores `N` integers. `O(N)`.
    *   **Overall Space Complexity:** `O(N)`.

---

### 5. Edge Cases & Correctness

*   **Empty Input (`n = 0`):** Handled correctly by `if n == 0: return 0`.
*   **Single Cuboid (`n = 1`):**
    *   Dimensions are sorted.
    *   `dp[0]` is initialized to `cuboids[0][2]`.
    *   `max_total_height` is updated to `cuboids[0][2]`.
    *   The loops complete, and the correct height of the single cuboid is returned.
*   **Cuboids with Identical Dimensions:** The sorting of all cuboids (lexicographical) will group identical cuboids together. The stacking rule `prev_cuboid[k] <= current_cuboid[k]` allows identical cuboids to be stacked on each other. The DP correctly calculates `dp[j] + current_cuboid[2]` for such cases, allowing a stack of identical cuboids.
*   **No Cuboids Can Be Stacked:** Each `dp[i]` will remain initialized to `cuboids[i][2]` (its own height). `max_total_height` will correctly return the maximum height among all individual cuboids.
*   **Zero or Negative Dimensions:** The problem constraints typically specify positive dimensions. If zero dimensions were allowed, the logic would still hold (a `[0,0,0]` cuboid could only stack on another `[0,0,0]` cuboid), but typically it's assumed dimensions are positive.

---

### 6. Improvements & Alternatives

*   **Readability:**
    *   The code is quite clear, but adding comments to explain the `dp[i]` state definition more explicitly and the conditions for updating it could further enhance clarity, especially for someone new to DP.
    *   Using named tuples or a small class for `Cuboid` (with `width`, `length`, `height` attributes) instead of raw lists `[0], [1], [2]` could improve readability, though it would add some overhead.

*   **Performance:**
    *   The `O(N^2)` time complexity is generally considered optimal for this type of 3D LIS-like problem. Unlike 1D LIS which can be optimized to `O(N log N)` using binary search, the 3D comparison prevents such a straightforward optimization.
    *   For extremely large `N` (e.g., `N > 5000`), an `O(N^2)` solution might become too slow. However, given typical competitive programming constraints for such problems, `N` is usually within the `O(N^2)` limit.

*   **Alternatives:**
    *   **Memoized Recursion:** An alternative implementation for dynamic programming is memoized recursion. This would yield the same time and space complexity but might be conceptually easier for some to read as it directly translates the recursive definition of the problem.
    *   **Graph Approach:** The problem can be modeled as finding the longest path in a Directed Acyclic Graph (DAG). Each cuboid is a node, and a directed edge exists from `j` to `i` if `cuboids[j]` can be placed under `cuboids[i]`. The weight of the edge could be `cuboids[i][2]`. Finding the longest path in a DAG is solvable using topological sort and dynamic programming, which is fundamentally what the current solution does with its pre-sorting.

---

### 7. Security/Performance Notes

*   **Security:** There are no inherent security vulnerabilities in this algorithm. It's a pure computational logic without external interactions, user input processing (beyond the `List[List[int]]` structure), or memory allocation beyond standard data structures.
*   **Performance:** The `O(N^2)` complexity means that for `N` up to a few thousand, the solution is efficient enough. For example, if `N = 2000`, `N^2 = 4 * 10^6` operations, which is typically well within a few seconds. However, if `N` were much larger (e.g., `10^5`), `N^2` would be `10^{10}`, making the solution too slow, requiring a different algorithmic approach, likely not possible given the 3D stacking constraint. Memory usage is linear, `O(N)`, which is also efficient.

### Code:
```python
class Solution:
    def maxHeight(self, cuboids: List[List[int]]) -> int:
        # Step 1: Normalize each cuboid's dimensions.
        # The problem states "You can rearrange any cuboid's dimensions by rotating it"
        # and "place cuboid i on cuboid j if width_i <= width_j and length_i <= length_j and height_i <= height_j".
        # This implies that if cuboid i can be placed on cuboid j, then its smallest dimension
        # must be less than or equal to j's smallest, its middle <= j's middle, and its largest <= j's largest.
        # Therefore, for consistent comparison, we sort the dimensions of each cuboid internally.
        for cuboid in cuboids:
            cuboid.sort()
        
        # Step 2: Sort all cuboids.
        # Sort the cuboids based on their dimensions. The default sort for lists of lists
        # sorts primarily by the first element, then by the second, then by the third.
        # This sorting order is crucial for the dynamic programming approach, ensuring that
        # if cuboid_j can be placed under cuboid_i, cuboid_j will appear before cuboid_i
        # in the sorted list (or have identical dimensions).
        cuboids.sort()
        
        n = len(cuboids)
        if n == 0:
            return 0
            
        # dp[i] will store the maximum height of a stack ending with cuboids[i] as the top-most cuboid.
        dp = [0] * n
        
        max_total_height = 0
        
        # Step 3: Dynamic Programming (Longest Increasing Subsequence variant).
        # Iterate through each cuboid in the sorted list.
        for i in range(n):
            current_cuboid = cuboids[i]
            # Initialize dp[i] with the height of the current cuboid itself.
            # After sorting its dimensions, the largest dimension (cuboid[2]) will be its height.
            dp[i] = current_cuboid[2]
            
            # Check all previous cuboids (those with index j < i) to see if they can be placed under the current one.
            for j in range(i):
                prev_cuboid = cuboids[j]
                
                # Check if prev_cuboid can be placed under current_cuboid.
                # This means prev_cuboid's dimensions must be less than or equal to current_cuboid's dimensions
                # in all three corresponding dimensions (smallest, middle, largest).
                if (prev_cuboid[0] <= current_cuboid[0] and
                    prev_cuboid[1] <= current_cuboid[1] and
                    prev_cuboid[2] <= current_cuboid[2]):
                    
                    # If it can, update dp[i] with the maximum height achieved by stacking
                    # prev_cuboid (and its optimal stack) under current_cuboid.
                    dp[i] = max(dp[i], dp[j] + current_cuboid[2])
            
            # Update the overall maximum height found so far.
            max_total_height = max(max_total_height, dp[i])
            
        return max_total_height
```
