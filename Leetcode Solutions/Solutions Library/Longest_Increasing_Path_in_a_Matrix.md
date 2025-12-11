## Longest Increasing Path in a Matrix
**Language:** python
**Tags:** dfs,dynamic programming,grid,memoization,pathfinding

### Description:
<p>The provided Python code finds the length of the longest increasing path in a given integer matrix. It uses a combination of Depth-First Search (DFS) and memoization (dynamic programming) to efficiently solve the problem.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>What it does:</strong> The code identifies the maximum length of a path within a 2D integer matrix where each step moves to an adjacent cell (up, down, left, or right) with a strictly greater value.</li>
<li><strong>Why it's used:</strong> This is a classic graph traversal problem that demonstrates the power of dynamic programming and memoization to optimize recursive solutions with overlapping subproblems. It's often encountered in algorithm design and interview contexts.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a recursive Depth-First Search (DFS) function <code>dfs</code> combined with memoization to avoid redundant computations.</p>
<ol>
<li><strong>Initialization:</strong><ul>
<li>It handles edge cases for empty or invalid matrices, returning 0.</li>
<li><code>rows</code> and <code>cols</code> store matrix dimensions.</li>
<li><code>memo</code> is a dictionary used to cache the results of <code>dfs(r, c)</code>, storing the longest increasing path length starting from <code>(r, c)</code>.</li>
<li><code>max_path</code> tracks the overall maximum path length found.</li>
<li><code>directions</code> defines the possible moves (up, down, left, right).</li>
</ul>
</li>
<li><strong><code>dfs(r, c)</code> Function (Recursive Helper):</strong><ul>
<li><strong>Memoization Check:</strong> Before any computation, it checks if the result for <code>(r, c)</code> is already in <code>memo</code>. If so, it immediately returns the cached value.</li>
<li><strong>Base Case:</strong> <code>current_max_len</code> is initialized to 1, representing the path containing just the current cell <code>(r, c)</code>.</li>
<li><strong>Explore Neighbors:</strong> It iterates through all four <code>directions</code>. For each potential neighbor <code>(nr, nc)</code>:<ul>
<li>It verifies that <code>(nr, nc)</code> is within the matrix bounds.</li>
<li>It checks if <code>matrix[nr][nc]</code> is strictly greater than <code>matrix[r][c]</code>. This is the core condition for an increasing path.</li>
</ul>
</li>
<li><strong>Recursive Call:</strong> If a neighbor <code>(nr, nc)</code> meets these conditions, it recursively calls <code>dfs(nr, nc)</code> to find the longest increasing path starting from that neighbor. The result is then incremented by 1 (to include the current cell) and <code>current_max_len</code> is updated with the maximum found so far.</li>
<li><strong>Cache Result:</strong> After exploring all neighbors, the computed <code>current_max_len</code> for <code>(r, c)</code> is stored in <code>memo[(r, c)]</code> before being returned.</li>
</ul>
</li>
<li><strong>Main Loop:</strong><ul>
<li>The code iterates through <em>every</em> cell <code>(r, c)</code> in the matrix.</li>
<li>For each cell, it calls <code>dfs(r, c)</code> to find the longest increasing path starting from that particular cell.</li>
<li><code>max_path</code> is continuously updated with the maximum value returned by any <code>dfs</code> call.</li>
</ul>
</li>
<li><strong>Return:</strong> Finally, <code>max_path</code> (the overall longest increasing path length) is returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Depth-First Search (DFS):</strong> DFS is a natural choice for exploring paths in a graph-like structure (the matrix with adjacent cells forming edges). It allows for a straightforward recursive definition of path length.</li>
<li><strong>Memoization (Dynamic Programming):</strong> The <code>memo</code> dictionary is critical. The problem has <em>overlapping subproblems</em> (the longest path from a cell might be needed by multiple preceding cells) and <em>optimal substructure</em> (the longest path from a cell is 1 + the longest path from one of its valid neighbors). Memoization avoids re-computing these subproblems, transforming an exponential solution into a polynomial one.</li>
<li><strong>Implicit Directed Acyclic Graph (DAG):</strong> The problem effectively transforms the matrix into a DAG where edges go from smaller values to larger adjacent values. Finding the longest increasing path is equivalent to finding the longest path in this DAG, which DFS with memoization handles effectively.</li>
<li><strong><code>directions</code> Array:</strong> This array simplifies the logic for checking adjacent cells, making the code cleaner and easier to modify if movement rules were to change.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(R * C)</strong><ul>
<li>Each cell <code>(r, c)</code> in the <code>R x C</code> matrix will have its <code>dfs</code> function computed <em>at most once</em> due to memoization.</li>
<li>During each <code>dfs</code> call's initial computation, it performs constant work (memo check, initialization) and iterates through at most 4 neighbors. For each neighbor, it does bounds checks and a value comparison.</li>
<li>Since each cell's <code>dfs</code> is computed only once and takes constant time relative to the number of neighbors, the total time complexity is proportional to the number of cells.</li>
</ul>
</li>
<li><strong>Space Complexity: O(R * C)</strong><ul>
<li><strong>Memoization Table (<code>memo</code>):</strong> In the worst case, the <code>memo</code> dictionary will store an entry for every cell in the <code>R x C</code> matrix, requiring <code>O(R * C)</code> space.</li>
<li><strong>Recursion Stack:</strong> In the worst-case scenario (e.g., a matrix where the longest path snakes through almost all cells), the DFS recursion stack depth can go up to <code>O(R * C)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Matrix <code>([], [[]])</code></strong>: The initial check <code>if not matrix or not matrix[0]: return 0</code> correctly handles this, returning 0.</li>
<li><strong>Single Cell Matrix <code>([[5]])</code></strong>: The <code>dfs(0, 0)</code> call will initialize <code>current_max_len = 1</code>. No neighbors will satisfy the strictly greater condition, so it correctly returns 1. <code>max_path</code> becomes 1.</li>
<li><strong>Matrix with No Increasing Paths (e.g., all same values <code>[[1,1],[1,1]]</code> or strictly decreasing <code>[[9,8],[7,6]]</code>)</strong>: For any cell, the condition <code>matrix[nr][nc] &gt; matrix[r][c]</code> will always be false for all neighbors. Thus, <code>dfs</code> for any cell will simply return 1 (the cell itself). <code>max_path</code> will correctly be 1.</li>
<li><strong>Multiple Longest Paths</strong>: The algorithm correctly finds the <em>length</em> of <em>a</em> longest path by always taking the maximum, even if multiple distinct paths achieve that maximum length.</li>
<li><strong>Correctness of Memoization</strong>: By checking <code>if (r, c) in memo:</code> first, the code ensures that subproblems are solved only once, preventing redundant computations and guaranteeing the <code>O(R*C)</code> time complexity.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong><code>functools.lru_cache</code>:</strong> Python's standard library offers <code>@functools.lru_cache(None)</code> as a decorator to automatically memoize a function. This can make the code more concise and remove explicit <code>memo</code> dictionary management.<pre><code class="language-python">from functools import lru_cache
# ... inside class Solution ...
@lru_cache(None) # Caches all results
def dfs(r, c):
    # ... rest of dfs logic, remove manual memo access ...
</code></pre>
</li>
<li><strong>Iterative Dynamic Programming / Topological Sort:</strong> While DFS with memoization is a form of DP, an explicit iterative DP approach could be devised. This often involves:<ol>
<li>Building an "out-degree" matrix or similar structure for the reverse graph (cells that can lead <em>to</em> the current cell).</li>
<li>Using a multi-source BFS-like approach, starting from cells that have no smaller neighbors (local minima), to build up the path lengths. This can avoid recursion depth limits.</li>
</ol>
</li>
<li><strong>Type Hinting for <code>memo</code>:</strong> For better clarity and maintainability, explicitly type hint the <code>memo</code> dictionary: <code>memo: Dict[Tuple[int, int], int] = {}</code>.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Recursion Depth Limit:</strong> For very large matrices (e.g., 1000x1000 or larger), the default Python recursion limit (typically 1000 or 3000) could be exceeded if the longest path is very long. This would lead to a <code>RecursionError</code>.<ul>
<li><strong>Mitigation:</strong> For competitive programming or specific high-constraint environments, one might need to increase the recursion limit (<code>sys.setrecursionlimit()</code>) or convert the solution to an iterative DP approach.</li>
</ul>
</li>
<li><strong>Memory Usage:</strong> While <code>O(R*C)</code> space complexity is optimal for this problem, for extremely large matrices, the <code>memo</code> dictionary can consume significant memory. For example, a 10,000 x 10,000 matrix would theoretically require 100 million entries, which would likely exhaust system memory. In practical interview settings, matrix sizes are usually constrained to prevent this.</li>
</ul>


### Code:
```python
from typing import List

class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])
        memo = {}  # Stores the length of the longest increasing path starting from (r, c)
        max_path = 0

        # Directions for moving up, down, left, right
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

        def dfs(r, c):
            if (r, c) in memo:
                return memo[(r, c)]

            # The path starting from (r, c) is at least 1 (the cell itself)
            current_max_len = 1

            for dr, dc in directions:
                nr, nc = r + dr, c + dc

                # Check bounds and increasing value condition
                if 0 <= nr < rows and 0 <= nc < cols and matrix[nr][nc] > matrix[r][c]:
                    current_max_len = max(current_max_len, 1 + dfs(nr, nc))

            memo[(r, c)] = current_max_len
            return current_max_len

        # Iterate through each cell to find the longest path starting from anywhere
        for r in range(rows):
            for c in range(cols):
                max_path = max(max_path, dfs(r, c))

        return max_path
```
