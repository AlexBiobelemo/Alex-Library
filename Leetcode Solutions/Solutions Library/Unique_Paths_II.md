## Unique Paths II
**Language:** python
**Tags:** python,dynamic programming,grid,pathfinding

### Description:
<h3>1. Overview &amp; Intent</h3>
<p>This Python code solves the "Unique Paths II" problem, a classic dynamic programming challenge.</p>
<ul>
<li><strong>Problem:</strong> Given a grid (<code>obstacleGrid</code>) where <code>0</code> represents an empty cell and <code>1</code> represents an obstacle, find the number of unique paths from the top-left corner <code>(0, 0)</code> to the bottom-right corner <code>(m-1, n-1)</code>.</li>
<li><strong>Movement Rules:</strong> You can only move down or right.</li>
<li><strong>Obstacles:</strong> Paths cannot pass through cells containing an obstacle.</li>
<li><strong>Approach:</strong> The solution uses dynamic programming to build up the number of paths to each cell based on the paths to its preceding cells.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm calculates the number of unique paths step-by-step:</p>
<ol>
<li><p><strong>Initialization &amp; Immediate Edge Case:</strong></p>
<ul>
<li>It first extracts the dimensions <code>m</code> (rows) and <code>n</code> (columns) of the <code>obstacleGrid</code>.</li>
<li>A crucial check: If the starting cell <code>obstacleGrid[0][0]</code> is an obstacle (<code>1</code>), it's impossible to start, so the function immediately returns <code>0</code>.</li>
<li>A 2D <code>dp</code> table of the same <code>m x n</code> dimensions is created, initialized with zeros. <code>dp[i][j]</code> will store the number of unique paths from <code>(0,0)</code> to <code>(i,j)</code>.</li>
</ul>
</li>
<li><p><strong>Base Case:</strong></p>
<ul>
<li><code>dp[0][0]</code> is set to <code>1</code>. There is one "path" to the starting cell itself (by simply being there).</li>
</ul>
</li>
<li><p><strong>Fill First Row:</strong></p>
<ul>
<li>It iterates from <code>j = 1</code> to <code>n-1</code> across the first row <code>(0, j)</code>.</li>
<li>For each cell <code>(0, j)</code>: If it's not an obstacle (<code>obstacleGrid[0][j] == 0</code>) AND the cell to its immediate left <code>(0, j-1)</code> was reachable (<code>dp[0][j-1] == 1</code>), then <code>dp[0][j]</code> is set to <code>1</code>. Otherwise, it remains <code>0</code> (meaning it's unreachable, either due to an obstacle at <code>(0,j)</code> or an obstacle blocking <code>(0,j-1)</code>).</li>
</ul>
</li>
<li><p><strong>Fill First Column:</strong></p>
<ul>
<li>Similarly, it iterates from <code>i = 1</code> to <code>m-1</code> down the first column <code>(i, 0)</code>.</li>
<li>For each cell <code>(i, 0)</code>: If it's not an obstacle (<code>obstacleGrid[i][0] == 0</code>) AND the cell directly above it <code>(i-1, 0)</code> was reachable (<code>dp[i-1][0] == 1</code>), then <code>dp[i][0]</code> is set to <code>1</code>. Otherwise, it remains <code>0</code>.</li>
</ul>
</li>
<li><p><strong>Fill Remaining Cells:</strong></p>
<ul>
<li>Nested loops iterate through the rest of the grid, starting from <code>(1, 1)</code> up to <code>(m-1, n-1)</code>.</li>
<li>For each cell <code>(i, j)</code>:<ul>
<li>If <code>obstacleGrid[i][j] == 1</code> (it's an obstacle), <code>dp[i][j]</code> is set to <code>0</code> because no paths can go through it.</li>
<li>Otherwise (it's an empty cell), the number of paths to <code>(i, j)</code> is the sum of paths from the cell above <code>(i-1, j)</code> and the cell to its left <code>(i, j-1)</code>. This is because any path to <code>(i, j)</code> must have come from one of these two adjacent cells. So, <code>dp[i][j] = dp[i-1][j] + dp[i][j-1]</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Final Result:</strong></p>
<ul>
<li>After filling the entire <code>dp</code> table, <code>dp[m-1][n-1]</code> holds the total number of unique paths to the bottom-right corner, which is then returned.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (DP):</strong><ul>
<li><strong>Optimal Substructure:</strong> The solution to the main problem (paths to <code>(m-1, n-1)</code>) is built from solutions to smaller subproblems (paths to <code>(i, j)</code>).</li>
<li><strong>Overlapping Subproblems:</strong> The number of paths to intermediate cells <code>(i, j)</code> are repeatedly needed when calculating paths to subsequent cells. Storing these results in the <code>dp</code> table prevents redundant computations.</li>
</ul>
</li>
<li><strong><code>dp</code> Table:</strong> A 2D array <code>dp[m][n]</code> is chosen to directly map to the grid. Each <code>dp[i][j]</code> intuitively represents the state (number of paths) for cell <code>(i, j)</code>.</li>
<li><strong>Iterative Approach:</strong> The <code>dp</code> table is filled iteratively from top-left to bottom-right. This ensures that when calculating <code>dp[i][j]</code>, the values for <code>dp[i-1][j]</code> and <code>dp[i][j-1]</code> (its dependencies) have already been computed. This avoids recursion overhead and potential stack overflow issues.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: <code>O(m * n)</code></strong><ul>
<li>The algorithm involves iterating through the grid cells several times (once for initialization, once for the first row, once for the first column, and once for the main DP fill). Each cell is processed in constant time. Thus, the total time is directly proportional to the number of cells in the grid (<code>m * n</code>).</li>
</ul>
</li>
<li><strong>Space Complexity: <code>O(m * n)</code></strong><ul>
<li>A 2D <code>dp</code> table of size <code>m x n</code> is created to store the intermediate results. This dominates the space usage.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution effectively handles a variety of edge cases:</p>
<ul>
<li><strong>Starting Cell is an Obstacle:</strong> Handled by the initial check <code>if obstacleGrid[0][0] == 1: return 0</code>.</li>
<li><strong>Ending Cell is an Obstacle:</strong> If <code>obstacleGrid[m-1][n-1] == 1</code>, the main DP loop will set <code>dp[m-1][n-1]</code> to <code>0</code>, correctly indicating no paths.</li>
<li><strong>Obstacles in First Row/Column:</strong> If an obstacle appears in the first row (e.g., <code>obstacleGrid[0][k] == 1</code>), then <code>dp[0][k]</code> will be <code>0</code>, and all subsequent cells <code>dp[0][j]</code> for <code>j &gt; k</code> will also correctly become <code>0</code> (as <code>dp[0][j-1]</code> would be <code>0</code>). The same logic applies to the first column.</li>
<li><strong>Grid with All Obstacles:</strong> The <code>dp</code> table will remain <code>0</code> everywhere (except potentially <code>dp[0][0]</code> if it's not an obstacle), leading to a correct result of <code>0</code>.</li>
<li><strong>Grid with No Obstacles:</strong> The solution correctly reduces to the standard unique paths problem, where each <code>dp[i][j]</code> is <code>dp[i-1][j] + dp[i][j-1]</code>, and <code>dp[0][j]</code> and <code>dp[i][0]</code> propagate <code>1</code>s.</li>
<li><strong>1xN or Nx1 Grids:</strong> The loops and conditions gracefully handle grids that are a single row or a single column, relying primarily on the first row or first column filling logic.</li>
<li><strong>Empty Grid <code>[]</code> or <code>[[]]</code>:</strong> While not explicitly handled, LeetCode constraints typically guarantee <code>m, n &gt;= 1</code>, preventing <code>IndexError</code> when accessing <code>obstacleGrid[0][0]</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Space Optimization (to <code>O(n)</code> or <code>O(m)</code>):</strong></p>
<ul>
<li>Since <code>dp[i][j]</code> only depends on <code>dp[i-1][j]</code> (cell above) and <code>dp[i][j-1]</code> (cell to the left), we don't strictly need the entire <code>m x n</code> DP table. We can optimize space to <code>O(n)</code> (or <code>O(m)</code>) by only keeping track of the current row and the previous row. A more advanced <code>O(n)</code> optimization can use a single 1D array, where <code>dp[j]</code> represents the current cell's value and <code>dp[j-1]</code> represents the value from the current row's left neighbor, while the <em>old</em> <code>dp[j]</code> (before update) implicitly represents the value from the previous row's top neighbor.</li>
<li><strong>Example of <code>O(N)</code> space optimization:</strong><pre><code class="language-python"># Initialize a 1D array representing a row
# dp[j] will store paths to (current_row, j)
dp = [0] * n
dp[0] = 1 # One way to reach the first cell of the current row

for i in range(m):
    for j in range(n):
        if obstacleGrid[i][j] == 1:
            dp[j] = 0 # Obstacle: no paths through this cell
        elif j &gt; 0:
            # If current cell (i,j) is not an obstacle,
            # paths come from:
            #   1. (i-1, j) -- which is the value of dp[j] from the previous row's iteration
            #   2. (i, j-1) -- which is the value of dp[j-1] for the current row's iteration
            dp[j] += dp[j-1]
return dp[n-1]
</code></pre>
This <code>O(N)</code> optimization requires careful handling for the first column (<code>j=0</code>) in subsequent rows. The provided <code>O(M*N)</code> solution is often preferred for its clarity and simpler logic.</li>
</ul>
</li>
<li><p><strong>In-place Modification:</strong> If modifying the input <code>obstacleGrid</code> is allowed, it could potentially be used as the DP table itself. This would reduce the auxiliary space complexity to <code>O(1)</code> (beyond the input grid). However, modifying input is generally discouraged unless specified.</p>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This algorithm operates purely on numerical array data. There are no external inputs, file operations, or network communications, thus no inherent security vulnerabilities.</li>
<li><strong>Performance:</strong><ul>
<li>The <code>O(m*n)</code> time complexity is optimal because every cell in the grid must be visited at least once to determine if it's an obstacle and to calculate its path count.</li>
<li>The <code>O(m*n)</code> space complexity is generally acceptable for typical constraints (e.g., <code>m, n &lt;= 100</code> means a DP table of <code>100x100 = 10,000</code> integers, which is small). Space optimization is primarily for very large grids where <code>m*n</code> could exceed memory limits.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def uniquePathsWithObstacles(self, obstacleGrid):
        """
        :type obstacleGrid: List[List[int]]
        :rtype: int
        """
        m = len(obstacleGrid)
        n = len(obstacleGrid[0])

        # If the starting cell is an obstacle, there are no paths.
        if obstacleGrid[0][0] == 1:
            return 0

        # Create a DP table to store the number of unique paths to each cell.
        # Initialize with zeros.
        dp = [[0] * n for _ in range(m)]

        # Base case: The starting cell has 1 way to reach itself (if not an obstacle).
        dp[0][0] = 1

        # Fill the first row
        for j in range(1, n):
            # If the current cell is not an obstacle and the previous cell in the row was reachable
            if obstacleGrid[0][j] == 0 and dp[0][j-1] == 1:
                dp[0][j] = 1
            # If it's an obstacle or the previous cell was unreachable, it remains 0

        # Fill the first column
        for i in range(1, m):
            # If the current cell is not an obstacle and the previous cell in the column was reachable
            if obstacleGrid[i][0] == 0 and dp[i-1][0] == 1:
                dp[i][0] = 1
            # If it's an obstacle or the previous cell was unreachable, it remains 0

        # Fill the rest of the DP table
        for i in range(1, m):
            for j in range(1, n):
                # If the current cell is an obstacle, no paths can go through it.
                if obstacleGrid[i][j] == 1:
                    dp[i][j] = 0
                else:
                    # The number of paths to (i, j) is the sum of paths from (i-1, j) (down)
                    # and paths from (i, j-1) (right).
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]

        # The result is the number of unique paths to the bottom-right corner.
        return dp[m-1][n-1]
```
