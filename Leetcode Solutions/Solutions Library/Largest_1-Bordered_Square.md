## Largest 1-Bordered Square
**Language:** python
**Tags:** python,oop,dynamic programming,2d array

### Description:
This code solves the problem of finding the largest square composed entirely of '1's, where *only the border* of the square needs to be '1's. The interior of the square can contain '0's or '1's.

## 1. Overview & Intent

The primary goal of the `largest1BorderedSquare` function is to calculate the area of the largest square within a given 2D binary grid that has all '1's along its border. The core idea is to use dynamic programming to efficiently precompute lengths of consecutive '1's horizontally and vertically, then iterate through possible bottom-right corners of squares to check their borders.

## 2. How It Works

1.  **Input Validation**:
    *   It first checks if the input `grid` is empty (either no rows or no columns). If so, it immediately returns `0`, as no square can exist.

2.  **Dynamic Programming Table Initialization**:
    *   Two 2D arrays, `dp_h` (horizontal DP) and `dp_v` (vertical DP), are created with the same dimensions as the input `grid`.
    *   `dp_h[r][c]` will store the count of consecutive '1's ending at `(r, c)` and extending to the left (inclusive of `(r, c)`).
    *   `dp_v[r][c]` will store the count of consecutive '1's ending at `(r, c)` and extending upwards (inclusive of `(r, c)`).

3.  **Precomputing `dp_h` and `dp_v`**:
    *   The code iterates through each cell `(r, c)` of the `grid`.
    *   If `grid[r][c]` is `1`:
        *   `dp_h[r][c]` is set to `1` plus the value of `dp_h[r][c-1]` (if `c > 0`, otherwise `0`). This extends the horizontal count.
        *   `dp_v[r][c]` is set to `1` plus the value of `dp_v[r-1][c]` (if `r > 0`, otherwise `0`). This extends the vertical count.
    *   If `grid[r][c]` is `0`, both `dp_h[r][c]` and `dp_v[r][c]` remain `0`.

4.  **Finding the Largest Square**:
    *   A variable `max_side_length` is initialized to `0`.
    *   The code then iterates through each cell `(r, c)` of the `grid` again, treating each `(r, c)` as a potential *bottom-right corner* of a square.
    *   If `grid[r][c]` is `0`, it cannot be part of any 1-bordered square's corner, so it's skipped.
    *   For a cell `(r, c)` that is `1`:
        *   `max_k_at_rc` is determined as `min(dp_h[r][c], dp_v[r][c])`. This gives the maximum possible side length `k` for a square with `(r, c)` as its bottom-right, based on the current horizontal and vertical runs.
        *   An inner loop iterates `k` downwards from `max_k_at_rc` to `1`. The goal is to find the *largest* `k` for the current `(r, c)`.
        *   Inside this loop, it checks if a square of side `k` can be formed with `(r, c)` as its bottom-right:
            *   It checks the `dp_h` value at `(r - k + 1, c)`. This location corresponds to the rightmost point of the *top row* of the potential square. `dp_h[r - k + 1][c] >= k` verifies that the top row has at least `k` consecutive '1's.
            *   It checks the `dp_v` value at `(r, c - k + 1)`. This location corresponds to the bottommost point of the *left column* of the potential square. `dp_v[r][c - k + 1] >= k` verifies that the left column has at least `k` consecutive '1's.
            *   If both conditions are met, a square of side `k` is found. `max_side_length` is updated, and the inner `k` loop `break`s (because we're iterating `k` downwards, the first one found is the largest for this `(r, c)`).

5.  **Result**:
    *   Finally, the function returns `max_side_length * max_side_length`, which is the area of the largest 1-bordered square.

## 3. Key Design Decisions

*   **Dynamic Programming for Precomputation**:
    *   **Decision**: Use `dp_h` and `dp_v` tables to store lengths of consecutive '1's.
    *   **Trade-off**: This increases space complexity (O(R*C)) but drastically reduces time complexity. Without these tables, checking if a potential square's border is all '1's would involve iterating along each of its four sides, leading to much slower checks (O(k) per check instead of O(1)).
*   **Iterating Bottom-Right Corner**:
    *   **Decision**: The main loops iterate through `(r, c)` and consider each as a potential bottom-right corner. This is a common and effective strategy for matrix problems involving squares or rectangles.
*   **Iterating Side Length `k` Downwards**:
    *   **Decision**: For each potential bottom-right corner `(r, c)`, the inner loop for `k` (side length) starts from `max_k_at_rc` and decrements.
    *   **Benefit**: This ensures that the *first* `k` that satisfies the border conditions is the largest possible for that `(r, c)`, allowing for an early `break` from the inner loop and optimizing performance.

## 4. Complexity

*   **Time Complexity**:
    *   **Precomputation (dp_h, dp_v)**: Two nested loops iterate through all `R * C` cells, performing constant time operations per cell. This is O(R * C).
    *   **Finding Largest Square**:
        *   Two nested loops iterate through all `R * C` cells (`(r, c)`).
        *   The innermost loop iterates `k` from `min(dp_h[r][c], dp_v[r][c])` down to `1`. In the worst case, this is `min(R, C)` iterations.
        *   Inside the `k` loop, operations are O(1).
        *   Therefore, this part is O(R * C * min(R, C)).
    *   **Total Time Complexity**: O(R * C * min(R, C)).

*   **Space Complexity**:
    *   Two DP tables (`dp_h` and `dp_v`) are created, each of size `R * C`.
    *   **Total Space Complexity**: O(R * C).

## 5. Edge Cases & Correctness

*   **Empty Grid**:
    *   `grid = []` or `grid = [[]]`: Handled by the initial `if R == 0` or `if C == 0` checks, returning `0`. Correct.
*   **Single Cell Grid**:
    *   `grid = [[1]]`: `dp_h[0][0]=1`, `dp_v[0][0]=1`. `max_k_at_rc=1`. `k=1` check passes. `max_side_length` becomes `1`. Returns `1`. Correct.
    *   `grid = [[0]]`: `grid[0][0]` is 0, so the inner loops are skipped. `max_side_length` remains `0`. Returns `0`. Correct.
*   **Grid of All Zeros**:
    *   All `dp_h` and `dp_v` entries will be `0`. The outer loops for finding the square will skip all `(r, c)` because `grid[r][c] == 0`. `max_side_length` remains `0`. Returns `0`. Correct.
*   **Grid of All Ones**:
    *   The precomputation will fill `dp_h[r][c]` with `c+1` and `dp_v[r][c]` with `r+1`. The algorithm will correctly identify the largest possible square (e.g., if `grid` is 3x4, it will find a 3x3 square). Correct.
*   **Non-Square Grids**:
    *   The logic correctly handles non-square grids (e.g., 2x5). `min(R, C)` properly constrains `max_k_at_rc`, ensuring `k` never exceeds the grid's smaller dimension. Correct.

## 6. Improvements & Alternatives

*   **Readability**: The code is already quite readable with clear variable names and comments. No significant improvements are needed here.
*   **Space Optimization (Minor)**: While O(R*C) space is typical for this DP problem, if `R` or `C` were extremely large, one might consider if `dp_h` and `dp_v` could be optimized. However, given the need to access `dp_h[r - k + 1][c]` and `dp_v[r][c - k + 1]`, reducing space to O(min(R,C)) or O(1) would significantly complicate the logic or increase time complexity by forcing recomputations. For standard constraints (e.g., 500x500), O(R*C) space is perfectly acceptable (2 * 500 * 500 * 4 bytes for ints ≈ 2MB).
*   **Alternative Approaches**:
    *   **Brute-Force**: Iterate over every possible top-left corner `(r1, c1)` and every possible side length `k`. For each potential square, iterate along its four borders to check for '1's. This would be much slower, likely O(R^3 * C^3) in the worst case, making the DP approach superior.

## 7. Security/Performance Notes

*   **Performance**: The current DP approach is efficient for the problem constraints typically found in competitive programming (e.g., grid sizes up to 500x500). The `O(R * C * min(R, C))` complexity means for a 500x500 grid, it would be roughly `500 * 500 * 500 = 125 * 10^6` operations, which is generally acceptable within a few seconds time limit.
*   **Security**: For this type of algorithmic problem, there are no direct security implications. The code processes a given binary grid without external inputs or side effects that could introduce vulnerabilities. Input validation for `grid` values (e.g., ensuring they are strictly 0 or 1 integers) would be a good practice in a production system, but it's typically assumed valid for competitive programming.

### Code:
```python
from typing import List

class Solution:
    def largest1BorderedSquare(self, grid: List[List[int]]) -> int:
        R = len(grid)
        if R == 0:
            return 0
        C = len(grid[0])
        if C == 0:
            return 0

        # dp_h[r][c] stores the number of consecutive 1s ending at (r, c) horizontally to the left
        dp_h = [[0] * C for _ in range(R)]
        # dp_v[r][c] stores the number of consecutive 1s ending at (r, c) vertically upwards
        dp_v = [[0] * C for _ in range(R)]

        # Precompute dp_h and dp_v tables
        for r in range(R):
            for c in range(C):
                if grid[r][c] == 1:
                    dp_h[r][c] = 1 + (dp_h[r][c-1] if c > 0 else 0)
                    dp_v[r][c] = 1 + (dp_v[r-1][c] if r > 0 else 0)

        max_side_length = 0

        # Iterate through all possible bottom-right corners (r, c) of a square
        for r in range(R):
            for c in range(C):
                # If grid[r][c] is 0, it cannot be the bottom-right corner of a 1-bordered square
                if grid[r][c] == 0:
                    continue
                
                # The maximum possible side length for a square ending at (r, c)
                # is limited by the horizontal and vertical runs ending at (r, c)
                max_k_at_rc = min(dp_h[r][c], dp_v[r][c])

                # Iterate k downwards from max_k_at_rc to 1
                # We are looking for the largest k for this specific bottom-right corner (r, c)
                for k in range(max_k_at_rc, 0, -1):
                    # Check the top row and left column of the potential square
                    # Top-left corner of the square would be (r - k + 1, c - k + 1)
                    # The top row of length k ends at (r - k + 1, c)
                    # The left column of length k ends at (r, c - k + 1)
                    if dp_h[r - k + 1][c] >= k and \
                       dp_v[r][c - k + 1] >= k:
                        max_side_length = max(max_side_length, k)
                        # Since we are iterating k downwards, the first k we find
                        # is the largest for this (r, c), so we can break and move to the next (r, c).
                        break
        
        return max_side_length * max_side_length
```
