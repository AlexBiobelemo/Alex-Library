## Trapping Rain Water
**Language:** python
**Tags:** python,two pointers,trapping rain water,algorithm,array

### Description:
<p>This Python code implements an efficient algorithm to solve the "Trapping Rain Water" problem.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Calculate the total amount of rainwater that can be trapped between bars of varying heights. Imagine an elevation map where <code>height[i]</code> represents the height of a bar at position <code>i</code>.</li>
<li><strong>Input:</strong> A list of non-negative integers <code>height</code>, where each integer represents the height of a bar.</li>
<li><strong>Output:</strong> An integer representing the total volume of water that can be trapped.</li>
<li><strong>Goal:</strong> Find an optimal solution that correctly computes the trapped water with good performance characteristics.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The code uses a two-pointer approach, iterating inwards from both ends of the <code>height</code> array.</p>
<ul>
<li><strong>Initialization:</strong><ul>
<li><code>left</code> and <code>right</code> pointers start at the beginning and end of the array, respectively.</li>
<li><code>left_max</code> and <code>right_max</code> store the maximum height encountered so far from the left and right sides, respectively. They are initially 0.</li>
<li><code>total_water</code> accumulates the trapped water.</li>
</ul>
</li>
<li><strong>Main Loop (<code>while left &lt; right</code>):</strong><ol>
<li><strong>Compare Heights:</strong> The core idea is to move the pointer associated with the <em>shorter</em> bar. This is because the amount of water that can be trapped at the current position is limited by the <em>shorter</em> of its two bounding bars.</li>
<li><strong>Move <code>left</code> pointer (if <code>height[left] &lt; height[right]</code>):</strong><ul>
<li><strong>Update <code>left_max</code>:</strong> If <code>height[left]</code> is greater than or equal to the current <code>left_max</code>, it means this bar is taller than any previous bar from the left. Update <code>left_max</code> to <code>height[left]</code>. No water is trapped here as it's part of a rising wall.</li>
<li><strong>Calculate Water:</strong> If <code>height[left]</code> is <em>less</em> than <code>left_max</code>, it means <code>left_max</code> forms a left boundary, and there must be a bar on the right side at least as tall as <code>height[left]</code> (specifically, <code>height[right]</code> is guaranteed to be taller than <code>height[left]</code>, and <code>right_max</code> is at least <code>height[right]</code>). The water trapped above <code>height[left]</code> is <code>left_max - height[left]</code>. Add this to <code>total_water</code>.</li>
<li>Increment <code>left</code>.</li>
</ul>
</li>
<li><strong>Move <code>right</code> pointer (if <code>height[right] &lt;= height[left]</code>):</strong><ul>
<li>Similar logic applies symmetrically.</li>
<li><strong>Update <code>right_max</code>:</strong> If <code>height[right]</code> is greater than or equal to <code>right_max</code>, update <code>right_max</code>.</li>
<li><strong>Calculate Water:</strong> If <code>height[right]</code> is <em>less</em> than <code>right_max</code>, add <code>right_max - height[right]</code> to <code>total_water</code>.</li>
<li>Decrement <code>right</code>.</li>
</ul>
</li>
</ol>
</li>
<li><strong>Termination:</strong> The loop continues until <code>left</code> and <code>right</code> pointers meet or cross, at which point all possible water has been calculated.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Two-Pointer Approach:</strong> This is the critical design choice.<ul>
<li>It allows for a single pass through the array.</li>
<li>By always moving the pointer corresponding to the shorter bar, the algorithm ensures that the potential "bottleneck" (the shorter of the two bounding walls for the current segment) is considered and correctly processed.</li>
<li>The "other" side's maximum (<code>left_max</code> or <code>right_max</code>) is guaranteed to be at least as tall as the current bar's maximum, because we only move the shorter pointer.</li>
</ul>
</li>
<li><strong><code>left_max</code> and <code>right_max</code>:</strong> Maintaining these running maximums locally (instead of pre-calculating them in separate arrays) is key to achieving O(1) space complexity.</li>
<li><strong>Trade-offs:</strong> The two-pointer method provides an optimal balance between time and space complexity, achieving O(N) time and O(1) space, which is generally preferred over O(N) time and O(N) space solutions (like dynamic programming or stack-based approaches) if possible.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The <code>while</code> loop iterates, moving either the <code>left</code> or <code>right</code> pointer one step at a time.</li>
<li>In the worst case, both pointers traverse the entire array from their respective ends until they meet.</li>
<li>Each step inside the loop involves constant time operations (comparisons, additions, assignments).</li>
<li>Therefore, the total time complexity is linear with respect to the number of bars <code>N</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>left</code>, <code>right</code>, <code>left_max</code>, <code>right_max</code>, <code>total_water</code>, <code>n</code>) regardless of the input array size.</li>
<li>No auxiliary data structures (like additional arrays or stacks) are created proportional to the input size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty or Short Input:</strong><ul>
<li><code>height = []</code>: Handled by <code>if not height</code>, returns <code>0</code>.</li>
<li><code>height = [5]</code>: Handled by <code>len(height) &lt; 3</code>, returns <code>0</code>. (Cannot trap water with less than 3 bars).</li>
<li><code>height = [1, 2]</code>: Handled by <code>len(height) &lt; 3</code>, returns <code>0</code>.</li>
<li><strong>Correctness:</strong> These initial checks correctly return 0 as no water can be trapped.</li>
</ul>
</li>
<li><strong>Monotonically Increasing/Decreasing Heights:</strong><ul>
<li><code>height = [1, 2, 3, 4, 5]</code>: <code>left_max</code> and <code>right_max</code> will always update to the current bar's height. <code>total_water</code> will remain <code>0</code>. Correct.</li>
<li><code>height = [5, 4, 3, 2, 1]</code>: Similarly, <code>total_water</code> will remain <code>0</code>. Correct.</li>
</ul>
</li>
<li><strong>V-shaped/U-shaped/Complex Heights:</strong><ul>
<li><code>height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]</code></li>
<li>The algorithm correctly handles these by always considering the shorter boundary. For instance, when <code>height[left]</code> is low, it contributes water based on <code>left_max</code>. <code>left_max</code> correctly tracks the highest wall seen <em>so far</em> on the left. The <code>height[right]</code> being taller guarantees that <code>left_max</code> is the actual limiting factor from the left side. The symmetrical logic applies to the right side.</li>
</ul>
</li>
<li><strong>Justification for Correctness:</strong> The critical insight is that if <code>height[left] &lt; height[right]</code>, then the amount of water trapped at <code>left</code> (and any points between <code>left</code> and <code>left_max</code> from its current left side) is determined solely by <code>left_max</code>. Why? Because we know there is <em>at least one</em> bar on the right side (<code>height[right]</code>) that is taller than <code>height[left]</code>. This <code>height[right]</code> (or <code>right_max</code>, which is at least <code>height[right]</code>) acts as a sufficient right boundary for any water trapped at <code>left</code> (given <code>left_max</code> as the left boundary). The same logic applies when <code>height[right] &lt;= height[left]</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already quite readable. Variable names are clear, and the logic is straightforward for this well-known algorithm.</li>
<li><strong>Performance:</strong> The current two-pointer solution is already optimal in terms of both time (O(N)) and space (O(1)) complexity. No further significant performance improvements are possible with a single pass through the data.</li>
<li><strong>Alternatives (with different space complexity):</strong><ul>
<li><strong>Dynamic Programming (DP):</strong><ul>
<li>Calculate <code>left_max_arr[i]</code> = maximum height to the left of <code>i</code> (including <code>i</code>).</li>
<li>Calculate <code>right_max_arr[i]</code> = maximum height to the right of <code>i</code> (including <code>i</code>).</li>
<li>Iterate <code>i</code> from <code>0</code> to <code>n-1</code>: <code>total_water += max(0, min(left_max_arr[i], right_max_arr[i]) - height[i])</code>.</li>
<li>Complexity: O(N) time, O(N) space.</li>
</ul>
</li>
<li><strong>Stack-based Approach:</strong><ul>
<li>Iterate through the <code>height</code> array. Maintain a monotonic decreasing stack of indices.</li>
<li>When encountering a bar taller than the top of the stack, pop elements from the stack, calculate trapped water using the popped bar, the new taller bar, and the new stack top (as the left boundary).</li>
<li>Complexity: O(N) time, O(N) space (for the stack in worst case).</li>
</ul>
</li>
</ul>
</li>
<li>The provided two-pointer solution is generally preferred due to its superior O(1) space complexity.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code performs purely mathematical operations on integer inputs. There are no inherent security vulnerabilities like injection, data exposure, or resource exhaustion beyond typical runtime behavior for large N.</li>
<li><strong>Performance:</strong> The algorithm is highly performant. Its linear time complexity means it scales well with larger inputs, and its constant space complexity ensures minimal memory footprint, making it suitable for environments with strict memory constraints. No bottlenecks or inefficient operations are present.</li>
</ul>


### Code:
```python
class Solution(object):
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if not height or len(height) < 3:
            return 0

        n = len(height)
        left = 0
        right = n - 1
        left_max = 0
        right_max = 0
        total_water = 0

        while left < right:
            if height[left] < height[right]:
                if height[left] >= left_max:
                    left_max = height[left]
                else:
                    total_water += left_max - height[left]
                left += 1
            else:
                if height[right] >= right_max:
                    right_max = height[right]
                else:
                    total_water += right_max - height[right]
                right -= 1
        
        return total_water
```
