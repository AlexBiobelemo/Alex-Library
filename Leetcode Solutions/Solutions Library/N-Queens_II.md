## N-Queens II
**Language:** python
**Tags:** python,backtracking,n-queens,recursion,sets

### Description:
<p>This Python code provides a solution to the classic N-Queens problem, aiming to count the total number of distinct ways to place <code>n</code> non-attacking queens on an <code>n x n</code> chessboard.</p>
<h3>1. Overview &amp; Intent</h3>
<p>The code's purpose is to find the total number of configurations where <code>n</code> queens can be placed on an <code>n x n</code> board such that no two queens attack each other. A queen can attack horizontally, vertically, and diagonally. The solution employs a backtracking algorithm to systematically explore all possible placements.</p>
<h3>2. How It Works</h3>
<p>The core of the solution is a recursive <code>backtrack</code> function:</p>
<ul>
<li><strong>Initialization</strong>: A counter <code>self.count</code> is initialized to 0. Three sets (<code>cols</code>, <code>diag1</code>, <code>diag2</code>) are created to efficiently keep track of occupied columns, main diagonals, and anti-diagonals, respectively.</li>
<li><strong><code>backtrack(row)</code> Function</strong>: This function attempts to place a queen in the given <code>row</code>.<ul>
<li><strong>Base Case</strong>: If <code>row == n</code>, it means <code>n</code> queens have been successfully placed in <code>n</code> distinct rows without attacking each other. A valid configuration has been found, so <code>self.count</code> is incremented, and the function returns.</li>
<li><strong>Iterating Columns</strong>: For the current <code>row</code>, the function iterates through each <code>col</code> from <code>0</code> to <code>n-1</code>.</li>
<li><strong>Safety Check</strong>: For each <code>(row, col)</code> position, it checks if placing a queen there would conflict with any previously placed queens using the three sets:<ul>
<li><code>col in cols</code>: Checks if the column is already occupied.</li>
<li><code>(row - col) in diag1</code>: Checks if the main diagonal is occupied.</li>
<li><code>(row + col) in diag2</code>: Checks if the anti-diagonal is occupied.</li>
<li>If any of these conditions are true, the position is unsafe, and the loop continues to the next column.</li>
</ul>
</li>
<li><strong>Place Queen (Recursive Step)</strong>: If the position <code>(row, col)</code> is safe:<ul>
<li>The column and diagonal identifiers (<code>col</code>, <code>row - col</code>, <code>row + col</code>) are added to their respective sets, marking them as occupied.</li>
<li>The <code>backtrack</code> function is called recursively for the <code>next row</code> (<code>row + 1</code>).</li>
</ul>
</li>
<li><strong>Backtrack (Unplace Queen)</strong>: After the recursive call returns (meaning all possibilities stemming from placing a queen at <code>(row, col)</code> have been explored), the queen is "unplaced". The column and diagonal identifiers are removed from their sets. This is crucial to allow other branches of the search tree to use these positions.</li>
</ul>
</li>
<li><strong>Starting Point</strong>: The process begins by calling <code>backtrack(0)</code>, attempting to place the first queen in <code>row 0</code>.</li>
<li><strong>Result</strong>: Finally, <code>self.count</code> holds the total number of solutions found.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Backtracking Algorithm</strong>: This is a natural fit for combinatorial problems like N-Queens, systematically exploring all potential solutions by building a solution step-by-step and undoing choices when they lead to a dead end.</li>
<li><strong>Sets for Conflict Detection</strong>: Using Python <code>set</code> objects (<code>cols</code>, <code>diag1</code>, <code>diag2</code>) is a highly efficient choice.<ul>
<li><code>add()</code>, <code>remove()</code>, and <code>in</code> operations on sets have an average time complexity of O(1), making conflict checks very fast.</li>
<li><code>cols</code>: A simple way to track occupied columns.</li>
<li><code>diag1</code> (main diagonals): Queens on the same main diagonal always have the same <code>row - col</code> value.</li>
<li><code>diag2</code> (anti-diagonals): Queens on the same anti-diagonal always have the same <code>row + col</code> value.</li>
</ul>
</li>
<li><strong>In-place State Modification</strong>: The <code>cols</code>, <code>diag1</code>, and <code>diag2</code> sets are modified directly within the <code>backtrack</code> function. This makes the state management explicit and tied to the recursive calls.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: Approximately <strong>O(N!)</strong>.<ul>
<li>In the worst case, a backtracking algorithm for N-Queens explores a search space whose size is roughly proportional to <code>N!</code>.</li>
<li>At each level of recursion (each row), the algorithm iterates through <code>N</code> columns. Inside the loop, set operations (<code>add</code>, <code>remove</code>, <code>in</code>) take average O(1) time.</li>
<li>While the <code>N!</code> estimate is an upper bound on the number of potential <em>partial</em> solutions, the pruning significantly reduces the actual states visited. For <code>N=15</code>, it's in the realm of tens of millions of states, not billions.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <strong>O(N)</strong>.<ul>
<li><strong>Recursion Stack</strong>: The maximum depth of the recursion is <code>N</code> (one call for each row).</li>
<li><strong>Sets</strong>: Each of the three sets (<code>cols</code>, <code>diag1</code>, <code>diag2</code>) will store at most <code>N</code> elements (representing the <code>N</code> queens placed).</li>
<li>Therefore, the total space required scales linearly with <code>N</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n = 0</code></strong>: The problem typically implies <code>n &gt;= 1</code>. If <code>n=0</code> were allowed, <code>range(n)</code> would be empty, <code>backtrack(0)</code> would never enter the <code>for</code> loop, and <code>self.count</code> would remain 0. This is technically correct as there are no queens to place.</li>
<li><strong><code>n = 1</code></strong>: <code>backtrack(0)</code> places a queen at <code>(0,0)</code>, calls <code>backtrack(1)</code>. <code>row == n</code> becomes <code>True</code>, <code>self.count</code> increments to 1. Correct.</li>
<li><strong><code>n = 2, 3</code></strong>: These values of <code>n</code> have zero solutions. The code will correctly find no paths to <code>row == n</code> and return <code>self.count = 0</code>.</li>
<li><strong>General Correctness</strong>: The use of sets with <code>row - col</code> and <code>row + col</code> to correctly identify occupied diagonals, combined with the <code>cols</code> set for columns, precisely models the non-attacking conditions. The <code>add</code>/<code>remove</code> operations correctly manage the state during backtracking, ensuring that choices are undone properly for alternative paths.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Bit Manipulation for Performance</strong>: For <code>n</code> values up to 32 (or 64 on 64-bit systems), bitmasks can represent the occupied columns and diagonals. This can offer a significant performance boost in some languages (like C++ or Java) by replacing set operations with faster bitwise operations (<code>&amp;</code>, <code>|</code>, <code>^</code>). Python's arbitrary-precision integers and efficient <code>set</code> implementation mitigate the performance gap, but bit manipulation can still be faster.</li>
<li><strong>Symmetry Optimization</strong>: For larger <code>n</code>, the search space can be further reduced by exploiting symmetry. For example, for <code>n &gt; 1</code>, one can place the first queen only in the first <code>n/2</code> or <code>(n+1)/2</code> columns and then multiply the resulting counts by appropriate factors (e.g., 2 for horizontal reflection, and sometimes more for rotational symmetry), being careful to avoid double-counting solutions that are symmetrical to themselves. This adds significant complexity to the implementation.</li>
<li><strong>Explicit State Passing</strong>: Instead of using <code>self.count</code> and enclosing <code>cols</code>, <code>diag1</code>, <code>diag2</code> for <code>backtrack</code>, these could be passed as arguments. This makes the <code>backtrack</code> function "purer" (stateless, except for its arguments) and potentially easier to test in isolation, though Python's closures are idiomatic and efficient here.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The N-Queens problem is a classic example of a problem with exponential time complexity. For <code>n</code> values above ~15, the computation time can become very significant, growing rapidly. While the current solution is optimal in terms of algorithmic approach (backtracking with efficient pruning), fundamental computational limits for larger <code>n</code> remain.</li>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this code. It performs purely computational tasks without external input handling or file/network operations.</li>
</ul>


### Code:
```python
class Solution(object):
    def totalNQueens(self, n):
        """
        :type n: int
        :rtype: int
        """
        self.count = 0
        
        # Sets to keep track of occupied columns and diagonals
        # cols: stores column indices that are occupied by a queen
        # diag1: stores (row - col) values for main diagonals that are occupied
        # diag2: stores (row + col) values for anti-diagonals that are occupied
        cols = set()
        diag1 = set() # r - c
        diag2 = set() # r + c

        def backtrack(row):
            # Base case: if all queens are placed (i.e., we've successfully placed a queen in 'n' rows)
            if row == n:
                self.count += 1
                return

            # Try placing a queen in each column of the current row
            for col in range(n):
                # Check if the current position (row, col) is safe
                # A position is safe if no other queen occupies:
                # 1. The same column (col in cols)
                # 2. The same main diagonal (row - col is constant)
                # 3. The same anti-diagonal (row + col is constant)
                if col in cols or (row - col) in diag1 or (row + col) in diag2:
                    continue # This position is not safe, try next column

                # If safe, place the queen:
                # Mark the column and diagonals as occupied
                cols.add(col)
                diag1.add(row - col)
                diag2.add(row + col)

                # Recurse to place a queen in the next row
                backtrack(row + 1)

                # Backtrack: Remove the queen from the current position
                # Unmark the column and diagonals so they can be used by other paths
                cols.remove(col)
                diag1.remove(row - col)
                diag2.remove(row + col)
        
        # Start the backtracking process from the first row (row 0)
        backtrack(0)
        
        return self.count
```
