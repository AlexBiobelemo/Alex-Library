## Maximum Path Sum
**Language:** python
**Tags:** python,dynamic programming,matrix,algorithm

### Description:
<p>This code solves the classic "Minimum Path Sum" problem using dynamic programming. It finds the path from the top-left corner to the bottom-right corner of a grid with non-negative numbers, such that the sum of all numbers along the path is minimized. You can only move either down or right at any point in time.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Find the minimum sum path from <code>grid[0][0]</code> to <code>grid[m-1][n-1]</code>.</li>
<li><strong>Constraints:</strong> Movement is restricted to only moving down or right.</li>
<li><strong>Approach:</strong> Utilizes dynamic programming with in-place modification of the input grid to store the minimum path sums to reach each cell.</li>
<li><strong>Output:</strong> Returns the minimum sum stored in <code>grid[m-1][n-1]</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm iteratively updates each cell <code>grid[i][j]</code> to store the minimum sum required to reach that cell from <code>grid[0][0]</code>.</p>
<ol>
<li><p><strong>Initialize Dimensions:</strong></p>
<ul>
<li><code>m</code>: Number of rows in the grid.</li>
<li><code>n</code>: Number of columns in the grid.</li>
</ul>
</li>
<li><p><strong>Fill the First Row:</strong></p>
<ul>
<li>It iterates through the first row starting from <code>grid[0][1]</code>.</li>
<li>For each cell <code>grid[0][j]</code>, it updates its value to <code>grid[0][j] + grid[0][j-1]</code>.</li>
<li><strong>Logic:</strong> To reach any cell in the first row, you can only come from its immediate left neighbor. So, the minimum path sum to <code>grid[0][j]</code> is its own value plus the minimum path sum to <code>grid[0][j-1]</code>.</li>
</ul>
</li>
<li><p><strong>Fill the First Column:</strong></p>
<ul>
<li>It iterates through the first column starting from <code>grid[1][0]</code>.</li>
<li>For each cell <code>grid[i][0]</code>, it updates its value to <code>grid[i][0] + grid[i-1][0]</code>.</li>
<li><strong>Logic:</strong> Similarly, to reach any cell in the first column, you can only come from its immediate top neighbor. So, the minimum path sum to <code>grid[i][0]</code> is its own value plus the minimum path sum to <code>grid[i-1][0]</code>.</li>
</ul>
</li>
<li><p><strong>Fill the Rest of the Grid:</strong></p>
<ul>
<li>It uses nested loops to iterate through the remaining cells of the grid, starting from <code>grid[1][1]</code>.</li>
<li>For each cell <code>grid[i][j]</code>, it updates its value to <code>grid[i][j] + min(grid[i-1][j], grid[i][j-1])</code>.</li>
<li><strong>Logic:</strong> To reach any cell <code>(i, j)</code> (not in the first row or column), you can either come from the cell directly above (<code>(i-1, j)</code>) or the cell directly to its left (<code>(i, j-1)</code>). The minimum path sum to <code>grid[i][j]</code> is its own value plus the minimum of the pre-calculated minimum path sums to these two preceding cells.</li>
</ul>
</li>
<li><p><strong>Return Result:</strong></p>
<ul>
<li>After filling the entire grid, <code>grid[m-1][n-1]</code> will contain the minimum path sum to reach the bottom-right corner. This value is returned.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (Tabulation):</strong> The problem exhibits optimal substructure (the optimal path to <code>(i,j)</code> depends on optimal paths to <code>(i-1,j)</code> and <code>(i,j-1)</code>) and overlapping subproblems. Dynamic programming is a natural fit. The bottom-up (tabulation) approach builds the solution from base cases outwards.</li>
<li><strong>In-Place Modification:</strong> The input <code>grid</code> itself is used as the DP table. This saves auxiliary space.<ul>
<li><strong>Pros:</strong> Achieves <code>O(1)</code> auxiliary space complexity.</li>
<li><strong>Cons:</strong> Modifies the original input array, which might not be desirable in all scenarios (e.g., if the caller needs the original <code>grid</code> for other operations).</li>
</ul>
</li>
<li><strong>Base Cases Handling:</strong> The first row and first column are handled separately to correctly establish the initial minimum paths, as cells in these areas have only one valid direction of arrival.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: <code>O(m * n)</code></strong><ul>
<li><code>m</code> is the number of rows, <code>n</code> is the number of columns.</li>
<li>The code iterates through each cell of the grid exactly once to calculate its minimum path sum.</li>
<li>The operations inside the loops (addition, <code>min</code> comparison) are constant time.</li>
</ul>
</li>
<li><strong>Space Complexity: <code>O(1)</code> (Auxiliary Space)</strong><ul>
<li>The algorithm modifies the input grid directly and does not use any additional data structures whose size depends on <code>m</code> or <code>n</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Grid or Empty Row/Column:</strong><ul>
<li>The code assumes <code>grid</code> is non-empty and <code>grid[0]</code> is non-empty (<code>m &gt;= 1</code>, <code>n &gt;= 1</code>). If <code>grid</code> is empty (<code>[]</code>), <code>len(grid)</code> would be 0, leading to <code>IndexError</code> on <code>grid[0]</code>. If <code>grid = [[]]</code>, <code>len(grid[0])</code> would be 0, leading to issues.</li>
<li><em>Correction:</em> In a real-world scenario, robust error handling or input validation would be needed (e.g., <code>if not grid or not grid[0]: return 0</code>).</li>
</ul>
</li>
<li><strong>1x1 Grid:</strong><ul>
<li><code>m=1</code>, <code>n=1</code>. All loops (<code>range(1, n)</code> and <code>range(1, m)</code>) will not execute.</li>
<li><code>grid[m-1][n-1]</code> correctly returns <code>grid[0][0]</code>. This is correct, as the path sum is just the value of the single cell.</li>
</ul>
</li>
<li><strong>1xN Grid (Single Row):</strong><ul>
<li><code>m=1</code>. The <code>for i</code> loops will not run (<code>range(1, 1)</code> is empty).</li>
<li>Only the <code>for j</code> loop for the first row will execute.</li>
<li><code>grid[0][n-1]</code> will correctly store the sum of all elements in that row. Correct.</li>
</ul>
</li>
<li><strong>Mx1 Grid (Single Column):</strong><ul>
<li><code>n=1</code>. The <code>for j</code> loops will not run.</li>
<li>Only the <code>for i</code> loop for the first column will execute.</li>
<li><code>grid[m-1][0]</code> will correctly store the sum of all elements in that column. Correct.</li>
</ul>
</li>
<li><strong>Negative Numbers:</strong> The problem statement typically implies non-negative numbers, but the logic would still hold for negative numbers, as <code>min</code> would correctly select the path that leads to the smallest (most negative) sum.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Input Validation:</strong> Add checks for an empty <code>grid</code> or <code>grid</code> with empty rows to prevent <code>IndexError</code>.</li>
<li><strong>Readability (for non-in-place version):</strong> While in-place is efficient, using a separate <code>dp</code> table (<code>dp = [[0]*n for _ in range(m)]</code>) can sometimes make the logic clearer, as you're explicitly building a new state.<ul>
<li>The <code>dp[i][j]</code> would be initialized from <code>grid[i][j]</code> and then updated.</li>
</ul>
</li>
<li><strong>Space Optimization (O(min(M, N))):</strong><ul>
<li>Notice that to calculate the current row <code>i</code>, you only need the values from the previous row <code>i-1</code> and the current row's left neighbor <code>(i, j-1)</code>.</li>
<li>This allows reducing space to <code>O(N)</code> (if iterating row by row) by using a single 1D array to store the previous row's (or current row's partially calculated) minimum path sums. This is often called a "rolling array" or "space-optimized DP".</li>
<li>Example: <code>dp = [0] * n</code>. <code>dp[j]</code> would represent the min path sum to <code>grid[i-1][j]</code>. When calculating for <code>i</code>, <code>dp[j]</code> would become <code>grid[i][j] + min(dp[j], dp[j-1])</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The <code>O(m * n)</code> time complexity is optimal for this problem, as every cell in the grid must be considered at least once to determine the minimum path. The <code>O(1)</code> auxiliary space complexity (due to in-place modification) is also optimal.</li>
<li><strong>Resource Exhaustion:</strong> For extremely large grids, the <code>m * n</code> operations could still take a significant amount of time and the grid itself could consume a large amount of memory. This is a general limitation for large inputs, not a specific flaw in the algorithm.</li>
<li><strong>No Security Vulnerabilities:</strong> This code is purely algorithmic and does not interact with external systems, user input (beyond the grid data), or sensitive data. Thus, there are no direct security concerns.</li>
</ul>


### Code:
```python
class Solution(object):
    def minPathSum(self, grid):
        m = len(grid)
        n = len(grid[0])

        # Fill the first row
        for j in range(1, n):
            grid[0][j] += grid[0][j-1]

        # Fill the first column
        for i in range(1, m):
            grid[i][0] += grid[i-1][0]

        # Fill the rest of the grid
        for i in range(1, m):
            for j in range(1, n):
                grid[i][j] += min(grid[i-1][j], grid[i][j-1])

        return grid[m-1][n-1]
```
