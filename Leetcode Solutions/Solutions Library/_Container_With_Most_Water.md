##  Container With Most Water
**Language:** python
**Tags:** python,two-pointers,algorithm,array,max-area

### Description:
<p>This code implements a solution to the "Container With Most Water" problem, a common algorithm challenge.</p>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to find the maximum amount of water that can be contained between two vertical lines, given an array of non-negative integers representing the heights of these lines. The lines are assumed to be drawn at consecutive indices (i.e., <code>i</code> and <code>j</code>), and the x-axis forms the bottom of the container. The water level will always be limited by the shorter of the two lines.</p>
<h3>2. How It Works</h3>
<p>The algorithm uses a <strong>two-pointer approach</strong>:</p>
<ul>
<li><strong>Initialization</strong>: Two pointers, <code>left</code> and <code>right</code>, are initialized at the beginning and end of the <code>height</code> list, respectively. <code>max_water</code> is set to 0 to store the maximum area found so far.</li>
<li><strong>Iteration</strong>: The <code>while left &lt; right</code> loop continues as long as the pointers haven't crossed.<ul>
<li><strong>Calculate Width</strong>: The <code>width</code> of the current container is simply the distance between the pointers (<code>right - left</code>).</li>
<li><strong>Determine Height</strong>: The <code>h</code> (height) of the container is determined by the shorter of the two lines pointed to by <code>left</code> and <code>right</code> (<code>min(height[left], height[right])</code>). Water cannot overflow, so it's limited by the lower boundary.</li>
<li><strong>Calculate Area</strong>: The <code>current_area</code> is <code>h * width</code>.</li>
<li><strong>Update Max</strong>: <code>max_water</code> is updated if <code>current_area</code> is greater than the current <code>max_water</code>.</li>
<li><strong>Move Pointer</strong>: This is the critical step. To potentially find a larger container, the algorithm moves the pointer that points to the <em>shorter</em> line inwards. The rationale is that moving the taller line inward would guarantee a decrease in width, and the height would still be limited by the <em>same</em> shorter line (or an even shorter one), thus definitely reducing or keeping the area the same. Moving the shorter line offers the possibility of encountering a taller line that could increase the container's height. If both heights are equal, either pointer can be moved (the current code moves <code>right</code>).</li>
</ul>
</li>
<li><strong>Return</strong>: Once the pointers cross (<code>left &gt;= right</code>), the loop terminates, and <code>max_water</code> holds the maximum possible area.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure</strong>: A <code>list</code> (<code>height</code>) is used to store the integer heights of the lines. This is a natural choice for sequential access and fixed-size data.</li>
<li><strong>Algorithm</strong>: The <strong>two-pointer strategy</strong> is the core design decision.<ul>
<li><strong>Trade-offs</strong>: This approach sacrifices checking all possible pairs (which would be O(N^2)) for a more efficient O(N) solution. The crucial trade-off is the proof that moving the shorter pointer <em>never skips an optimal solution</em>. Any pair involving the current shorter line and any line <em>inside</em> the current taller line would have a smaller width and an equal or smaller height (because the current shorter line is still the limiting factor), hence a smaller area.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: <strong>O(N)</strong><ul>
<li>The <code>left</code> and <code>right</code> pointers start at opposite ends and move towards each other, making exactly <code>N-1</code> movements in total (where <code>N</code> is the length of the <code>height</code> list). Each step involves a constant number of operations (comparison, subtraction, multiplication). Thus, the algorithm makes a single pass over the data.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <strong>O(1)</strong><ul>
<li>The algorithm uses a constant amount of extra space for variables like <code>max_water</code>, <code>left</code>, <code>right</code>, <code>width</code>, <code>h</code>, and <code>current_area</code>, regardless of the input size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>height</code> list</strong>: If <code>height</code> is empty, <code>len(height) - 1</code> would be <code>-1</code>. <code>left</code> would be <code>0</code>, <code>right</code> would be <code>-1</code>. The <code>while left &lt; right</code> condition (<code>0 &lt; -1</code>) would immediately be false, and <code>0</code> would be returned, which is correct as no container can be formed. (Note: LeetCode problems usually specify non-empty inputs for this problem).</li>
<li><strong>List with two elements</strong>: <code>left=0</code>, <code>right=1</code>. The loop runs once, correctly calculating the area and returning it.</li>
<li><strong>All heights are the same</strong>: Pointers move inward (e.g., <code>right</code> moves if <code>height[left] == height[right]</code>). The largest width is considered first, and subsequent calculations correctly update <code>max_water</code>.</li>
<li><strong>Heights in increasing/decreasing order</strong>: The two-pointer logic correctly navigates these scenarios, ensuring the shorter pointer always moves, seeking a potentially taller boundary.</li>
<li><strong>One or both bounding heights are 0</strong>: If <code>height[left]</code> or <code>height[right]</code> is 0, <code>min(height[left], height[right])</code> will be 0, resulting in <code>current_area = 0</code>. This is correct, as a container with a height of 0 holds no water.</li>
<li><strong>Correctness of Pointer Movement</strong>: The core proof for correctness lies in understanding why moving the <em>shorter</em> pointer is optimal. If we move the <em>taller</em> pointer, the width <em>decreases</em>, and the height is <em>still limited by the original shorter line</em> (or potentially an even shorter new line if the taller line moved to a shorter one). Therefore, moving the taller pointer would never lead to a larger area. Moving the shorter pointer allows for the possibility of finding a taller line that could increase the effective height, potentially overcoming the decreased width.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already very readable due to clear variable names and straightforward logic. No significant improvements are needed here.</li>
<li><strong>Performance</strong>: The two-pointer approach is the optimal solution in terms of time complexity (O(N)), so there aren't algorithmic improvements that would offer a better asymptotic performance.</li>
<li><strong>Robustness</strong>:<ul>
<li><strong>Input Validation</strong>: For production code, one might add explicit checks for <code>height</code> being non-empty or containing only non-negative integers, although problem constraints often guarantee these conditions.<pre><code class="language-python">if not height or len(height) &lt; 2:
    return 0 # Or raise an error
</code></pre>
</li>
</ul>
</li>
<li><strong>Alternative (Less Efficient)</strong>: A brute-force approach would involve checking every possible pair of lines (<code>(i, j)</code> where <code>i &lt; j</code>). This would calculate <code>min(height[i], height[j]) * (j - i)</code> for all pairs and find the maximum. This has a time complexity of O(N^2), which is significantly less efficient for larger inputs.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this algorithm. It operates on numerical data and does not involve external inputs, sensitive data handling, or complex interactions that could lead to exploits.</li>
<li><strong>Performance</strong>: As established, the algorithm is highly performant with O(N) time complexity, making it suitable for large datasets. No specific performance bottlenecks or concerns exist beyond the fundamental limits of processing <code>N</code> elements.</li>
</ul>


### Code:
```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        max_water = 0
        left = 0
        right = len(height) - 1

        while left < right:
            # Calculate the current width
            width = right - left
            # Determine the height of the container (limited by the shorter line)
            h = min(height[left], height[right])
            # Calculate the current area
            current_area = h * width
            # Update max_water if the current area is greater
            max_water = max(max_water, current_area)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        
        return max_water
```
