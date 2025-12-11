## Word Search
**Language:** python
**Tags:** dfs,backtracking,grid traversal,recursion

### Description:
<p>This code implements a solution to the classic "Word Search" problem using Depth-First Search (DFS) with backtracking.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to determine if a given <code>word</code> can be constructed by sequentially adjacent letters on a 2D <code>board</code> of characters. "Adjacent" means horizontally or vertically neighboring cells. A letter cell cannot be used more than once within the same word path.</p>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a recursive Depth-First Search (DFS) approach, starting from every possible cell on the board.</p>
<ul>
<li><strong>Initialization</strong>: It first gets the dimensions <code>m</code> (rows) and <code>n</code> (columns) of the <code>board</code>.</li>
<li><strong>Main Loop</strong>: It iterates through every cell <code>(r, c)</code> on the <code>board</code>. For each cell, it attempts to start a DFS traversal.<ul>
<li>If <code>dfs(r, c, 0)</code> returns <code>True</code> (meaning the word was found starting from <code>(r, c)</code>), the method immediately returns <code>True</code>.</li>
</ul>
</li>
<li><strong><code>dfs(r, c, k)</code> Function</strong>: This is the core recursive function.<ul>
<li><strong>Base Case 1 (Success)</strong>: If <code>k</code> (the current index in <code>word</code>) reaches <code>len(word)</code>, it means all characters of the word have been successfully matched in sequence, so it returns <code>True</code>.</li>
<li><strong>Base Case 2 (Failure)</strong>:<ul>
<li>If <code>(r, c)</code> is out of bounds (<code>0 &lt;= r &lt; m</code> or <code>0 &lt;= c &lt; n</code> is false).</li>
<li>Or if the character at <code>board[r][c]</code> does not match <code>word[k]</code>.</li>
<li>In either of these cases, it means this path cannot form the word, so it returns <code>False</code>.</li>
</ul>
</li>
<li><strong>Mark Visited</strong>: To prevent reusing the same cell in the current path, <code>board[r][c]</code> is temporarily changed to a special character (here, <code>'#'</code>). The original character is stored in <code>original_char</code> for restoration.</li>
<li><strong>Explore Neighbors</strong>: It recursively calls <code>dfs</code> for all four adjacent cells (<code>(r+1, c)</code>, <code>(r-1, c)</code>, <code>(r, c+1)</code>, <code>(r, c-1)</code>), incrementing <code>k</code> to look for the next character in <code>word</code>. It uses logical <code>or</code> because if <em>any</em> of these recursive calls finds the rest of the word, the current path is successful.</li>
<li><strong>Backtrack</strong>: After exploring all neighbors from <code>(r, c)</code>, the cell's character is restored to <code>original_char</code>. This is crucial for two reasons:<ol>
<li>Other DFS paths (e.g., starting from a different initial cell) might need to use this cell.</li>
<li>A different branch within the <em>current</em> DFS path might need to use this cell if the first branches failed.</li>
</ol>
</li>
<li><strong>Return Result</strong>: Finally, it returns <code>found</code>, which indicates whether any of the explored paths successfully completed the word.</li>
</ul>
</li>
<li><strong>Final Return</strong>: If the main loops complete without finding the word from any starting cell, it returns <code>False</code>.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm: Depth-First Search (DFS)</strong>:<ul>
<li><strong>Why</strong>: DFS is a natural fit for exploring all possible paths in a grid-like structure where each path must follow specific rules (adjacency, no re-use).</li>
</ul>
</li>
<li><strong>Visited Tracking: In-place Modification</strong>:<ul>
<li>The code marks visited cells by temporarily changing their value on the <code>board</code> to <code>'#'</code>.</li>
<li><strong>Trade-offs</strong>:<ul>
<li><strong>Pros</strong>: Saves auxiliary space that a separate <code>visited</code> 2D boolean array would require.</li>
<li><strong>Cons</strong>: Modifies the input <code>board</code>. However, the backtracking step (<code>board[r][c] = original_char</code>) ensures that the <code>board</code> is fully restored to its original state <em>after</em> each <code>dfs</code> call completes its exploration from a given <code>(r,c,k)</code> state, making it transparent to the caller of <code>exist</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Backtracking</strong>:<ul>
<li><strong>Why</strong>: Essential for correctness. Without backtracking, a cell marked as visited for one path would remain visited, incorrectly preventing other valid paths from using it. It allows the algorithm to "undo" its choices and explore alternative paths.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>M</code> be the number of rows, <code>N</code> be the number of columns in the <code>board</code>, and <code>L</code> be the length of the <code>word</code>.</p>
<ul>
<li><strong>Time Complexity</strong>: <code>O(M * N * 3^L)</code><ul>
<li><code>M * N</code>: In the worst case, the DFS might be initiated from every cell on the board.</li>
<li><code>3^L</code>: For each starting cell, the DFS goes <code>L</code> levels deep (the length of the word). At each step (except the first), there are up to 3 possible new directions to explore (since one direction is where the path came from, which cannot be immediately returned to). In the worst case, all <code>L</code> characters could potentially lead to branching.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(L)</code><ul>
<li>This is determined by the maximum depth of the recursion stack, which corresponds to the length of the <code>word</code>.</li>
<li>No significant auxiliary data structures are used beyond the input <code>board</code> and the recursion stack.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Board (<code>m=0</code> or <code>n=0</code>)</strong>:<ul>
<li>The code assumes <code>board</code> has at least one row and <code>board[0]</code> has at least one column (i.e., <code>m &gt;= 1</code> and <code>n &gt;= 1</code>). If <code>m=0</code>, <code>len(board)</code> is 0, the loops won't run, and it correctly returns <code>False</code>. If <code>board = [[]]</code> (m=1, n=0), <code>board[0]</code> is <code>[]</code>, and <code>len(board[0])</code> would be 0, <code>n</code> would be 0. The inner loop <code>for c in range(n)</code> won't run, and it correctly returns <code>False</code>.</li>
</ul>
</li>
<li><strong>Empty Word (<code>word=""</code>)</strong>:<ul>
<li>If <code>len(word)</code> is 0, then <code>k == len(word)</code> is true when <code>k=0</code>. The <code>dfs</code> call with <code>k=0</code> will immediately return <code>True</code>. This is generally considered correct, as an empty word can always be found.</li>
</ul>
</li>
<li><strong>Word Longer Than Board Cells (<code>L &gt; M*N</code>)</strong>:<ul>
<li>The <code>dfs</code> will naturally fail because it won't be able to match <code>L</code> distinct cells. The <code>k == len(word)</code> condition will not be met if <code>k</code> exceeds <code>M*N</code> before finding <code>word[k]</code>.</li>
</ul>
</li>
<li><strong>Single Character Word</strong>: Handled correctly by the base cases and recursion.</li>
<li><strong>Board with Identical Characters</strong>: The <code>board[r][c] = '#'</code> (mark visited) combined with backtracking ensures that a cell is not reused in a single path, even if it has the same character as others.</li>
<li><strong>Word Not Found</strong>: If no path from any starting cell forms the word, the method correctly returns <code>False</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>:<ul>
<li><strong>Direction Arrays</strong>: Instead of four separate <code>dfs</code> calls, one could use <code>dr = [0, 0, 1, -1]</code> and <code>dc = [1, -1, 0, 0]</code> arrays to loop through directions, making the code more concise:<pre><code class="language-python"># ... inside dfs ...
for x, y in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
    if dfs(r + x, c + y, k + 1):
        found = True
        break # Optimization: if one direction finds it, no need to check others
# ...
</code></pre>
</li>
<li><strong>Named Visited Token</strong>: Use a constant variable for the visited marker (e.g., <code>_VISITED_TOKEN = '#'</code>) instead of a literal string.</li>
</ul>
</li>
<li><strong>Space (Alternative for Visited Tracking)</strong>:<ul>
<li>Instead of modifying the input <code>board</code>, an explicit <code>visited = [[False] * n for _ in range(m)]</code> 2D boolean array could be used. This would consume <code>O(M*N)</code> additional space but avoid altering the input object. The <code>dfs</code> function would then pass <code>visited</code> along and update/reset cells in it.</li>
</ul>
</li>
<li><strong>Performance (Potential Pruning)</strong>:<ul>
<li><strong>Character Frequency Check</strong>: Before starting the DFS, check if the frequency of each character in <code>word</code> is less than or equal to its frequency on the <code>board</code>. If not, return <code>False</code> immediately. This is a common optimization for problems like this.</li>
<li><strong>Start Point Optimization</strong>: If the first character <code>word[0]</code> is rare, you might filter initial <code>(r, c)</code> candidates more efficiently.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Recursion Depth</strong>: Python has a default recursion limit (e.g., 1000 or 3000). For extremely long words (<code>L</code>), this limit could be hit. While competitive programming problems typically keep <code>L</code> small enough, in real-world scenarios, an iterative DFS (using an explicit stack) might be preferred or the recursion limit increased (<code>sys.setrecursionlimit</code>).</li>
<li><strong>Input Modification Transparency</strong>: Although the <code>board</code> is modified temporarily, the backtracking mechanism ensures that by the time the <code>exist</code> method returns to its caller, the <code>board</code> is fully restored to its original state. This is a good practice as it doesn't leave side effects on the input data for the caller.</li>
</ul>


### Code:
```python
class Solution(object):
    def exist(self, board, word):
        """
        :type board: List[List[str]]
        :type word: str
        :rtype: bool
        """
        m = len(board)
        n = len(board[0])

        def dfs(r, c, k):
            # Base case: if k reaches the length of word, it means we found all characters
            if k == len(word):
                return True

            # Check boundary conditions and if current cell character matches word[k]
            if not (0 <= r < m and 0 <= c < n) or board[r][c] != word[k]:
                return False

            # Mark the current cell as visited by changing its character
            original_char = board[r][c]
            board[r][c] = '#'

            # Explore all four possible directions
            found = (dfs(r + 1, c, k + 1) or
                     dfs(r - 1, c, k + 1) or
                     dfs(r, c + 1, k + 1) or
                     dfs(r, c - 1, k + 1))

            # Backtrack: restore the original character of the cell
            board[r][c] = original_char

            return found

        # Iterate through each cell of the board to start the DFS
        for r in range(m):
            for c in range(n):
                if dfs(r, c, 0): # Start DFS from (r, c) looking for the first character of word
                    return True

        return False
```
