## Find the Largest Area of Square Inside Two Rectangles
**Language:** python
**Tags:** python,oop,brute-force,geometry,list

### Description:
<p>This code addresses a geometric problem: finding the largest possible square area that can be formed by the intersection of <em>any two</em> given rectangles.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to calculate the maximum area of a square that can be entirely contained within the intersection of any two distinct rectangles from a given list.</p>
<ul>
<li><strong>Input</strong>: Two lists of coordinates: <code>bottomLeft</code> and <code>topRight</code>. Each sub-list <code>[x, y]</code> represents the coordinates for one corner of a rectangle. Both lists have the same length <code>n</code>, where <code>bottomLeft[i]</code> and <code>topRight[i]</code> define the <code>i</code>-th rectangle.</li>
<li><strong>Output</strong>: An integer representing the area of the largest such square.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm proceeds by systematically checking every unique pair of rectangles and calculating the largest square that can fit within their intersection.</p>
<ol>
<li><strong>Initialization</strong>: <code>max_side</code> is initialized to 0, which will store the maximum side length found for any square.</li>
<li><strong>Pairwise Iteration</strong>: It uses nested loops to iterate through all unique pairs of rectangles.<ul>
<li>The outer loop <code>i</code> goes from <code>0</code> to <code>n-1</code>.</li>
<li>The inner loop <code>j</code> goes from <code>i + 1</code> to <code>n-1</code>. This ensures each pair is considered exactly once and no rectangle is compared with itself.</li>
</ul>
</li>
<li><strong>Extract Coordinates</strong>: For each pair <code>(i, j)</code>, it extracts the bottom-left <code>(x_bl, y_bl)</code> and top-right <code>(x_tr, y_tr)</code> coordinates for both rectangles.</li>
<li><strong>Calculate Intersection</strong>:<ul>
<li>The bottom-left x-coordinate of the intersection is the maximum of the two rectangles' bottom-left x-coordinates.</li>
<li>The bottom-left y-coordinate of the intersection is the maximum of the two rectangles' bottom-left y-coordinates.</li>
<li>The top-right x-coordinate of the intersection is the minimum of the two rectangles' top-right x-coordinates.</li>
<li>The top-right y-coordinate of the intersection is the minimum of the two rectangles' top-right y-coordinates.</li>
</ul>
</li>
<li><strong>Check for Valid Intersection</strong>: An intersection is valid (i.e., it's a rectangle with positive area) if its calculated bottom-left x is strictly less than its top-right x, AND its bottom-left y is strictly less than its top-right y. If this condition is not met, the rectangles do not overlap or only touch at a point/line, so no square with positive area can be formed.</li>
<li><strong>Calculate Square Side</strong>: If a valid intersection exists:<ul>
<li>Calculate the <code>width</code> of the intersection: <code>x_intersect_tr - x_intersect_bl</code>.</li>
<li>Calculate the <code>height</code> of the intersection: <code>y_intersect_tr - y_intersect_bl</code>.</li>
<li>The side length of the largest square that can fit inside this intersecting rectangle is <code>min(width, height)</code>.</li>
</ul>
</li>
<li><strong>Update Maximum</strong>: The <code>current_side</code> is compared with <code>max_side</code>, and <code>max_side</code> is updated to the larger of the two.</li>
<li><strong>Final Result</strong>: After checking all pairs, the function returns <code>max_side * max_side</code>, which is the area of the largest square found.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Pairwise Comparison</strong>: The most straightforward approach to finding the optimal intersection between any two elements is to compare all possible pairs. The nested loop structure <code>for i in range(n): for j in range(i + 1, n):</code> efficiently generates these unique pairs.</li>
<li><strong>Intersection Logic</strong>: The calculation of the intersecting rectangle's coordinates (<code>max</code> for bottom-left, <code>min</code> for top-right) is a standard and robust geometric technique for axis-aligned rectangles.</li>
<li><strong>Largest Square within Rectangle</strong>: The largest square that can be inscribed in any given rectangle will always have a side length equal to the <em>minimum</em> of the rectangle's width and height. This is a fundamental property used here.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N^2)</strong><ul>
<li>The core of the algorithm involves two nested loops, each iterating up to <code>N</code> times (where <code>N</code> is the number of rectangles).</li>
<li>Inside the innermost loop, all operations (coordinate extraction, <code>max</code>/<code>min</code> calculations, subtraction, comparisons) are constant time, O(1).</li>
<li>Therefore, the total time complexity is proportional to <code>N * (N-1) / 2</code>, which simplifies to O(N^2).</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>n</code>, <code>max_side</code>, <code>x1_bl</code>, etc.) regardless of the input size <code>N</code>. No auxiliary data structures that grow with <code>N</code> are created.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>No Rectangles (N=0)</strong>: <code>len(bottomLeft)</code> will be 0. The <code>range(n)</code> for the outer loop will be empty, so the loops won't execute. <code>max_side</code> remains 0, and <code>0 * 0</code> is returned. Correct.</li>
<li><strong>One Rectangle (N=1)</strong>: Similar to N=0, the outer loop <code>range(1)</code> runs once, but the inner loop <code>range(i + 1, n)</code> (i.e., <code>range(1, 1)</code>) will be empty. <code>max_side</code> remains 0, returning <code>0</code>. Correct, as a pair cannot be formed.</li>
<li><strong>Rectangles That Don't Intersect</strong>:<ul>
<li>If rectangles are entirely separate (e.g., <code>x1_tr &lt; x2_bl</code>), then <code>x_intersect_bl</code> will be <code>&gt;= x_intersect_tr</code>.</li>
<li>If they touch at a single point or line, the strict inequality <code>x_intersect_bl &lt; x_intersect_tr</code> or <code>y_intersect_bl &lt; y_intersect_tr</code> will fail.</li>
<li>In both cases, the <code>if</code> condition <code>x_intersect_bl &lt; x_intersect_tr and y_intersect_bl &lt; y_intersect_tr</code> correctly evaluates to <code>False</code>, preventing an update to <code>max_side</code> with a non-positive side length.</li>
</ul>
</li>
<li><strong>Perfectly Overlapping Rectangles</strong>: The intersection calculation correctly identifies the overlapping region (which might be one of the original rectangles if one is contained within the other). The <code>min(width, height)</code> correctly yields the side of the largest square within that overlapping region.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>No Major Algorithmic Improvement for General Case</strong>: For arbitrarily positioned axis-aligned rectangles, finding the largest intersection-square <em>among all pairs</em> inherently requires checking pairs. O(N^2) is typically the lower bound for this general problem without specific data properties or spatial indexing tricks.</li>
<li><strong>Minor Readability</strong>:<ul>
<li>Could encapsulate rectangle coordinates in a <code>Rectangle</code> class or named tuple for better readability, though for simple <code>[x,y]</code> lists, it's not strictly necessary.</li>
<li>The variable names are already quite clear.</li>
</ul>
</li>
<li><strong>Potential Optimization (Minor)</strong>: If <code>max_side</code> becomes very large, and coordinates have a known upper bound, it might be possible to prune some comparisons (e.g., if two rectangles are so far apart that their maximum possible intersection wouldn't yield a larger square), but this often adds complexity for little gain in typical competitive programming scenarios.</li>
<li><strong>Alternative Data Structures (for very large N with specific distributions)</strong>: For extremely large <code>N</code> (e.g., millions) where many rectangles do not intersect, a spatial data structure like a k-d tree or R-tree <em>might</em> be used to prune the search space for intersecting pairs. However, building and querying such structures also has overhead, and the constant factor for O(N^2) for smaller <code>N</code> might be faster. Given the problem statement, O(N^2) is the expected solution.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The O(N^2) complexity means that for <code>N</code> values in the order of <code>10^4</code> or more, the solution would become too slow (e.g., <code>10^8</code> operations is roughly the upper limit for typical 1-second time limits). For <code>N</code> up to a few thousand (e.g., <code>N=2000</code> gives <code>4 * 10^6</code> operations), it should be acceptable.</li>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this code. It performs purely mathematical computations on its input parameters and does not interact with external systems, files, or user-supplied unvalidated data in a way that could lead to injection attacks, data breaches, or resource exhaustion beyond CPU cycles. The input types (<code>List[List[int]]</code>) suggest that integer overflow is a potential concern only if coordinates are extremely large, but Python's arbitrary-precision integers handle this gracefully.</li>
</ul>


### Code:
```python
class Solution:
    def largestSquareArea(self, bottomLeft: List[List[int]], topRight: List[List[int]]) -> int:
        n = len(bottomLeft)
        max_side = 0  # Initialize the maximum side length found

        # Iterate through all unique pairs of rectangles
        for i in range(n):
            for j in range(i + 1, n):
                # Coordinates of the first rectangle
                x1_bl, y1_bl = bottomLeft[i][0], bottomLeft[i][1]
                x1_tr, y1_tr = topRight[i][0], topRight[i][1]

                # Coordinates of the second rectangle
                x2_bl, y2_bl = bottomLeft[j][0], bottomLeft[j][1]
                x2_tr, y2_tr = topRight[j][0], topRight[j][1]

                # Calculate the bottom-left and top-right coordinates of the intersection
                # The intersection's bottom-left x is the maximum of the two x_bls
                x_intersect_bl = max(x1_bl, x2_bl)
                # The intersection's bottom-left y is the maximum of the two y_bls
                y_intersect_bl = max(y1_bl, y2_bl)
                # The intersection's top-right x is the minimum of the two x_trs
                x_intersect_tr = min(x1_tr, x2_tr)
                # The intersection's top-right y is the minimum of the two y_trs
                y_intersect_tr = min(y1_tr, y2_tr)

                # Check if there is a valid intersection (width and height > 0)
                if x_intersect_bl < x_intersect_tr and y_intersect_bl < y_intersect_tr:
                    # Calculate the width and height of the intersecting rectangle
                    width = x_intersect_tr - x_intersect_bl
                    height = y_intersect_tr - y_intersect_bl

                    # The largest square that can fit has a side length equal to the minimum of the intersection's width and height
                    current_side = min(width, height)

                    # Update the maximum side length found so far
                    max_side = max(max_side, current_side)
        
        # The result is the area of the largest square, which is max_side squared
        return max_side * max_side
```
