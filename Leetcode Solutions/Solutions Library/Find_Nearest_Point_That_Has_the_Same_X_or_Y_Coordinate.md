## Find Nearest Point That Has the Same X or Y Coordinate
**Language:** python
**Tags:** python,oop,manhattan distance,brute-force

### Description:
This code snippet defines a method to find the nearest "valid" point to a given reference point `(x, y)` from a list of `points`.

---

### 1. Overview & Intent

The `nearestValidPoint` function aims to identify a point from a provided list `points` that meets two criteria:
1.  **Validity**: The point must share either the same x-coordinate (`px == x`) or the same y-coordinate (`py == y`) with the reference point `(x, y)`.
2.  **Proximity**: Among all valid points, it must be the closest to `(x, y)` based on Manhattan distance.

If multiple valid points are equally close, the function returns the index of the one that appeared earliest (has the smallest index) in the input `points` list. If no valid points are found, it returns -1.

---

### 2. How It Works

The function operates by:

*   **Initialization**: Setting `min_distance` to positive infinity and `min_index` to -1. These will store the shortest distance found so far and the index of the point achieving it.
*   **Iteration**: Looping through each `point` in the `points` list using `enumerate` to get both the index `i` and the `point` itself `(px, py)`.
*   **Validity Check**: For each point, it first checks if `px == x` or `py == y`. If neither condition is met, the point is not valid and is skipped.
*   **Distance Calculation**: If the point is valid, it calculates the Manhattan distance to the reference point `(x, y)` using `abs(x - px) + abs(y - py)`.
*   **Update Minimum**:
    *   If the `current_distance` is strictly less than `min_distance`, it updates `min_distance` to `current_distance` and `min_index` to the current point's index `i`.
    *   The tie-breaking rule (preferring the smallest index for equal distances) is implicitly handled here: the code *only* updates if `current_distance < min_distance`. If `current_distance == min_distance`, the `min_index` (which must have been found at an earlier, smaller index) is preserved, correctly fulfilling the requirement.
*   **Return Value**: After checking all points, the function returns the `min_index`.

---

### 3. Key Design Decisions

*   **Data Structures**:
    *   `points`: A `List[List[int]]` is used to represent a collection of 2D points. Each inner list `[px, py]` stores the coordinates of a single point. This is a standard and straightforward representation.
    *   Scalar variables (`min_distance`, `min_index`, `px`, `py`, `current_distance`) are used for tracking state and temporary calculations, which is efficient.
*   **Algorithm**:
    *   **Linear Scan**: The core algorithm is a simple linear scan through all available points. This is chosen for its simplicity and directness.
    *   **Manhattan Distance**: The problem explicitly requires Manhattan distance (`|x1 - x2| + |y1 - y2|`). This is directly implemented.
    *   **Tie-breaking**: The update condition `if current_distance < min_distance` naturally handles the tie-breaking rule (smallest index) because the loop processes points in ascending order of their indices.

*   **Trade-offs**:
    *   **Simplicity vs. Performance**: A linear scan is very easy to understand and implement. For the typical constraints of such problems (up to 10^5 points), it's performant enough. For extremely large datasets or frequent queries, spatial indexing structures (like k-d trees or quadtrees) might offer faster average-case lookups, but they would significantly increase complexity for this specific problem where every point might need validity checking.

---

### 4. Complexity

*   **Time Complexity**:
    *   The algorithm iterates through the `points` list exactly once.
    *   Inside the loop, operations like array access, comparisons, arithmetic (`abs`, addition, subtraction), and variable assignments are all constant time operations.
    *   Therefore, if `N` is the number of points in the `points` list, the time complexity is **O(N)**.

*   **Space Complexity**:
    *   The function uses a fixed number of variables (`min_distance`, `min_index`, `px`, `py`, `current_distance`) regardless of the input size.
    *   This means the space complexity is **O(1)** (excluding the space required to store the input `points` list itself).

---

### 5. Edge Cases & Correctness

*   **Empty `points` list**:
    *   The loop `for i, point in enumerate(points)` will not execute.
    *   `min_index` will remain its initial value of -1. This is correct as no valid point can be found.
*   **No valid points in `points`**:
    *   If no point satisfies `px == x or py == y`, `min_index` will remain -1, and `min_distance` will remain `float('inf')`. This is correct.
*   **All points are valid**:
    *   The algorithm will correctly compare all distances and find the closest valid point, breaking ties by index.
*   **Single point in `points`**:
    *   The loop runs once. If the point is valid, its distance is calculated and `min_index` is updated. If not, `min_index` remains -1. Correct.
*   **Points with identical minimum Manhattan distances**:
    *   The explicit check `if current_distance < min_distance:` ensures that if a new point has the *same* distance as the current minimum, the `min_index` is *not* updated. This preserves the index of the point found earlier (which by definition has a smaller index), thus correctly adhering to the tie-breaking rule.
*   **Reference point `(x, y)` is present in `points`**:
    *   If `(x, y)` itself is in the `points` list, its Manhattan distance to itself will be 0. It will be considered a valid point, and its distance of 0 will become the `min_distance`, making it the chosen point (unless another point with index 0 also has a distance of 0, which implies `(x, y)` is at index 0). This is correct behavior.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   The code is already quite readable. The comment explaining the tie-breaking logic is particularly helpful.
    *   Using `math.inf` from the `math` module is an alternative to `float('inf')`, though `float('inf')` is widely understood and used.
*   **Performance**:
    *   For the problem as stated, an `O(N)` solution is optimal because every point must be examined for its validity condition (`px == x or py == y`). There are no significant algorithmic improvements possible without changing the problem definition or constraints.
*   **Pythonic Style**:
    *   The use of `enumerate` and tuple unpacking (`px, py = point`) are idiomatic Python and contribute to good readability.

---

### 7. Security/Performance Notes

*   **Security**: This code does not involve any external input (beyond function parameters), file I/O, network communication, or sensitive data, so there are no apparent security vulnerabilities.
*   **Performance**: The solution is efficient and scales linearly with the number of input points. For typical competitive programming constraints (N up to 10^5), an O(N) solution usually completes well within the time limits (e.g., 1-2 seconds). Python's `abs()` function is implemented efficiently in C, so its performance is not a concern.

### Code:
```python
class Solution:
    def nearestValidPoint(self, x: int, y: int, points: List[List[int]]) -> int:
        min_distance = float('inf')
        min_index = -1

        for i, point in enumerate(points):
            px, py = point[0], point[1]

            # Check if the point is valid
            if px == x or py == y:
                # Calculate Manhattan distance
                current_distance = abs(x - px) + abs(y - py)

                # Update if current point is closer or has the same distance but smaller index
                # The problem states "If there are multiple, return the valid point with the smallest index."
                # Since we iterate from 0-indexed, if current_distance == min_distance,
                # the existing min_index is already smaller or equal, so we only update if strictly smaller.
                if current_distance < min_distance:
                    min_distance = current_distance
                    min_index = i
        
        return min_index
```
