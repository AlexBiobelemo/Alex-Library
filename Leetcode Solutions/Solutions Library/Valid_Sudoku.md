## Valid Sudoku
**Language:** python
**Tags:** python,sudoku,validation,grid,set

### Description:
<p>This code snippet provides a function to determine if a given Sudoku board is valid according to the classic Sudoku rules (numbers 1-9 must appear only once in each row, column, and 3x3 sub-box). Empty cells are denoted by '.'.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: The <code>isValidSudoku</code> function checks if the current state of a 9x9 Sudoku <code>board</code> is valid.</li>
<li><strong>Validation Scope</strong>: It only verifies if the <em>already placed</em> numbers adhere to Sudoku rules. It does <em>not</em> check if the Sudoku puzzle is solvable or complete.</li>
<li><strong>Application</strong>: Commonly used as a helper function in Sudoku solvers, game validation logic, or input checkers for Sudoku puzzles.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm iterates through each cell of the 9x9 Sudoku board and performs three checks for every non-empty cell:</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li>Three lists of sets are created: <code>rows</code>, <code>cols</code>, and <code>boxes</code>. Each list contains 9 empty sets.</li>
<li><code>rows[i]</code> will store numbers seen in row <code>i</code>.</li>
<li><code>cols[j]</code> will store numbers seen in column <code>j</code>.</li>
<li><code>boxes[k]</code> will store numbers seen in box <code>k</code>.</li>
</ul>
</li>
<li><p><strong>Board Traversal</strong>:</p>
<ul>
<li>The code uses nested loops to iterate through each row <code>r</code> (0 to 8) and each column <code>c</code> (0 to 8) of the <code>board</code>.</li>
</ul>
</li>
<li><p><strong>Cell Processing</strong>:</p>
<ul>
<li>For each cell <code>(r, c)</code>, it retrieves the <code>num</code>.</li>
<li>If <code>num</code> is '.', it represents an empty cell, which by definition doesn't violate any rules, so the loop <code>continue</code>s to the next cell.</li>
</ul>
</li>
<li><p><strong>Rule Checks (and Update)</strong>:</p>
<ul>
<li><strong>Row Check</strong>: It checks if <code>num</code> is already present in <code>rows[r]</code>. If it is, a duplicate is found in that row, so the board is invalid, and <code>False</code> is returned immediately. Otherwise, <code>num</code> is added to <code>rows[r]</code>.</li>
<li><strong>Column Check</strong>: Similarly, it checks if <code>num</code> is present in <code>cols[c]</code>. If a duplicate is found, <code>False</code> is returned. Otherwise, <code>num</code> is added to <code>cols[c]</code>.</li>
<li><strong>Box Check</strong>:<ul>
<li>The <code>box_idx</code> (0-8) for the current cell <code>(r, c)</code> is calculated using <code>(r // 3) * 3 + (c // 3)</code>. This formula correctly maps the 9 3x3 sub-grids to a single index.</li>
<li>It then checks if <code>num</code> is present in <code>boxes[box_idx]</code>. If a duplicate is found, <code>False</code> is returned. Otherwise, <code>num</code> is added to <code>boxes[box_idx]</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Final Result</strong>: If the entire board is traversed without encountering any rule violations, the function returns <code>True</code>.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structures</strong>:<ul>
<li><strong><code>List[List[str]]</code> for <code>board</code></strong>: Standard and intuitive representation for a 2D grid. The use of strings for digits '1'-'9' and '.' is as per typical LeetCode problem statements.</li>
<li><strong><code>List[set]</code> for <code>rows</code>, <code>cols</code>, <code>boxes</code></strong>:<ul>
<li><strong>Sets</strong>: Chosen for their efficient average O(1) time complexity for <code>add()</code> (insertion) and <code>in</code> (membership test) operations. This is critical for quickly checking for duplicates.</li>
<li><strong>List of Sets</strong>: Allows direct indexing (e.g., <code>rows[r]</code>) to access the specific collection of numbers for a given row, column, or 3x3 box.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Algorithm</strong>:<ul>
<li><strong>Single Pass</strong>: The algorithm processes each cell of the board exactly once, making it efficient.</li>
<li><strong>Early Exit</strong>: The function returns <code>False</code> as soon as any rule violation is detected. This avoids unnecessary computations and is a common optimization.</li>
<li><strong>Box Indexing</strong>: The formula <code>(r // 3) * 3 + (c // 3)</code> is a standard and correct way to map a 2D coordinate <code>(r, c)</code> to its corresponding 1D 3x3 sub-grid index (0-8).</li>
</ul>
</li>
<li><strong>Trade-offs</strong>:<ul>
<li><strong>Space vs. Time</strong>: The use of auxiliary space (three lists of sets) allows for constant-time (average) checks and insertions for each cell, leading to an overall efficient time complexity. Without this auxiliary space, one would have to re-scan rows, columns, and boxes for each cell, drastically increasing time complexity. This is a very common and effective trade-off in many algorithmic problems.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the side length of the Sudoku board (here, <code>N=9</code>). The total number of cells is <code>N*N</code>.</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><strong>Initialization</strong>: Creating <code>3 * N</code> empty sets takes O(N) time (e.g., 9 sets for rows, 9 for columns, 9 for boxes).</li>
<li><strong>Main Loop</strong>: The nested loops iterate <code>N*N</code> times (81 times for a 9x9 board).</li>
<li><strong>Inside the Loop</strong>:<ul>
<li>Accessing <code>board[r][c]</code>: O(1).</li>
<li>Set membership check (<code>num in set</code>): Average O(1). In the worst case (hash collisions), it can be O(k) where k is the number of elements in the set, but <code>k</code> is at most <code>N</code> (9) here, so practically O(1) on average.</li>
<li>Set addition (<code>set.add(num)</code>): Average O(1).</li>
<li>Arithmetic for <code>box_idx</code>: O(1).</li>
</ul>
</li>
<li><strong>Total Time</strong>: The overall time complexity is dominated by the <code>N*N</code> iterations, each performing constant-time operations. Thus, the time complexity is <strong>O(N^2)</strong>. For a fixed N=9, this is effectively O(1) (constant time, as it's 81 iterations).</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><strong><code>rows</code></strong>: A list of <code>N</code> sets, each potentially storing up to <code>N</code> unique numbers.</li>
<li><strong><code>cols</code></strong>: A list of <code>N</code> sets, each potentially storing up to <code>N</code> unique numbers.</li>
<li><strong><code>boxes</code></strong>: A list of <code>N</code> sets, each potentially storing up to <code>N</code> unique numbers.</li>
<li><strong>Total Space</strong>: In the worst case, each set could contain <code>N</code> elements (e.g., if the board is valid and full). Therefore, the total space complexity is roughly <code>3 * N * N</code> elements. This simplifies to <strong>O(N^2)</strong>. For a fixed N=9, this is effectively O(1) (constant space, as it's 3 * 9 * 9 = 243 string references).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Board (all '.')</strong>:<ul>
<li><strong>Correctness</strong>: The code correctly returns <code>True</code>. No numbers are present to violate any rules.</li>
</ul>
</li>
<li><strong>Board with one duplicate</strong>:<ul>
<li><strong>Correctness</strong>: The code will detect the first duplicate it encounters (in row, column, or box) and immediately return <code>False</code>. This is correct.</li>
</ul>
</li>
<li><strong>Completely Valid Board</strong>:<ul>
<li><strong>Correctness</strong>: If all numbers adhere to the rules, all checks will pass, and the function will iterate through the entire board and return <code>True</code>. This is correct.</li>
</ul>
</li>
<li><strong>Input Constraints</strong>: The problem implies digits are '1'-'9' and empty cells are '.'. The solution correctly handles these string values in sets. If other characters were possible, robust input validation might be needed.</li>
<li><strong>Implicit Rule</strong>: The problem implicitly assumes a 9x9 board. The code is hardcoded for this dimension (using <code>range(9)</code> and <code>// 3</code>). This aligns with standard Sudoku rules.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>:<ul>
<li><strong>Constants</strong>: For improved clarity and maintainability, especially if the board size could vary (though not typical for Sudoku), one could define constants:<pre><code class="language-python">BOARD_SIZE = 9
SUBGRID_SIZE = 3
# ...
rows = [set() for _ in range(BOARD_SIZE)]
# ...
for r in range(BOARD_SIZE):
    for c in range(BOARD_SIZE):
        # ...
        box_idx = (r // SUBGRID_SIZE) * SUBGRID_SIZE + (c // SUBGRID_SIZE)
</code></pre>
</li>
</ul>
</li>
<li><strong>Performance (Minor/Advanced)</strong>:<ul>
<li><strong>Bit Manipulation</strong>: For languages that allow efficient bitwise operations (like C++, Java), one could use integers as bitmasks instead of sets. Each bit in a 9-bit integer could represent the presence of a digit (1-9). This can offer marginal performance gains by reducing object overhead and potentially using less memory. For Python, <code>set</code> operations are highly optimized, so the benefit might not be significant enough to justify the decreased readability and increased complexity of bit manipulation.</li>
</ul>
</li>
<li><strong>Memory (Minor)</strong>:<ul>
<li>If memory were extremely constrained, and only digits '1'-'9' were allowed, one could use boolean arrays (<code>seen[digit-1] = True</code>) instead of sets. However, Python sets are generally memory-efficient for small, sparse collections.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: This function is purely for validation and processes an internal data structure. It does not interact with external systems, user input directly (beyond the <code>board</code> parameter), or files, so there are no direct security implications or vulnerabilities like injection attacks.</li>
<li><strong>Performance</strong>: The current implementation is highly performant for the given constraints (9x9 board).<ul>
<li>The use of <code>set</code> for O(1) average time complexity for lookups and insertions is optimal for this problem.</li>
<li>The <code>O(N^2)</code> time complexity for <code>N=9</code> means it executes a very small, fixed number of operations (roughly 81 iterations * 3 checks/adds per iteration), resulting in extremely fast execution, typically in microseconds or milliseconds.</li>
<li>The <code>O(N^2)</code> space complexity for <code>N=9</code> implies a small, fixed amount of memory is used, which is negligible for modern systems.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def isValidSudoku(self, board):
        """
        :type board: List[List[str]]
        :rtype: bool
        """
        rows = [set() for _ in range(9)]
        cols = [set() for _ in range(9)]
        boxes = [set() for _ in range(9)]

        for r in range(9):
            for c in range(9):
                num = board[r][c]
                if num == '.':
                    continue

                # Check row
                if num in rows[r]:
                    return False
                rows[r].add(num)

                # Check column
                if num in cols[c]:
                    return False
                cols[c].add(num)

                # Check 3x3 box
                box_idx = (r // 3) * 3 + (c // 3)
                if num in boxes[box_idx]:
                    return False
                boxes[box_idx].add(num)
        
        return True
```
