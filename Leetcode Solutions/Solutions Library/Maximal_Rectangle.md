## Maximal Rectangle
**Language:** python
**Tags:** python,object-oriented,dynamic programming,monotonic stack

### Description:
---

### 1. Overview & Intent

*   **Problem**: Given a `rows x cols` binary matrix filled with '0's and '1's, the goal is to find the largest rectangle containing only '1's and return its area.
*   **Approach**: The code tackles the 2D "Maximal Rectangle" problem by reducing it to a series of 1D "Largest Rectangle in Histogram" (LRH) problems. It iterates through each row, dynamically building a histogram representing the heights of consecutive '1's above that row, and then finds the largest rectangle within that histogram.

### 2. How It Works

The solution processes the matrix row by row:

1.  **Initialization**:
    *   Handles empty or malformed input matrices by returning `0`.
    *   `max_area` stores the overall maximum area found so far.
    *   `heights` is an array initialized to zeros, with `cols` length. This array will store the "height" of consecutive '1's ending at the current cell `(i, j)`.

2.  **Row Iteration**:
    *   The outer loop iterates `i` from `0` to `rows - 1`. For each row `i`:
        *   **Update `heights` array**: The inner loop (`j` from `0` to `cols - 1`) updates the `heights` array for the current row:
            *   If `matrix[i][j]` is `'1'`, it means the '1' extends the current column's consecutive '1's upwards, so `heights[j]` is incremented.
            *   If `matrix[i][j]` is `'0'`, the chain of consecutive '1's is broken at this column, so `heights[j]` is reset to `0`.
        *   **Largest Rectangle in Histogram (LRH)**: After updating `heights` for the current row, the problem becomes finding the largest rectangle within this `heights` array (which now represents a histogram).
            *   A `stack` is used to keep track of indices of bars in *increasing* order of their heights.
            *   The LRH algorithm iterates through `heights` (including an implicit `0` sentinel at the end, `k = cols`, to ensure all bars on the stack are processed).
            *   **Pop Condition**: If the stack is not empty and the height of the bar at the top of the stack (`heights[stack[-1]]`) is greater than or equal to the `current_height` (of `heights[k]`), bars are popped from the stack.
                *   For each popped bar (let its index be `h_idx` and height `h`), its area is calculated: `h * w`.
                *   The `width (w)` calculation is crucial:
                    *   If the stack becomes empty after popping `h_idx`, it means `h` is the shortest bar encountered so far to its left, so its width spans from index `0` to `k-1`. Width = `k`.
                    *   Otherwise, the new top of the stack (`stack[-1]`) is the index of the first bar to the left that is *smaller* than `h`. So, `h` can extend from `stack[-1] + 1` to `k-1`. Width = `k - stack[-1] - 1`.
                *   `current_max_hist_area` is updated.
            *   **Push Condition**: After popping, the current index `k` is pushed onto the stack. This maintains the monotonic increasing property of the stack.
        *   **Update `max_area`**: The `max_area` (overall for the matrix) is updated with `current_max_hist_area` found for the current row's histogram.

3.  **Return**: After processing all rows, `max_area` holds the largest rectangle area.

### 3. Key Design Decisions

*   **Reduction to LRH**: The most significant design decision is transforming the 2D problem into a series of 1D problems. Any maximal rectangle in the matrix must be the maximal rectangle of *some* histogram formed by stacking '1's up to a particular row. This insight allows reusing a well-known efficient algorithm.
*   **`heights` Array**: This array acts as a dynamic programming approach to efficiently calculate the "height" of consecutive '1's for each column. Instead of re-scanning columns upwards for each row, it leverages the previous row's calculations.
*   **Monotonic Stack for LRH**: The use of a monotonic stack (specifically, one that stores indices of bars in increasing order of height) is critical for solving the LRH problem efficiently. It allows determining the left and right boundaries for each bar in `O(1)` amortized time.
*   **Sentinel Value (`cols + 1`)**: Adding an implicit `0` at the end of the `heights` array (handled by iterating `k` up to `cols`) simplifies the LRH logic. It ensures that all remaining bars on the stack are popped and their areas calculated, as any bar on the stack will be greater than or equal to this artificial `0` height.

### 4. Complexity

*   **Time Complexity**: `O(rows * cols)`
    *   The outer loop runs `rows` times.
    *   Inside the outer loop:
        *   Updating `heights` takes `O(cols)` time.
        *   The "Largest Rectangle in Histogram" algorithm runs on an array of `cols` elements. Each element (index) is pushed onto the stack and popped from the stack at most once. Thus, this part takes `O(cols)` time.
    *   Total time: `rows * (O(cols) + O(cols)) = O(rows * cols)`.
*   **Space Complexity**: `O(cols)`
    *   The `heights` array stores `cols` integers: `O(cols)`.
    *   The `stack` in the worst case (e.g., a histogram with monotonically increasing heights) can store up to `cols` indices: `O(cols)`.
    *   Total space: `O(cols)`.

### 5. Edge Cases & Correctness

*   **Empty Matrix**: `if not matrix or not matrix[0]: return 0` correctly handles cases where the input is `[]` or `[[""]]` (an empty row), returning 0.
*   **Matrix with all '0's**: `heights` will always be `[0, 0, ..., 0]`. The LRH algorithm will correctly calculate 0 area for each row, and `max_area` will remain 0.
*   **Matrix with all '1's**: `heights` will increment for each row, correctly building a histogram that represents the entire matrix. The LRH algorithm will find the `rows * cols` area correctly.
*   **Single Row/Column**: These cases are naturally handled by the general `rows * cols` logic without special conditions.
*   **Correctness of Width Calculation**: The logic `w = k if not stack else k - stack[-1] - 1` is precisely how to calculate the width for a popped bar in the monotonic stack LRH algorithm.
    *   `k` represents the index of the first bar to the right that is *smaller* than the popped bar (or the end of the array).
    *   `stack[-1]` (if not empty) represents the index of the first bar to the left that is *smaller* than the popped bar.
    *   The width is correctly calculated as `(right_boundary - 1) - (left_boundary + 1) + 1`.

### 6. Improvements & Alternatives

*   **Readability**: The code is well-structured and uses descriptive variable names. The comments, especially before the LRH section, are very helpful. No major readability improvements are strictly necessary. One minor refactoring could be to extract the LRH logic into a separate helper method, though it's concise enough here.
*   **Performance**: The current `O(rows * cols)` time complexity is optimal for this problem, as every cell in the matrix must be considered at least once. There's no asymptotically faster approach.
*   **Alternative Approaches**:
    *   **Dynamic Programming (Another DP variant)**: A different DP approach involves maintaining three arrays for each cell `(i, j)`: `left[j]` (leftmost boundary of '1's ending at `(i, j)`), `right[j]` (rightmost boundary), and `height[j]` (current height). This also achieves `O(rows * cols)` time and `O(cols)` space. The current solution's `heights` array is a form of DP, and then the LRH solves the width part efficiently.

### 7. Security/Performance Notes

*   **Security**: There are no apparent security vulnerabilities. The code operates on a simple binary matrix and does not involve external input, network operations, or complex data manipulation that could lead to injection or overflow issues.
*   **Performance**: As noted, the algorithm is optimal in terms of Big-O complexity. For extremely large matrices, the constant factors might become relevant, but for typical competitive programming constraints, this solution is efficient. Python's list operations (append, pop, indexing) are implemented in C and are generally performant.

### Code:
```python
from typing import List

class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows = len(matrix)
        cols = len(matrix[0])
        max_area = 0
        
        # heights array stores the height of consecutive '1's ending at the current cell (j)
        # for the current row (i).
        heights = [0] * cols

        for i in range(rows):
            # Update heights for the current row based on the matrix values
            for j in range(cols):
                if matrix[i][j] == '1':
                    heights[j] += 1
                else:
                    heights[j] = 0 # Reset height if '0' is encountered

            # Now, calculate the largest rectangle in the histogram formed by 'heights'
            # This is a standard problem: "Largest Rectangle in Histogram"
            
            stack = [] # Stores indices of bars in increasing order of height
            current_max_hist_area = 0
            
            # Iterate through the heights array (including a sentinel 0 at the end)
            # The sentinel helps to pop all remaining elements from the stack and calculate their areas
            for k in range(cols + 1):
                # Get the current height, or 0 if it's the sentinel position
                current_height = heights[k] if k < cols else 0
                
                # While the stack is not empty and the height of the bar at the top of the stack
                # is greater than or equal to the current height
                while stack and heights[stack[-1]] >= current_height:
                    h = heights[stack.pop()] # Pop the top bar's index and get its height
                    
                    # Calculate the width for the popped bar
                    # If the stack is empty, it means the popped bar is the smallest
                    # up to the current index k, so its width extends from 0 to k-1.
                    # Otherwise, its width extends from (stack[-1] + 1) to (k-1).
                    w = k if not stack else k - stack[-1] - 1
                    
                    current_max_hist_area = max(current_max_hist_area, h * w)
                
                # Push the current index onto the stack
                stack.append(k)
            
            # Update the overall max_area with the max area found in the current histogram
            max_area = max(max_area, current_max_hist_area)
            
        return max_area
```
