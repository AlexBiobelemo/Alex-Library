## Find a Peak Element II
**Language:** python
**Tags:** python,binary search,matrix,peak element

### Description:
<p>The provided Python code implements an efficient algorithm to find a peak element in a 2D integer matrix.</p>
<h2>1. Overview &amp; Intent</h2>
<p>This code aims to find any "peak element" within a given 2D matrix <code>mat</code>. A peak element is defined as an element that is strictly greater than all its four immediate neighbors (up, down, left, right). Elements on the boundary of the matrix are considered peaks if they are greater than their existing neighbors, with non-existent neighbors implicitly treated as having a value of negative infinity (or a very small number, like -1 in this implementation).</p>
<p>The problem statement typically guarantees that:</p>
<ul>
<li>No two adjacent cells have equal values.</li>
<li>A peak element always exists in the matrix.</li>
</ul>
<h2>2. How It Works</h2>
<p>The algorithm uses a strategy akin to a binary search, but applied to the columns of the 2D matrix, combined with a linear scan within the chosen column.</p>
<ol>
<li><p><strong>Binary Search on Columns</strong>: It initializes <code>low_col</code> and <code>high_col</code> to represent the range of columns to search, then enters a <code>while</code> loop that continues as long as <code>low_col</code> is less than or equal to <code>high_col</code>. In each iteration, it calculates <code>mid_col</code>.</p>
</li>
<li><p><strong>Find Column-wise Maximum</strong>: For the <code>mid_col</code>, the code iterates through all rows (<code>r</code> from 0 to <code>m-1</code>) to find the row (<code>max_row</code>) that contains the maximum value in that specific <code>mid_col</code>. Let this maximum value be <code>current_val</code>.</p>
<ul>
<li><strong>Key Insight</strong>: By choosing the maximum value in the column, <code>current_val</code> is guaranteed to be greater than its immediate top and bottom neighbors (if they exist and are within the <code>mid_col</code>), satisfying two of the four peak conditions (or at least making it the "highest point" in that column, which is essential for the hill-climbing logic).</li>
</ul>
</li>
<li><p><strong>Check Neighbors and Decide Direction</strong>:</p>
<ul>
<li>It retrieves the values of the <code>current_val</code>'s left and right neighbors (<code>left_val</code>, <code>right_val</code>). If a neighbor is out-of-bounds, its value is treated as -1.</li>
<li><strong>Peak Check</strong>: If <code>current_val</code> is strictly greater than both <code>left_val</code> AND <code>right_val</code>, then <code>current_val</code> is a peak element. Its coordinates <code>[max_row, mid_col]</code> are returned.</li>
<li><strong>Move Direction</strong>:<ul>
<li>If <code>current_val</code> is less than <code>left_val</code>, it implies there's a larger element to the left. The algorithm then discards the current column and all columns to its right by setting <code>high_col = mid_col - 1</code>. The peak must lie in the left half.</li>
<li>Otherwise (if <code>current_val</code> is less than <code>right_val</code>), there's a larger element to the right. The algorithm discards the current column and all columns to its left by setting <code>low_col = mid_col + 1</code>. The peak must lie in the right half.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Guaranteed Termination</strong>: Since the search range (<code>low_col</code> to <code>high_col</code>) is halved in each step and a peak is guaranteed to exist, the loop will eventually find and return a peak. The <code>return [-1, -1]</code> statement at the end is unreachable under normal problem constraints.</p>
</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Binary Search on Columns</strong>: This is the core algorithmic choice. It leverages the "hill-climbing" property (if not a peak, move towards a larger neighbor) efficiently in one dimension (columns).</li>
<li><strong>Finding Column-wise Maximum</strong>: Instead of picking an arbitrary element in <code>mid_col</code>, finding the maximum within that column simplifies the peak check. It ensures that the vertical neighbors are already accounted for, reducing the problem to checking only horizontal neighbors.</li>
<li><strong>Out-of-Bounds Neighbors as -1</strong>: This is a standard and effective way to handle boundary conditions. By treating non-existent neighbors as a very small value, elements at the edge or corner can still be correctly identified as peaks if they are larger than their actual existing neighbors.</li>
<li><strong>Implicit Peak Guarantee</strong>: The problem statement's guarantee of "no two adjacent cells are equal" and "a peak always exists" is crucial. It ensures that the hill-climbing strategy will always find a strictly larger neighbor if the current <code>current_val</code> is not a peak, and that the search will not get stuck in a plateau or local minima that isn't a true peak.</li>
</ul>
<h2>4. Complexity</h2>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li>The outer <code>while</code> loop performs a binary search on the columns. There are <code>n</code> columns, so this loop runs <code>O(log n)</code> times.</li>
<li>Inside the loop, finding the maximum element in <code>mid_col</code> requires iterating through all <code>m</code> rows. This takes <code>O(m)</code> time.</li>
<li>The comparisons with <code>left_val</code> and <code>right_val</code> take <code>O(1)</code> time.</li>
<li>Therefore, the total time complexity is <strong><code>O(m * log n)</code></strong>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li>The algorithm uses a few variables to store indices, values, and dimensions (<code>m</code>, <code>n</code>, <code>low_col</code>, <code>high_col</code>, <code>mid_col</code>, <code>max_val_in_col</code>, <code>max_row</code>, <code>current_val</code>, <code>left_val</code>, <code>right_val</code>). These require a constant amount of extra space.</li>
<li>The space complexity is <strong><code>O(1)</code></strong>.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong>1xN or Nx1 Grid</strong>: The code handles these cases correctly. If <code>m=1</code>, the inner loop runs once. If <code>n=1</code>, the binary search effectively runs for <code>mid_col = 0</code> and then terminates.</li>
<li><strong>Peak at Boundary/Corner</strong>: Correctly handled by treating out-of-bounds neighbors as <code>-1</code>. For instance, an element at <code>[0, 0]</code> is compared only with <code>mat[0][1]</code> and <code>mat[1][0]</code> (if they exist); <code>left_val</code> and <code>top_val</code> would default to -1.</li>
<li><strong>Monotonically Increasing/Decreasing Grids</strong>: While the problem usually states "no two adjacent cells are equal", if a grid were strictly increasing/decreasing (which would mean a peak is at a corner), the algorithm would correctly identify it.</li>
<li><strong>Guaranteed Peak</strong>: The problem guarantees a peak exists. The "hill-climbing" approach ensures that if the current element isn't a peak, there's always a larger neighbor to move towards, guaranteeing progress and eventual discovery of a peak. Thus, the <code>return [-1, -1]</code> is not expected to be reached.</li>
<li><strong>Empty Matrix</strong>: The code assumes <code>mat</code> is not empty and <code>mat[0]</code> is not empty. If <code>mat</code> were empty, <code>len(mat)</code> would be 0, and <code>len(mat[0])</code> would raise an <code>IndexError</code>. Constraints typically specify <code>m, n &gt;= 1</code>.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Input Validation</strong>: For robustness, one could add checks at the beginning to ensure <code>mat</code> is not empty and <code>mat[0]</code> is not empty before accessing <code>len(mat)</code> and <code>len(mat[0])</code>.</li>
<li><strong>Docstrings/Type Hinting</strong>: While the type hints are provided in the LeetCode template style comments, adding proper Python docstrings and modern type hints (<code>m: List[List[int]] -&gt; List[int]</code>) would improve readability and tooling support.</li>
<li><strong>Optimization of Max Finding (Minor)</strong>: The current <code>for</code> loop to find <code>max_row</code> is clear. For extremely wide columns with few rows, or if <code>m</code> was much larger than <code>n</code>, a more optimized column-finding method could be explored, but generally, this linear scan is necessary.</li>
<li><strong>Alternative Algorithms</strong>:<ul>
<li><strong>Brute-Force</strong>: Iterate through every cell <code>(r, c)</code> and check all four neighbors. This would be <code>O(m*n)</code> time complexity, which is less efficient than the current approach.</li>
<li><strong>Strictly 2D Binary Search (More Complex)</strong>: Some variants of 2D peak finding can achieve <code>O(log(m*n))</code> or <code>O(log m + log n)</code> complexity, but they often require stricter matrix properties or a different definition of "peak" that allows for simultaneous pruning of both rows and columns. The current <code>O(m log n)</code> approach is a standard and effective solution for the given problem constraints and peak definition.</li>
</ul>
</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this algorithm. It operates purely on numerical comparisons and array indexing.</li>
<li><strong>Performance</strong>: The <code>O(m log n)</code> time complexity is quite efficient for the problem. For large matrices, especially those with many more rows than columns (i.e., <code>m &gt;&gt; n</code>), the <code>log n</code> factor will dominate the <code>m</code> factor less, making it perform closer to <code>O(m)</code>. If <code>n &gt;&gt; m</code>, then it performs closer to <code>O(log n)</code>. This is generally a good balance.</li>
</ul>


### Code:
```python
class Solution(object):
    def findPeakGrid(self, mat):
        """
        :type mat: List[List[int]]
        :rtype: List[int]
        """
        m = len(mat)
        n = len(mat[0])

        low_col = 0
        high_col = n - 1

        while low_col <= high_col:
            mid_col = (low_col + high_col) // 2

            # Find the row with the maximum value in mid_col
            max_val_in_col = -1
            max_row = -1
            for r in range(m):
                if mat[r][mid_col] > max_val_in_col:
                    max_val_in_col = mat[r][mid_col]
                    max_row = r
            
            current_val = mat[max_row][mid_col]

            # Get neighbors' values, treating out-of-bounds as -1
            left_val = -1
            if mid_col > 0:
                left_val = mat[max_row][mid_col - 1]
            
            right_val = -1
            if mid_col < n - 1:
                right_val = mat[max_row][mid_col + 1]
            
            # Check if current_val is a peak
            # It's already strictly greater than its top/bottom neighbors in the column
            # because we picked the maximum in the column and no two adjacent cells are equal.
            # We just need to check left and right.
            if current_val > left_val and current_val > right_val:
                return [max_row, mid_col]
            
            # If not a peak, move towards the larger neighbor
            elif current_val < left_val:
                high_col = mid_col - 1
            else: # current_val < right_val
                low_col = mid_col + 1
        
        # This part should ideally not be reached as a peak is guaranteed to exist.
        return [-1, -1]
```
