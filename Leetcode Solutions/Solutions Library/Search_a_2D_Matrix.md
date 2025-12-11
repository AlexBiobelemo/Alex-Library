## Search a 2D Matrix
**Language:** python
**Tags:** python,binary search,matrix,divide and conquer

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>searchMatrix</code> function aims to efficiently determine if a given <code>target</code> integer exists within a 2D integer <code>matrix</code>.</p>
<p>This specific algorithm relies on a common problem constraint for such matrices:</p>
<ul>
<li>Each row is sorted in non-decreasing order.</li>
<li>The first integer of each row is greater than the last integer of the previous row.</li>
</ul>
<p>These properties imply that if the matrix were flattened into a single 1D array, it would also be sorted in non-decreasing order. The code leverages this property to perform a highly efficient search.</p>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to treat the 2D matrix as a single, flattened, sorted 1D array and then apply a standard binary search algorithm.</p>
<ol>
<li><strong>Handle Empty Matrix</strong>: It first checks for edge cases where the matrix is empty (no rows or no columns), returning <code>False</code> immediately.</li>
<li><strong>Define Search Space</strong>: It initializes <code>low</code> to <code>0</code> (representing the first element of the conceptual 1D array) and <code>high</code> to <code>m * n - 1</code> (representing the last element).</li>
<li><strong>Binary Search Loop</strong>: While <code>low</code> is less than or equal to <code>high</code>:<ul>
<li>It calculates <code>mid</code> using standard binary search logic.</li>
<li><strong>1D to 2D Conversion</strong>: The crucial step is converting this 1D <code>mid</code> index back to 2D <code>(row, col)</code> coordinates:<ul>
<li><code>row = mid // n</code> (integer division gives the row index)</li>
<li><code>col = mid % n</code> (modulo operation gives the column index)</li>
</ul>
</li>
<li>It retrieves <code>current_val</code> from the matrix using these 2D coordinates.</li>
<li><strong>Comparison</strong>:<ul>
<li>If <code>current_val</code> equals <code>target</code>, the target is found, and <code>True</code> is returned.</li>
<li>If <code>current_val</code> is less than <code>target</code>, the search space is narrowed to the upper half (<code>low = mid + 1</code>).</li>
<li>If <code>current_val</code> is greater than <code>target</code>, the search space is narrowed to the lower half (<code>high = mid - 1</code>).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Target Not Found</strong>: If the loop completes without finding the target, <code>False</code> is returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm Choice</strong>: Binary search is chosen because the implicit property of the matrix (when flattened) makes it a sorted collection. Binary search is the most efficient comparison-based search algorithm for sorted data.</li>
<li><strong>Data Structure Abstraction</strong>: The most significant decision is to abstract the 2D <code>matrix</code> as a 1D <code>sorted array</code> of size <code>m * n</code>. This allows a single binary search to work across the entire structure.</li>
<li><strong>Index Mapping</strong>: The conversion formulas <code>row = mid // n</code> and <code>col = mid % n</code> are fundamental to this approach. They correctly map any 1D index <code>mid</code> to its corresponding <code>(row, col)</code> position in a row-major ordered 2D array.</li>
<li><strong>Trade-offs</strong>:<ul>
<li><strong>Pros</strong>: Highly efficient (logarithmic time), concise code, and uses minimal extra space.</li>
<li><strong>Cons</strong>: Relies heavily on the specific sorted properties of the matrix. If those properties didn't hold, this algorithm would be incorrect. It might be slightly less intuitive than a two-step binary search (on rows then columns) for those unfamiliar with the 1D flattening trick.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log(m * n))</strong><ul>
<li>The algorithm performs a binary search over a conceptual 1D array of <code>m * n</code> elements.</li>
<li>Each step of binary search effectively halves the search space.</li>
<li>Therefore, the time taken is proportional to the logarithm of the total number of elements.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a constant amount of extra space for variables like <code>m</code>, <code>n</code>, <code>low</code>, <code>high</code>, <code>mid</code>, <code>row</code>, <code>col</code>, and <code>current_val</code>. This space usage does not grow with the input size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Matrix</strong>:<ul>
<li><code>m = 0</code>: Handled directly by <code>if m == 0: return False</code>.</li>
<li><code>n = 0</code>: Handled directly by <code>if n == 0: return False</code>.</li>
<li>These checks ensure the code doesn't attempt to access <code>matrix[0]</code> or perform calculations with <code>n=0</code>.</li>
</ul>
</li>
<li><strong>Single Element Matrix</strong>: If <code>matrix = [[5]]</code> and <code>target = 5</code>, <code>m=1, n=1</code>. <code>low=0, high=0</code>. <code>mid=0</code>, <code>row=0, col=0</code>. <code>current_val=5</code>. <code>return True</code>. Correct.</li>
<li><strong>Target Not Present</strong>: If the target is not in the matrix, the <code>while low &lt;= high</code> loop will eventually terminate with <code>low &gt; high</code>, and the function correctly returns <code>False</code>.</li>
<li><strong>Target at Boundaries</strong>: The binary search logic (<code>low = mid + 1</code>, <code>high = mid - 1</code>) correctly handles cases where the target is the first, last, or any boundary element in the conceptual flattened array.</li>
<li><strong>Integer Overflow</strong>: The <code>mid = (low + high) // 2</code> calculation is safe in Python as integers handle arbitrary precision. In languages like C++/Java, <code>mid = low + (high - low) // 2</code> is preferred to prevent <code>low + high</code> from overflowing for very large <code>low</code> and <code>high</code> values, although it's not strictly necessary here due to <code>m*n</code> fitting into standard integer types for typical problem constraints.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<p><strong>6.1. Readability Improvements</strong></p>
<ul>
<li><strong>Comments</strong>: Add a comment explaining the 1D to 2D index conversion, as it's the core "trick" of the algorithm.<pre><code class="language-python">        # Convert 1D index 'mid' to 2D coordinates (row, col)
        row = mid // n
        col = mid % n
</code></pre>
</li>
</ul>
<p><strong>6.2. Alternative Algorithms</strong></p>
<p>While the current solution is optimal for its constraints, two common alternatives exist:</p>
<ul>
<li><p><strong>Two Binary Searches (O(log m + log n))</strong>:</p>
<ol>
<li><strong>Binary Search on Rows</strong>: Perform a binary search on the first element of each row to find the row that <em>might</em> contain the target. This finds the largest row index <code>r</code> such that <code>matrix[r][0] &lt;= target</code>. (O(log m) time)</li>
<li><strong>Binary Search on Column</strong>: Once the potential row <code>r</code> is identified, perform a standard binary search within <code>matrix[r]</code> for the <code>target</code>. (O(log n) time)</li>
</ol>
<ul>
<li><strong>Pros</strong>: More intuitive, potentially better cache locality if rows are contiguous in memory.</li>
<li><strong>Cons</strong>: Slightly more lines of code (two separate binary search steps).</li>
<li><strong>Complexity Comparison</strong>: <code>log(m*n)</code> is mathematically equivalent to <code>log m + log n</code>. Both approaches have the same time complexity profile.</li>
</ul>
</li>
<li><p><strong>Corner Start Search (O(m + n))</strong>:</p>
<ul>
<li>Start at a corner like <code>(row = 0, col = n - 1)</code> (top-right) or <code>(row = m - 1, col = 0)</code> (bottom-left).</li>
<li>If <code>current_val == target</code>, return <code>True</code>.</li>
<li>If <code>current_val &lt; target</code>: Move down (<code>row++</code>) if starting top-right, or right (<code>col++</code>) if starting bottom-left.</li>
<li>If <code>current_val &gt; target</code>: Move left (<code>col--</code>) if starting top-right, or up (<code>row--</code>) if starting bottom-left.</li>
<li><strong>Pros</strong>: Very simple and elegant logic for certain types of sorted matrices (where both rows and columns are sorted).</li>
<li><strong>Cons</strong>: Less efficient than binary search for the given problem constraints. For an <code>N x N</code> matrix, this is <code>O(N)</code> while binary search is <code>O(log N^2) = O(log N)</code>. E.g., for a 1000x1000 matrix, <code>1000+1000 = 2000</code> steps vs <code>log(10^6)</code> which is around <code>20</code> steps.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: The code does not handle external input directly or perform operations that could lead to security vulnerabilities. It operates solely on the provided matrix and target.</li>
<li><strong>Performance</strong>: The chosen <code>O(log(m*n))</code> solution is optimal for this problem, given the specific sorted properties of the matrix. There are no obvious performance bottlenecks or missed optimizations within the chosen algorithm. The constant factor operations (integer division, modulo) are highly efficient.</li>
</ul>


### Code:
```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        m = len(matrix)
        if m == 0:
            return False
        n = len(matrix[0])
        if n == 0:
            return False

        low = 0
        high = m * n - 1

        while low <= high:
            mid = (low + high) // 2
            
            # Convert 1D index to 2D coordinates
            row = mid // n
            col = mid % n
            
            current_val = matrix[row][col]
            
            if current_val == target:
                return True
            elif current_val < target:
                low = mid + 1
            else: # current_val > target
                high = mid - 1
                
        return False
```
