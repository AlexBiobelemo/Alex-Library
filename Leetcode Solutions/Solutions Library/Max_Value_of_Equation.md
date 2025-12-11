## Max Value of Equation
**Language:** python
**Tags:** python,oop,monotonic queue,deque,sliding window

### Description:
This code solves a classic problem that involves finding the maximum value of an equation over a sliding window, leveraging a monotonic deque for efficient lookups.

---

### 1. Overview & Intent

This Python code defines a method `findMaxValueOfEquation` within a `Solution` class. Its primary purpose is to find the maximum possible value of the expression `yi + yj + |xi - xj|` given a list of `points` (where each point is `[x, y]`) and an integer `k`. The constraints for selecting two points `(xi, yi)` and `(xj, yj)` are:
1.  `xi < xj` (the problem statement usually implies this order, or points are sorted by x).
2.  `|xi - xj| <= k`.

The code transforms the equation into `(yi - xi) + (yj + xj)` (since `xi < xj`, `|xi - xj|` becomes `xj - xi`) to optimize finding the maximum value for `(yi - xi)` within a valid `x`-range.

### 2. How It Works

The algorithm processes the `points` list, which is assumed to be sorted by `x`-coordinates. It uses a `collections.deque` (double-ended queue) to efficiently maintain candidate `(yi - xi)` values.

1.  **Initialization**: `max_val` is initialized to negative infinity, and an empty `deque` is created. The `deque` stores pairs `[y - x, x]`.
2.  **Iterate Through Points**: The code iterates through each point `(xj, yj)` in the input `points` list.
3.  **Window Maintenance (Pruning from Front)**: For each `(xj, yj)`, it first removes points `(xi, yi)` from the *front* of the `deque` that are no longer valid. A point `(xi, yi)` is invalid if its `x`-coordinate `xi` is too far to the left, specifically if `xj - xi > k` (or `xi < xj - k`). This maintains the `|xi - xj| <= k` constraint.
4.  **Calculate Maximum Value**: If the `deque` is not empty after pruning, the point at the *front* (`deque[0]`) holds the largest `(yi - xi)` value among all valid `xi`s. The equation `(yi - xi) + (yj + xj)` is calculated using `deque[0][0]` (which is `yi - xi`) and the current point's `yj + xj`. `max_val` is updated if this new value is greater.
5.  **Monotonic Deque Update (Pruning from Back)**: The current point `(xj, yj)`'s `[yj - xj, xj]` pair is considered for addition to the `deque`. Before adding, any points `(xp, yp)` at the *back* of the `deque` that have `(yp - xp)` values less than or equal to `(yj - xj)` are removed. This ensures the `deque` remains monotonically decreasing with respect to `(y - x)` values. If an existing point `(xp, yp)` has a smaller or equal `(y - x)` value but a smaller `x` (because it was added earlier), it's inferior to the current `(xj, yj)` because `(yj - xj)` is either better or equal, and `xj` is larger, meaning it stays valid for future `xj`s for longer.
6.  **Add Current Point**: The current point's `[yj - xj, xj]` is added to the back of the `deque`.
7.  **Return Value**: After iterating through all points, `max_val` holds the maximum equation value found.

### 3. Key Design Decisions

*   **Equation Transformation**: The core insight is transforming `yi + yj + |xi - xj|` into `(yi - xi) + (yj + xj)`. Since `points` are sorted by `x`, `xi < xj` is implicitly handled, making `|xi - xj| = xj - xi`. This separates the terms dependent on `xi` and `xj`, allowing us to optimize for `(yi - xi)`.
*   **Monotonic Deque (Sliding Window Maximum)**: A `collections.deque` is used to efficiently find the maximum `(yi - xi)` within the sliding window `[xj - k, xj]`. The deque maintains elements in decreasing order of `(y - x)`, so the maximum is always at the front.
*   **Storing `[y - x, x]`**: Storing `x` along with `y - x` in the deque is crucial for efficiently checking the `x`-coordinate constraint (`xi < xj - k`) when pruning from the front.

### 4. Complexity

*   **Time Complexity**: O(N)
    *   Each point `(x, y)` is processed exactly once in the main loop.
    *   Each point is added to the deque at most once.
    *   Each point is removed from the deque (either from the front due to window constraint or from the back due to being inferior) at most once.
    *   Deque operations (`append`, `popleft`, `pop`) are O(1).
    *   Therefore, the total time complexity is linear, O(N), where N is the number of points.
*   **Space Complexity**: O(N)
    *   In the worst case (e.g., if all `(y - x)` values are strictly increasing, or `k` is very large), all points could be stored in the `deque` simultaneously.
    *   Therefore, the space complexity is linear, O(N).

### 5. Edge Cases & Correctness

*   **Empty `points` list**: The code would return `-float('inf')` and then attempt `int(-float('inf'))`, which raises an `OverflowError`. A production system would likely handle this by returning a specific value (e.g., 0) or raising a more descriptive error if `N < 2` is allowed.
*   **No valid pair found**: If `k` is too small, `max_val` might remain `-float('inf')`. Similar to the empty list case, `int()` on infinity is problematic.
*   **`k=0`**: If `k=0`, then `|xi - xj| = 0`, implying `xi = xj`. However, the problem constraint is typically `xi < xj`. If `xi = xj` were allowed, `xj - xi` would be 0. The current logic correctly handles `xi < xj` as points are iterated, ensuring `xj - xi > 0`.
*   **Correctness of `deque` pruning**:
    *   **Front pruning (`deque[0][1] < xj - k`)**: Correctly removes points outside the `x`-coordinate window `[xj - k, xj]`.
    *   **Back pruning (`deque[-1][0] <= yj - xj`)**: Correctly maintains the monotonic decreasing property. If `yj - xj` is greater, the current point is strictly better. If `yj - xj` is equal, the current point `xj` is larger than the `x` of the point being popped, meaning the current point extends the valid window further to the right, making the older point inferior for future calculations.

### 6. Improvements & Alternatives

*   **Error Handling for `max_val`**: If the problem constraints allow for cases where no valid pair exists (e.g., `points` is too small, or `k` is too restrictive), explicitly check if `max_val` is still `-float('inf')` before converting to `int`. For example, return `0` or throw a custom exception if no solution is found.
*   **Assumptions Clarification**: The problem implicitly assumes `points` is sorted by `x`-coordinate. If not, an initial `points.sort()` would be necessary, adding `O(N log N)` to the time complexity. The current code structure benefits from this pre-sorted nature.
*   **Comments**: The existing comments are excellent and explain the logic clearly.

### 7. Security/Performance Notes

*   **Performance**: The solution is optimally performant with O(N) time complexity, which is the best achievable as every point must be considered at least once. Deque operations are inherently efficient.
*   **Security**: No specific security vulnerabilities are present in this algorithm, as it deals purely with numerical computation and data structure manipulation without external input interaction or complex system calls.

### Code:
```python
import collections
from typing import List

class Solution:
    def findMaxValueOfEquation(self, points: List[List[int]], k: int) -> int:
        max_val = -float('inf')
 
        deque = collections.deque()

        for xj, yj in points:
    
            while deque and deque[0][1] < xj - k:
                deque.popleft()

 
            if deque:
  
                max_val = max(max_val, deque[0][0] + yj + xj)

            while deque and deque[-1][0] <= yj - xj:
                deque.pop()
            deque.append([yj - xj, xj])

        return int(max_val)
```
