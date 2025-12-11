## Surrounded Regions
**Language:** python
**Tags:** python,oop,dfs,grid,in-place

### Description:
This code implements a solution to the "Surrounded Regions" problem. It modifies a 2D board of characters ('X' and 'O') in-place, flipping 'O's that are entirely surrounded by 'X's to 'X's. An 'O' is considered *not* surrounded if it is on the border of the board or connected to an 'O' that is on the border.

---

### 1. Overview & Intent

*   **Problem**: Given an `m x n` 2D board containing 'X' and 'O', capture all regions that are surrounded by 'X's.
*   **Definition of "Captured"**: A region is captured if all 'O's within it are completely enclosed by 'X's. 'O's on the boarder are never captured.
*   **Output**: The board is modified in-place; the function does not return anything.
*   **Approach**: Identify 'O's connected to the border, mark them as "safe", then flip any remaining 'O's to 'X's, and finally restore the "safe" 'O's.

---

### 2. How It Works

The solution uses a two-phase approach based on Depth-First Search (DFS) for connectivity:

1.  **Phase 1: Mark Safe 'O's**:
    *   It first handles edge cases for empty boards.
    *   It defines a `dfs` helper function that takes row and column coordinates. This function recursively explores adjacent cells.
        *   **Base Cases**: It stops if coordinates are out of bounds, or if the current cell is not an 'O' (meaning it's 'X' or already visited 'M').
        *   **Action**: If it finds an 'O', it marks it with 'M' (for "marked" or "middleman" – signifying it's safe) and then recursively calls itself for all four neighbors (up, down, left, right).
    *   The code then iterates through all cells on the **borders** of the board (top row, bottom row, left column, right column).
    *   If an 'O' is found on any border cell, the `dfs` function is called from that cell. This effectively marks all 'O's connected to that border 'O' (and thus connected to the border itself) as 'M'.

2.  **Phase 2: Finalize Changes**:
    *   After the DFS phase, the code iterates through the *entire* board.
    *   If a cell contains an 'O', it means this 'O' was *not* reached by any border DFS, implying it is surrounded by 'X's. Such 'O's are changed to 'X'.
    *   If a cell contains an 'M', it means this 'O' was connected to the border and thus "safe". These 'M's are changed back to 'O'.

---

### 3. Key Design Decisions

*   **Algorithm Choice**: Depth-First Search (DFS) is used to traverse connected components of 'O's. This is a standard approach for connectivity problems on grids.
*   **In-Place Modification**: The board is modified directly, avoiding the need for creating a copy, which saves space.
*   **Temporary Marker**: The character 'M' is used as a temporary marker. This is a crucial choice because it allows distinguishing 'O's that have been visited and deemed safe from 'O's that haven't been visited yet (and thus might be surrounded).
*   **Two-Pass Approach**: The algorithm requires two main passes: one to identify and mark safe 'O's (using border traversal and DFS), and a second to finalize the board based on the temporary marks.
*   **Border-First Strategy**: The strategy of starting DFS only from border 'O's cleverly identifies all un-capturable 'O's directly.

---

### 4. Complexity

*   **Time Complexity**: `O(m * n)`
    *   The initial border traversal loops visit `2*(m+n)-4` cells.
    *   The DFS function, in the worst case, might visit every cell on the board (if all are 'O's and connected). Each cell is visited by DFS at most once because it's marked as 'M' after the first visit, preventing redundant processing.
    *   The final iteration over the entire board takes `O(m * n)` time.
    *   Therefore, the dominant factor is `O(m * n)`.
*   **Space Complexity**: `O(m * n)`
    *   The modification is done in-place, so no extra space is used for the board itself.
    *   The primary space consumption comes from the recursion stack used by DFS. In the worst case (e.g., a board full of 'O's forming a long path), the recursion depth can go up to `O(m * n)`.

---

### 5. Edge Cases & Correctness

*   **Empty Board/Empty Row**:
    *   `if not board or not board[0]: return` handles cases like `[]` or `[[]]` gracefully, preventing errors. Correct.
*   **Single Cell Board**:
    *   `[["X"]]`: No DFS, no change. Correct.
    *   `[["O"]]`: DFS from border (top-left), marks 'O' as 'M', then restored to 'O'. Correct, as it's on the border.
*   **Small Boards (e.g., 1xN, Nx1)**:
    *   `[["O", "X", "O"]]`: Both 'O's are on the border, become 'M', then restored. Correct.
    *   `[["X"], ["O"], ["X"]]`: The middle 'O' is on the border, becomes 'M', then restored. Correct.
*   **Board with no 'O's**:
    *   The border loops won't find 'O's, so DFS is never called. The final loop makes no changes. Correct.
*   **Board with all 'O's**:
    *   All 'O's are connected to the border, so all will be marked 'M' by DFS and subsequently restored to 'O'. Correct.
*   **Surrounded Region**:
    *   Example: `[["X","X","X"], ["X","O","X"], ["X","X","X"]]`. The inner 'O' is not reached by border DFS, remains 'O', then changed to 'X'. Correct.
*   **Unusual Characters**: The code implicitly assumes only 'X' and 'O' are present, and 'M' is a safe temporary character. If other characters were possible, this could lead to issues.

---

### 6. Improvements & Alternatives

*   **Iterative BFS Instead of Recursive DFS**:
    *   **Improvement**: An iterative Breadth-First Search (BFS) using a `collections.deque` would avoid Python's recursion depth limit. For very large boards with extensive connected 'O' regions, recursive DFS can hit this limit, leading to a `RecursionError`.
    *   **Change**: Instead of recursive calls, neighbors would be added to a queue.
*   **Consolidate Border Traversal**:
    *   The border loops could be slightly refactored for conciseness, e.g., by creating a list of border coordinates and iterating through them once.
    *   `border_cells = [(0, c) for c in range(n)] + [(m-1, c) for c in range(n)] + [(r, 0) for r in range(1, m-1)] + [(r, n-1) for r in range(1, m-1)]`
    *   `for r, c in border_cells: if board[r][c] == 'O': dfs(r, c)`
*   **Alternative Marking Strategy**:
    *   If direct in-place modification with a temporary character (`'M'`) is not allowed or desired, a separate `visited` 2D boolean array could be used. This would increase space complexity for the `visited` array (`O(m*n)`), but might be cleaner if the input board needs to be read-only initially. However, since the problem states "modify board in-place", the current approach is perfectly fine.

---

### 7. Security/Performance Notes

*   **Recursion Depth Limit**: As mentioned, for very large grid sizes, Python's default recursion limit might be exceeded. This is a common performance/robustness concern with recursive DFS on large inputs. The solution is performant within typical competitive programming constraints but could fail for extremely large "snake-like" 'O' paths.
*   **No External Dependencies**: The code uses standard Python features and `typing.List`, so there are no security concerns related to third-party libraries.
*   **Efficient Memory Usage**: Modifying the board in-place is memory efficient as it avoids creating new copies of the board data.

### Code:
```python
from typing import List

class Solution:
    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        if not board or not board[0]:
            return

        m, n = len(board), len(board[0])

        def dfs(r, c):
            # Base cases for DFS:
            # 1. Out of bounds
            # 2. Not an 'O' (either 'X' or already visited 'M')
            if r < 0 or r >= m or c < 0 or c >= n or board[r][c] != 'O':
                return

            # Mark the 'O' as visited and safe (connected to border)
            board[r][c] = 'M'

            # Explore all 4 neighbors
            dfs(r + 1, c) # Down
            dfs(r - 1, c) # Up
            dfs(r, c + 1) # Right
            dfs(r, c - 1) # Left

        # 1. Traverse the borders to find all 'O's connected to the edge
        #    and mark them (and their connected 'O's) as 'M'.

        # Top and Bottom borders
        for c in range(n):
            if board[0][c] == 'O':
                dfs(0, c)
            if board[m - 1][c] == 'O':
                dfs(m - 1, c)

        # Left and Right borders (excluding corners already processed by top/bottom loops)
        for r in range(1, m - 1): # Start from 1 and end at m-2 to avoid re-processing corners
            if board[r][0] == 'O':
                dfs(r, 0)
            if board[r][n - 1] == 'O':
                dfs(r, n - 1)

        # 2. Iterate through the entire board to finalize the changes:
        #    - 'O's that are still 'O' are surrounded, so change them to 'X'.
        #    - 'M's were safe 'O's, so change them back to 'O'.
        for r in range(m):
            for c in range(n):
                if board[r][c] == 'O':
                    # This 'O' was not reached by DFS from the border,
                    # meaning it's part of a surrounded region.
                    board[r][c] = 'X'
                elif board[r][c] == 'M':
                    # This 'M' was a safe 'O' connected to the border,
                    # so restore it to 'O'.
                    board[r][c] = 'O'
```
