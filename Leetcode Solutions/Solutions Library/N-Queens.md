## N-Queens
**Language:** python
**Tags:** n-queens,backtracking,recursion,sets

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code solves the classic N-Queens puzzle.</p>
<ul>
<li><strong>Problem</strong>: Given an integer <code>n</code>, find all distinct solutions to placing <code>n</code> non-attacking queens on an <code>n x n</code> chessboard.</li>
<li><strong>Goal</strong>: A queen can attack horizontally, vertically, and diagonally. The objective is to place <code>n</code> queens such that no two queens threaten each other.</li>
<li><strong>Output</strong>: A list of all possible board configurations. Each configuration is represented as a list of strings, where each string corresponds to a row on the board, with 'Q' for a queen and '.' for an empty square.</li>
</ul>
<h3>2. How It Works</h3>
<p>The solution employs a <strong>backtracking algorithm</strong> to explore possible queen placements.</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li><code>queens_pos</code>: An array of size <code>n</code>, where <code>queens_pos[r]</code> stores the column index of the queen placed in row <code>r</code>. Initialized with <code>-1</code> (no queen).</li>
<li><code>col_occupied</code>: A set to track columns that already have a queen.</li>
<li><code>diag_occupied</code>: A set to track main diagonals that already have a queen. A main diagonal is identified by <code>row - col</code>.</li>
<li><code>anti_diag_occupied</code>: A set to track anti-diagonals that already have a queen. An anti-diagonal is identified by <code>row + col</code>.</li>
<li><code>solutions</code>: A list to collect all valid board configurations found.</li>
</ul>
</li>
<li><p><strong><code>backtrack(row)</code> Function</strong>: This is the core recursive function that attempts to place a queen in the current <code>row</code>.</p>
<ul>
<li><strong>Base Case</strong>: If <code>row == n</code>, it means <code>n</code> queens have been successfully placed in all <code>n</code> rows. A valid solution has been found.<ul>
<li>The board configuration is constructed from <code>queens_pos</code> (iterating through each row and placing 'Q' at <code>queens_pos[r]</code>).</li>
<li>This <code>current_board</code> is added to the <code>solutions</code> list.</li>
<li>The function then returns.</li>
</ul>
</li>
<li><strong>Recursive Step</strong>: For the current <code>row</code>, the function iterates through each <code>col</code> from <code>0</code> to <code>n-1</code>.<ul>
<li><strong>Safety Check</strong>: It checks if placing a queen at <code>(row, col)</code> is safe. This means checking if <code>col</code> is not in <code>col_occupied</code>, <code>(row - col)</code> is not in <code>diag_occupied</code>, and <code>(row + col)</code> is not in <code>anti_diag_occupied</code>. These set lookups are efficient.</li>
<li><strong>Place Queen</strong>: If safe:<ul>
<li>Record the queen's position: <code>queens_pos[row] = col</code>.</li>
<li>Mark the column, main diagonal, and anti-diagonal as occupied by adding <code>col</code>, <code>row - col</code>, and <code>row + col</code> to their respective sets.</li>
<li><strong>Recurse</strong>: Call <code>backtrack(row + 1)</code> to try placing a queen in the next row.</li>
</ul>
</li>
<li><strong>Backtrack (Undo)</strong>: After the recursive call returns (meaning all possibilities for the current placement have been explored), the queen is "removed" by:<ul>
<li>Removing <code>col</code>, <code>row - col</code>, and <code>row + col</code> from their respective sets.</li>
<li>Resetting <code>queens_pos[row] = -1</code>. This allows the loop to try placing a queen in the next column of the current <code>row</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Initiation</strong>: The backtracking process starts by calling <code>backtrack(0)</code> to begin placing queens from the first row (row index 0).</p>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Backtracking</strong>: This is the standard and most intuitive algorithmic paradigm for solving combinatorial problems like N-Queens, where partial solutions are built incrementally, and invalid paths are pruned.</li>
<li><strong>State Representation with Sets</strong>:<ul>
<li>Using separate <code>set</code> objects (<code>col_occupied</code>, <code>diag_occupied</code>, <code>anti_diag_occupied</code>) for tracking occupied positions is a highly efficient choice. Set lookups, insertions, and deletions have an average time complexity of O(1), making the safety check very fast.</li>
<li>The use of <code>row - col</code> and <code>row + col</code> to uniquely identify main diagonals and anti-diagonals, respectively, is a classic and elegant trick in N-Queens solutions.</li>
</ul>
</li>
<li><strong><code>queens_pos</code> Array</strong>: Storing only the column index for each row (<code>queens_pos[row] = col</code>) is memory-efficient compared to maintaining a full <code>n x n</code> board array during recursion. The full board representation is only constructed when a complete solution is found.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li>The N-Queens problem is intrinsically combinatorial. Without any pruning, there would be <code>N^N</code> possibilities. However, the <code>is_safe</code> checks prune the search space significantly.</li>
<li>The time complexity is exponential. A loose upper bound is often cited as <code>O(N!)</code>, but the exact complexity is difficult to determine precisely due to aggressive pruning. For <code>N=8</code>, it's relatively fast. For each valid full solution, constructing the board takes <code>O(N^2)</code> time.</li>
<li>Let <code>S(N)</code> be the number of solutions for <code>N</code> queens. The total time complexity can be expressed as <code>O(number_of_nodes_visited_in_search_tree * 1 + S(N) * N^2)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>queens_pos</code>: <code>O(N)</code> for storing column positions.</li>
<li><code>col_occupied</code>, <code>diag_occupied</code>, <code>anti_diag_occupied</code>: Each set can hold up to <code>N</code> elements (for columns), <code>2N-1</code> elements (for diagonals like <code>row-col</code>, ranging from <code>-(N-1)</code> to <code>N-1</code>), and <code>2N-1</code> elements (for anti-diagonals like <code>row+col</code>, ranging from <code>0</code> to <code>2N-2</code>). Thus, they collectively take <code>O(N)</code> space.</li>
<li>Recursion Stack Depth: The maximum depth of the recursion is <code>N</code> (one call per row), so <code>O(N)</code> space.</li>
<li><code>solutions</code> list: Stores <code>S(N)</code> board configurations. Each configuration is a list of <code>N</code> strings, each string of length <code>N</code>. So, each solution takes <code>O(N^2)</code> space. Total space for storing solutions is <code>O(S(N) * N^2)</code>.</li>
<li>Therefore, the dominant space complexity is <code>O(S(N) * N^2)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n = 1</code></strong>:<ul>
<li><code>backtrack(0)</code> is called.</li>
<li>Loop <code>col=0</code>. <code>(0,0)</code> is safe.</li>
<li>Place queen at <code>(0,0)</code>. Call <code>backtrack(1)</code>.</li>
<li><code>row == n</code> (1 == 1) is true. A solution is found.</li>
<li><code>current_board</code> becomes <code>[['Q']]</code>. This is appended to <code>solutions</code>.</li>
<li>Correctly returns <code>[['Q']]</code>.</li>
</ul>
</li>
<li><strong><code>n = 0</code></strong>:<ul>
<li><code>queens_pos</code> is <code>[]</code>, sets are empty. <code>backtrack(0)</code> is called.</li>
<li><code>row == n</code> (0 == 0) is true immediately.</li>
<li><code>current_board</code> becomes <code>[]</code>. This is appended to <code>solutions</code>.</li>
<li>Correctly returns <code>[[]]</code>. While technically a valid outcome for "0 queens", most N-Queens problem statements imply <code>n &gt;= 1</code>. If <code>n=0</code> should return <code>[]</code>, a simple <code>if n == 0: return []</code> check can be added at the beginning.</li>
</ul>
</li>
<li><strong>Correctness</strong>: The algorithm's correctness hinges on:<ul>
<li><strong>Exhaustive Search</strong>: The nested loop (<code>for col in range(n)</code>) combined with recursion ensures that every possible position for a queen in each row is considered.</li>
<li><strong>Accurate Safety Checks</strong>: The use of sets with <code>col</code>, <code>row-col</code>, and <code>row+col</code> guarantees O(1) average-time checks for conflicts, correctly identifying if a square is attacked by any previously placed queen.</li>
<li><strong>Proper Backtracking</strong>: Crucially, the "undo" steps (removing from sets and resetting <code>queens_pos</code>) ensure that the state is reset correctly before trying the next column, allowing the exploration of all distinct paths without interference.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (Bit Manipulation)</strong>: For <code>N &lt;= 32</code> (or 64 on 64-bit systems), the <code>col_occupied</code>, <code>diag_occupied</code>, and <code>anti_diag_occupied</code> sets can be replaced by bitmasks (integers).<ul>
<li>A single integer can represent <code>col_occupied</code>, where the <code>k</code>-th bit is set if column <code>k</code> is occupied.</li>
<li>Similarly for diagonals. This can offer a significant speedup due to the extremely fast nature of bitwise operations (AND, OR, XOR) compared to hash set operations, reducing constant factors.</li>
</ul>
</li>
<li><strong>Readability (Helper Function)</strong>: While clear, the <code>if</code> condition for <code>is_safe</code> could be extracted into a small helper function <code>_is_safe(row, col)</code> within <code>backtrack</code> for slightly enhanced modularity, though it's already quite concise.</li>
<li><strong>Symmetry Breaking</strong>: For larger <code>n</code>, the search space can be further reduced by exploiting symmetries. For example, if a solution exists, its rotations and reflections are also solutions. One could find solutions where the first queen is only placed in the first half of the columns (<code>col &lt; n/2</code>), and then generate symmetric solutions, effectively reducing the initial branching factor. This significantly complicates the solution generation logic and is usually only pursued when the <em>count</em> of solutions is needed, or distinct solutions <em>up to symmetry</em> are required. This code aims for all distinct solutions.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: No direct security vulnerabilities in this algorithm. It's a purely computational problem.</li>
<li><strong>Performance</strong>: The current implementation is efficient for the general backtracking approach. The choice of <code>set</code> for conflict checking is optimal for average-case performance. The inherent combinatorial explosion of the problem means that for very large <code>N</code> (e.g., <code>N &gt; 15-20</code>), even optimized solutions will take considerable time. For <code>N</code> up to around <code>10-12</code>, this solution performs very well.</li>
</ul>


### Code:
```python
class Solution(object):
    def solveNQueens(self, n):
        """
        :type n: int
        :rtype: List[List[str]]
        """
        
        # queens_pos[row] stores the column index of the queen in that row.
        # Initialized with -1 to indicate no queen placed yet.
        queens_pos = [-1] * n 
        
        # Sets to keep track of occupied columns, diagonals, and anti-diagonals.
        # Using sets provides O(1) average time complexity for add, remove, and check operations.
        
        # col_occupied: stores column indices that have a queen.
        col_occupied = set()
        
        # diag_occupied: stores (row - col) values for main diagonals.
        # Queens on the same main diagonal have the same (row - col) difference.
        diag_occupied = set()
        
        # anti_diag_occupied: stores (row + col) values for anti-diagonals.
        # Queens on the same anti-diagonal have the same (row + col) sum.
        anti_diag_occupied = set()
        
        # List to store all valid board configurations found.
        solutions = []
        
        # Helper function for backtracking.
        # 'row' represents the current row we are trying to place a queen in.
        def backtrack(row):
            # Base case: If we have successfully placed queens in all 'n' rows,
            # a valid solution has been found.
            if row == n:
                # Construct the board configuration from the queens_pos array.
                current_board = []
                for r in range(n):
                    # Create a row string with '.' for empty spaces.
                    board_row_list = ['.'] * n
                    # Place 'Q' at the column indicated by queens_pos[r].
                    board_row_list[queens_pos[r]] = 'Q'
                    current_board.append("".join(board_row_list))
                solutions.append(current_board)
                return
            
            # Recursive step: Try placing a queen in each column of the current 'row'.
            for col in range(n):
                # Check if placing a queen at (row, col) is safe.
                # It's safe if the column, main diagonal, and anti-diagonal are not occupied.
                if col not in col_occupied and \
                   (row - col) not in diag_occupied and \
                   (row + col) not in anti_diag_occupied:
                    
                    # Place the queen:
                    # 1. Record its position in queens_pos.
                    # 2. Mark the column, main diagonal, and anti-diagonal as occupied.
                    queens_pos[row] = col
                    col_occupied.add(col)
                    diag_occupied.add(row - col)
                    anti_diag_occupied.add(row + col)
                    
                    # Recurse to the next row to place the next queen.
                    backtrack(row + 1)
                    
                    # Backtrack: Remove the queen (undo the placement)
                    # This is crucial for exploring other possibilities.
                    anti_diag_occupied.remove(row + col)
                    diag_occupied.remove(row - col)
                    col_occupied.remove(col)
                    queens_pos[row] = -1 # Reset the position for this row
        
        # Start the backtracking process from the first row (row 0).
        backtrack(0)
        
        return solutions
```
