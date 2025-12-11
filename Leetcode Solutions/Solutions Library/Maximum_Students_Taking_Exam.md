## Maximum Students Taking Exam
**Language:** python
**Tags:** dynamic programming,bit manipulation,mask dp,grid

### Description:
<p>This code solves the "Maximum Students Taking Exam" problem using dynamic programming with bitmasking. The goal is to find the maximum number of students that can be seated in a classroom, respecting rules about broken seats and student adjacency.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The code aims to determine the maximum number of students that can be seated in a classroom represented by a 2D grid.</p>
<ul>
<li><code>'.'</code> denotes an available seat.</li>
<li><code>'#'</code> denotes a broken seat.</li>
<li><strong>Constraints:</strong><ul>
<li>Students cannot sit in broken seats.</li>
<li>Students cannot sit in adjacent seats (horizontally, or diagonally up-left, up-right). This implies students in the current row cannot be adjacent to students in the previous row.</li>
</ul>
</li>
</ul>
<p>The problem is solved using dynamic programming, where states are represented by bitmasks for seating arrangements in each row.</p>
<hr>
<h3>2. How It Works</h3>
<p>The solution proceeds in three main phases:</p>
<ul>
<li><p><strong>Preprocessing Valid Row Masks:</strong></p>
<ul>
<li>For each row <code>r</code>, it first identifies all broken seats and represents them as a <code>broken_seats_mask</code>.</li>
<li>Then, it iterates through all possible <code>2^n</code> bitmasks for that row.</li>
<li>A mask is considered "valid for the row" if:<ul>
<li>No student is placed in a broken seat (<code>(mask &amp; broken_seats_mask) == 0</code>).</li>
<li>No two students are seated horizontally adjacent (<code>(mask &amp; (mask &gt;&gt; 1)) == 0</code>).</li>
</ul>
</li>
<li>These valid masks are stored in <code>valid_masks_for_row[r]</code>.</li>
</ul>
</li>
<li><p><strong>Dynamic Programming Initialization (First Row):</strong></p>
<ul>
<li>A 2D DP table <code>dp</code> is initialized. <code>dp[r][mask]</code> will store the maximum number of students up to row <code>r</code>, with <code>mask</code> representing the seating arrangement in row <code>r</code>.</li>
<li>For the first row (<code>r=0</code>), <code>dp[0][mask]</code> is set to the number of students (set bits) in that <code>mask</code>, for all <code>mask</code> in <code>valid_masks_for_row[0]</code>. Unreachable masks default to -1.</li>
</ul>
</li>
<li><p><strong>Dynamic Programming Iteration (Subsequent Rows):</strong></p>
<ul>
<li>For each row <code>r</code> from 1 to <code>m-1</code>:<ul>
<li>It iterates through every <code>current_mask</code> that is valid for row <code>r</code>.</li>
<li>For each <code>current_mask</code>, it iterates through every <code>prev_mask</code> that was valid for the <code>r-1</code> row.</li>
<li>It checks for diagonal adjacency between <code>current_mask</code> and <code>prev_mask</code>:<ul>
<li>No student at <code>(r, c)</code> can be adjacent to a student at <code>(r-1, c-1)</code>: <code>(current_mask &amp; (prev_mask &gt;&gt; 1)) == 0</code>.</li>
<li>No student at <code>(r, c)</code> can be adjacent to a student at <code>(r-1, c+1)</code>: <code>(current_mask &amp; (prev_mask &lt;&lt; 1)) == 0</code>.</li>
</ul>
</li>
<li>If both diagonal conditions are met and <code>dp[r-1][prev_mask]</code> is a valid (not -1) state:<ul>
<li>It updates <code>dp[r][current_mask]</code> by taking the maximum of <code>dp[r-1][prev_mask] + students_in_current_mask</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Final Result:</strong></p>
<ul>
<li>After filling the DP table, the maximum value across all entries in <code>dp</code> is the final answer.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Bitmasking for Row States:</strong><ul>
<li><strong>Decision:</strong> Each row's seating arrangement is represented by a bitmask, where the <code>c</code>-th bit is <code>1</code> if a student is at <code>(r, c)</code> and <code>0</code> otherwise.</li>
<li><strong>Rationale:</strong> This is highly efficient for checking adjacency conditions using bitwise operations (<code>&amp;</code>, <code>|</code>, <code>&lt;&lt;</code>, <code>&gt;&gt;</code>). For a row of <code>n</code> columns, there are <code>2^n</code> possible arrangements, which is manageable for small <code>n</code>.</li>
</ul>
</li>
<li><strong>Dynamic Programming:</strong><ul>
<li><strong>Decision:</strong> A 2D DP table <code>dp[row][mask]</code> stores the maximum students up to that <code>row</code> given <code>mask</code> for the current row.</li>
<li><strong>Rationale:</strong> The problem exhibits optimal substructure (optimal solution for <code>r</code> depends on optimal solutions for <code>r-1</code>) and overlapping subproblems (same <code>prev_mask</code> might be evaluated multiple times). DP effectively memoizes these results.</li>
</ul>
</li>
<li><strong>Preprocessing Valid Masks:</strong><ul>
<li><strong>Decision:</strong> Generate <code>valid_masks_for_row</code> lists once at the beginning.</li>
<li><strong>Rationale:</strong> Avoids redundant calculations of "within-row" validity checks inside the main DP loop, making the DP iteration cleaner and potentially faster.</li>
</ul>
</li>
<li><strong><code>collections.defaultdict(lambda: -1)</code>:</strong><ul>
<li><strong>Decision:</strong> Use a <code>defaultdict</code> initialized to -1 for DP states.</li>
<li><strong>Rationale:</strong> -1 acts as a sentinel value indicating an unreachable or invalid state, which is crucial when taking <code>max</code> values to ensure only valid previous states contribute.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>m</code> be the number of rows and <code>n</code> be the number of columns.</p>
<ul>
<li><p><strong>Time Complexity:</strong></p>
<ul>
<li><strong>Preprocessing <code>valid_masks_for_row</code>:</strong> For each of <code>m</code> rows, it iterates through <code>2^n</code> masks. Inside the loop, <code>bin(mask).count('1')</code> and bitwise operations take <code>O(n)</code> time (or <code>O(1)</code> for fixed-size integer operations, but <code>count</code> explicitly depends on <code>n</code>).<ul>
<li><code>O(m * 2^n * n)</code></li>
</ul>
</li>
<li><strong>DP Initialization (Row 0):</strong> Iterates over at most <code>2^n</code> masks.<ul>
<li><code>O(2^n * n)</code></li>
</ul>
</li>
<li><strong>DP Iteration (Rows 1 to <code>m-1</code>):</strong><ul>
<li>Outer loop <code>m-1</code> times.</li>
<li>Inner loops: <code>current_mask</code> iterates over at most <code>2^n</code> masks, <code>prev_mask</code> iterates over at most <code>2^n</code> masks.</li>
<li>Inside the loops, bitwise operations and <code>count('1')</code> take <code>O(n)</code>.</li>
<li><code>O(m * (2^n)^2 * n)</code> which simplifies to <code>O(m * 4^n * n)</code>.</li>
</ul>
</li>
<li><strong>Finding Max Total Students:</strong> Iterates over <code>m</code> rows and <code>2^n</code> possible masks.<ul>
<li><code>O(m * 2^n)</code></li>
</ul>
</li>
<li><strong>Overall Time Complexity:</strong> Dominated by the DP iteration: <code>O(m * 4^n * n)</code>.<ul>
<li><em>Note:</em> This complexity implies <code>n</code> must be relatively small (e.g., usually &lt;= 10-12 in competitive programming contexts, but often &lt;= 8 for <code>4^n</code> factors).</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong></p>
<ul>
<li><code>valid_masks_for_row</code>: Stores <code>m</code> lists, each potentially containing <code>2^n</code> masks.<ul>
<li><code>O(m * 2^n)</code></li>
</ul>
</li>
<li><code>dp</code>: Stores <code>m</code> dictionaries, each potentially containing <code>2^n</code> entries.<ul>
<li><code>O(m * 2^n)</code></li>
</ul>
</li>
<li><strong>Overall Space Complexity:</strong> <code>O(m * 2^n)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Grid (m=0 or n=0):</strong> The current code would likely fail or behave unexpectedly if <code>m=0</code> (e.g., <code>seats[0]</code> would throw <code>IndexError</code>). Assuming <code>m, n &gt;= 1</code> based on typical problem constraints. If <code>m</code> or <code>n</code> can be 0, explicit checks are needed.</li>
<li><strong>All Broken Seats:</strong> If all <code>seats[r][c] == '#'</code>, then <code>valid_masks_for_row[r]</code> will contain only <code>[0]</code> (empty mask) for all rows. The <code>dp</code> values will remain -1 (except for <code>dp[0][0]=0</code>), and the final result will correctly be 0.</li>
<li><strong>All Open Seats:</strong> The logic correctly applies the horizontal and diagonal adjacency rules, limiting the possible seating arrangements even if all seats are available.</li>
<li><strong>Single Row (m=1):</strong> The <code>for r in range(1, m)</code> loop will not execute. The <code>max_total_students</code> will correctly be calculated from <code>dp[0]</code>.</li>
<li><strong>Correctness of Adjacency Checks:</strong><ul>
<li><code>mask &amp; (mask &gt;&gt; 1)</code>: Checks if any bit is set at <code>c</code> AND <code>c-1</code>, indicating horizontal adjacency. Correct.</li>
<li><code>current_mask &amp; (prev_mask &gt;&gt; 1)</code>: Checks if a student at <code>(r, c)</code> is diagonally adjacent to <code>(r-1, c-1)</code>. Correct.</li>
<li><code>current_mask &amp; (prev_mask &lt;&lt; 1)</code>: Checks if a student at <code>(r, c)</code> is diagonally adjacent to <code>(r-1, c+1)</code>. Correct.</li>
</ul>
</li>
<li><strong>Handling Unreachable States:</strong> The use of <code>-1</code> for <code>dp</code> states that cannot be reached (e.g., no valid <code>prev_mask</code> allows <code>current_mask</code>) ensures that these states do not contribute positively to the <code>max_total_students</code> calculation.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Space Optimization (DP Table):</strong><ul>
<li>Since <code>dp[r]</code> only depends on <code>dp[r-1]</code>, we can reduce the space complexity of the <code>dp</code> table from <code>O(m * 2^n)</code> to <code>O(2 * 2^n)</code> (or even <code>O(2^n)</code> by swapping references). This would store only the current and previous row's DP states.</li>
</ul>
</li>
<li><strong>Faster Bit Counting:</strong><ul>
<li><code>bin(mask).count('1')</code> is relatively slow as it converts to a string. For competitive programming, faster methods include:<ul>
<li>Precomputing <code>popcount</code> (number of set bits) for all masks up to <code>2^n - 1</code>.</li>
<li>Using <code>sum(1 for i in range(n) if (mask &gt;&gt; i) &amp; 1)</code> or more advanced bit manipulation techniques like <code>mask &amp;= (mask - 1)</code> (Brian Kernighan's algorithm).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Readability/Modularity:</strong><ul>
<li>Extracting the <code>is_valid_row_mask</code> and <code>is_valid_diagonal_placement</code> logic into separate helper functions could improve readability, though for bitmasking problems, inline bitwise operations are common.</li>
</ul>
</li>
<li><strong>Pre-calculation of <code>valid_masks_for_row</code>:</strong> Could use a helper function to make the main logic clearer.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck:</strong> The primary performance bottleneck is the <code>O(m * 4^n * n)</code> time complexity. This algorithm is only feasible for small values of <code>n</code>. For larger <code>n</code>, a different approach entirely would be required (e.g., maximum bipartite matching or minimum cost maximum flow on a carefully constructed graph), but these are usually much more complex to implement.</li>
<li><strong>Input Size:</strong> Given the exponential dependency on <code>n</code>, it's critical to know the constraints on <code>n</code>. If <code>n</code> were, for example, 15 or 20, this solution would be too slow. Typical constraints for <code>4^n</code> approaches are <code>n &lt;= 10-12</code>.</li>
<li><strong>No Obvious Security Concerns:</strong> This is a pure algorithmic problem; there are no external inputs, file operations, or network calls that would introduce typical security vulnerabilities like injection, data exposure, etc.</li>
</ul>


### Code:
```python
import collections

class Solution(object):
    def maxStudents(self, seats):
        """
        :type seats: List[List[str]]
        :rtype: int
        """
        m = len(seats)
        n = len(seats[0])

        valid_masks_for_row = [[] for _ in range(m)]
        for r in range(m):
            broken_seats_mask = 0
            for c in range(n):
                if seats[r][c] == '#':
                    broken_seats_mask |= (1 << c)

            for mask in range(1 << n):
                if (mask & broken_seats_mask) != 0:
                    continue
                
                if (mask & (mask >> 1)) != 0:
                    continue
                
                valid_masks_for_row[r].append(mask)

        dp = [collections.defaultdict(lambda: -1) for _ in range(m)]

        for mask in valid_masks_for_row[0]:
            dp[0][mask] = bin(mask).count('1')

        for r in range(1, m):
            for current_mask in valid_masks_for_row[r]:
                current_students = bin(current_mask).count('1')
                
                max_prev_students = -1
                for prev_mask in valid_masks_for_row[r-1]:
                    if (current_mask & (prev_mask >> 1)) != 0:
                        continue
                    
                    if (current_mask & (prev_mask << 1)) != 0:
                        continue
                    
                    if dp[r-1][prev_mask] != -1:
                        max_prev_students = max(max_prev_students, dp[r-1][prev_mask])
                
                if max_prev_students != -1:
                    dp[r][current_mask] = max_prev_students + current_students

        max_total_students = 0
        for r in range(m):
            for mask_value in dp[r].values():
                max_total_students = max(max_total_students, mask_value)
        
        return max_total_students
```
