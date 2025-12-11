## Right Triangles
**Language:** python
**Tags:** python,matrix,precomputation,counting

### Description:
This code defines a method to count the number of "right triangles" formed by '1's in a binary grid. A right triangle here is defined by three '1's: one acting as the vertex of the right angle, and the other two forming the legs along the same row and column as the vertex.

---

### 1. Overview & Intent

*   **Purpose**: The `numberOfRightTriangles` method calculates the total count of valid right-angled triangles in a given `m x n` binary grid.
*   **Definition of Triangle**: A right triangle is formed by three '1's, where one '1' acts as the pivot (the vertex of the 90-degree angle), and the other two '1's lie on the same row and same column as the pivot, respectively.
*   **Input**: A `List[List[int]]` representing a 2D binary grid (containing only 0s and 1s).
*   **Output**: An integer representing the total number of such right triangles.

---

### 2. How It Works

The algorithm uses a two-pass approach: a precomputation pass and a main calculation pass.

*   **Initialization**:
    *   Determines `num_rows` and `num_cols` from the input grid dimensions.
    *   Initializes `rows_ones` and `cols_ones` lists with zeros. These lists will store the count of '1's in each respective row and column.

*   **First Pass (Precomputation)**:
    *   Iterates through every cell `(r, c)` of the grid.
    *   If `grid[r][c]` is `1`:
        *   It increments `rows_ones[r]` (the count for the current row).
        *   It increments `cols_ones[c]` (the count for the current column).
    *   After this pass, `rows_ones[r]` holds the total number of '1's in row `r`, and `cols_ones[c]` holds the total number of '1's in column `c`.

*   **Second Pass (Triangle Counting)**:
    *   Initializes `total_triangles` to `0`.
    *   Iterates through every cell `(r, c)` of the grid again.
    *   If `grid[r][c]` is `1`:
        *   This cell is considered a potential vertex for a right angle.
        *   `count_in_row` is calculated as `rows_ones[r] - 1`. This represents the number of *other* '1's in the current row `r` (excluding the pivot `grid[r][c]` itself).
        *   `count_in_col` is calculated as `cols_ones[c] - 1`. This represents the number of *other* '1's in the current column `c` (excluding the pivot `grid[r][c]` itself).
        *   If `count_in_row` is greater than 0 AND `count_in_col` is greater than 0:
            *   It means there's at least one other '1' in the same row and at least one other '1' in the same column.
            *   The number of right triangles that can be formed with `grid[r][c]` as the pivot is `count_in_row * count_in_col`. This is because any of the `count_in_row` '1's can form one leg, and any of the `count_in_col` '1's can form the other leg.
            *   This product is added to `total_triangles`.

*   **Result**: Returns the final `total_triangles` count.

---

### 3. Key Design Decisions

*   **Two-Pass Approach**:
    *   **Decision**: First precompute row/column sums, then iterate again to calculate triangles.
    *   **Rationale**: This avoids redundant recalculations. If we didn't precompute, for each potential pivot, we'd have to scan its entire row and column, leading to a much higher time complexity.
*   **Data Structures (`rows_ones`, `cols_ones` lists)**:
    *   **Decision**: Use simple Python lists (arrays) to store counts.
    *   **Rationale**: Lists provide O(1) access to row/column counts once precomputed. They are lightweight and efficient for this purpose.
*   **Counting Logic (`count_in_row - 1`, `count_in_col - 1`)**:
    *   **Decision**: Subtract 1 from total '1's in a row/column to exclude the pivot itself.
    *   **Rationale**: A triangle needs three distinct '1's. The pivot is one; the other two must be *different* '1's on its row and column.

---

### 4. Complexity

Let `M` be the number of rows and `N` be the number of columns in the grid.

*   **Time Complexity: O(M * N)**
    *   **Precomputation Pass**: The nested loop iterates through all `M * N` cells once to populate `rows_ones` and `cols_ones`. This is `O(M * N)`.
    *   **Triangle Counting Pass**: The second nested loop also iterates through all `M * N` cells. Inside the loop, operations (list access, arithmetic) are `O(1)`. This is `O(M * N)`.
    *   **Total**: The total time complexity is dominated by these two passes, resulting in `O(M * N)`.

*   **Space Complexity: O(M + N)**
    *   `rows_ones`: A list of size `M` to store row counts. `O(M)` space.
    *   `cols_ones`: A list of size `N` to store column counts. `O(N)` space.
    *   **Total**: The total space complexity is `O(M + N)`.

---

### 5. Edge Cases & Correctness

The code handles various edge cases correctly:

*   **Empty Grid or Single-Cell Grid**: Assumes a valid grid per typical problem constraints (e.g., `1 <= m, n <= 1000`). If `grid` is `[]` or `[[]]`, `len(grid[0])` would raise an `IndexError`. For a single-cell `[[1]]` grid, `rows_ones[0]` and `cols_ones[0]` become `1`, but `count_in_row` and `count_in_col` become `0`, correctly resulting in `0` triangles.
*   **Grid with No '1's**:
    *   `rows_ones` and `cols_ones` will remain all zeros.
    *   The second pass will find no `grid[r][c] == 1`, so `total_triangles` remains `0`. Correct.
*   **Grid with '1's but Insufficient to Form a Triangle**:
    *   Example: `[[1, 1, 1], [0, 0, 0]]` (a single row of '1's).
    *   For `(0,0)`, `rows_ones[0]` is `3`, `cols_ones[0]` is `1`. `count_in_row` is `2`, `count_in_col` is `0`. `total_triangles` is not incremented because `count_in_col` is not `> 0`. Correct, as no vertical leg can be formed.
*   **Minimum Triangle**: A `2x2` grid like `[[1,1],[1,0]]` would correctly identify `(0,0)` as a pivot, with `count_in_row = 1` and `count_in_col = 1`, adding `1*1 = 1` triangle.
*   **Non-Square Grids**: The logic correctly uses `num_rows` and `num_cols`, so it works perfectly for `M x N` grids where `M != N`.

---

### 6. Improvements & Alternatives

*   **Readability**: The code is already quite readable. Variable names are descriptive, and comments explain the core logic well. No major improvements are needed here.
*   **Performance**:
    *   The current `O(M * N)` time complexity is optimal because every cell in the grid might potentially be a vertex or part of a leg, meaning we inherently need to examine all cells at least once.
    *   The `O(M + N)` space complexity is also optimal for storing row/column counts independently.
*   **Robustness (Minor)**: For a production system, one might add input validation to ensure `grid` is not empty, `grid[0]` is not empty, and all rows have the same number of columns, although competitive programming problems typically guarantee valid inputs.

---

### 7. Security/Performance Notes

*   **Security**: There are no inherent security vulnerabilities in this algorithm. It purely processes numerical data and does not interact with external systems, user input directly, or sensitive resources.
*   **Performance**: The solution is efficient for the problem constraints. The use of Python lists for `rows_ones` and `cols_ones` is appropriate. For extremely large grids (e.g., beyond typical memory limits for Python lists), one might consider memory-mapped files or other strategies for very sparse grids, but that's beyond the typical scope of this problem. The current approach scales linearly with the size of the grid, which is ideal.

### Code:
```python
from typing import List

class Solution:
    def numberOfRightTriangles(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])

        rows_ones = [0] * num_rows
        cols_ones = [0] * num_cols

        # Precompute the number of '1's in each row and each column
        for r in range(num_rows):
            for c in range(num_cols):
                if grid[r][c] == 1:
                    rows_ones[r] += 1
                    cols_ones[c] += 1

        total_triangles = 0

        # Iterate through the grid to find potential right-angle vertices
        for r in range(num_rows):
            for c in range(num_cols):
                if grid[r][c] == 1:
                    # If grid[r][c] is '1', it can be the vertex of the right angle.
                    # We need to count how many other '1's are in the same row (to form the base)
                    # and how many other '1's are in the same column (to form the height).
                    
                    # Number of other '1's in row 'r' (excluding grid[r][c] itself)
                    count_in_row = rows_ones[r] - 1 
                    
                    # Number of other '1's in column 'c' (excluding grid[r][c] itself)
                    count_in_col = cols_ones[c] - 1
                    
                    # If there are '1's available in both the row and column,
                    # we can form `count_in_row * count_in_col` triangles with (r,c) as the pivot.
                    # We only add if both counts are positive, meaning there's at least one other '1'
                    # in the row and one other '1' in the column.
                    if count_in_row > 0 and count_in_col > 0:
                        total_triangles += count_in_row * count_in_col
        
        return total_triangles
```
