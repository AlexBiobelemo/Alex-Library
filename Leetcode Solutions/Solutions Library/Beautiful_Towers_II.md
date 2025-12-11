## Beautiful Towers II
**Language:** python
**Tags:** monotonic stack,array,dynamic programming,prefix sum

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given an array <code>maxHeights</code>, we need to construct a "mountain array" <code>arr</code> of the same length such that:<ol>
<li><code>arr[i] &lt;= maxHeights[i]</code> for all <code>i</code>.</li>
<li>There exists a peak index <code>p</code> such that <code>arr[0] &lt;= arr[1] &lt;= ... &lt;= arr[p]</code> and <code>arr[p] &gt;= arr[p+1] &gt;= ... &gt;= arr[n-1]</code>.</li>
<li>The goal is to maximize the sum of elements in <code>arr</code>.</li>
</ol>
</li>
<li><strong>Approach:</strong> The code iterates through each possible index <code>i</code> in <code>maxHeights</code>, assuming <code>i</code> is the peak of the mountain array. For each <code>i</code>, it calculates the maximum possible sum of heights for the left side (<code>arr[0]</code> to <code>arr[i]</code>) and the right side (<code>arr[i]</code> to <code>arr[n-1]</code>) while adhering to the mountain property and <code>arr[k] &lt;= maxHeights[k]</code>. These calculations are optimized using a monotonic stack.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a two-pass approach using a monotonic stack to calculate prefix and suffix sums efficiently.</p>
<ol>
<li><p><strong>Left Pass (<code>left_sum</code> calculation):</strong></p>
<ul>
<li>Initializes <code>left_sum</code> array and an empty stack. <code>current_sum</code> tracks the sum of heights for the current left-sided mountain.</li>
<li>Iterates <code>i</code> from <code>0</code> to <code>n-1</code>. For each <code>i</code>, <code>maxHeights[i]</code> is considered the rightmost element (potential peak) of a left-sided mountain segment.</li>
<li>The stack maintains indices of <code>maxHeights</code> in increasing order of their values.</li>
<li><strong>Pop Phase:</strong> While the stack is not empty and <code>maxHeights[stack[-1]] &gt;= maxHeights[i]</code>, elements are popped. When an element at <code>pop_idx</code> is popped, it means <code>maxHeights[i]</code> is a smaller (or equal) "cap" than <code>maxHeights[pop_idx]</code> was. Its contribution to <code>current_sum</code> (for the range it capped) is subtracted.<ul>
<li>The range <code>maxHeights[pop_idx]</code> capped was from <code>prev_idx_on_stack + 1</code> to <code>pop_idx</code>.</li>
</ul>
</li>
<li><strong>Add Phase:</strong> After popping, <code>maxHeights[i]</code> becomes the new "cap" for a range. Its contribution (value <code>maxHeights[i]</code> for all elements from <code>prev_idx_on_stack + 1</code> to <code>i</code>) is added to <code>current_sum</code>.</li>
<li>The current index <code>i</code> is pushed onto the stack.</li>
<li><code>left_sum[i]</code> stores the <code>current_sum</code>. This <code>current_sum</code> represents <code>sum(arr[k])</code> for <code>0 &lt;= k &lt;= i</code> where <code>arr[k] = min(maxHeights[k], ..., maxHeights[i])</code>.</li>
</ul>
</li>
<li><p><strong>Right Pass (<code>right_sum</code> calculation):</strong></p>
<ul>
<li>Symmetric to the left pass, but iterates <code>i</code> from <code>n-1</code> down to <code>0</code>. <code>maxHeights[i]</code> is considered the leftmost element (potential peak) of a right-sided mountain segment.</li>
<li>The stack logic is identical, but <code>next_idx_on_stack</code> is used for range calculations (effectively <code>i</code> to <code>next_idx_on_stack - 1</code>).</li>
<li><code>right_sum[i]</code> stores the <code>current_sum</code>, representing <code>sum(arr[k])</code> for <code>i &lt;= k &lt;= n-1</code> where <code>arr[k] = min(maxHeights[i], ..., maxHeights[k])</code>.</li>
</ul>
</li>
<li><p><strong>Final Calculation:</strong></p>
<ul>
<li>Iterates <code>i</code> from <code>0</code> to <code>n-1</code>.</li>
<li>For each <code>i</code>, the total sum of heights for a mountain with peak <code>i</code> is <code>left_sum[i] + right_sum[i] - maxHeights[i]</code>. <code>maxHeights[i]</code> is subtracted because it is counted in both <code>left_sum[i]</code> and <code>right_sum[i]</code>.</li>
<li>The maximum of these total sums is the final result.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Monotonic Stack for Range Sums:</strong> This is the core algorithm. It efficiently calculates prefix/suffix sums where elements in a range are constrained by a minimum value (the "cap" from the stack). This avoids O(N^2) brute-force recalculation for each potential peak.</li>
<li><strong>Two Independent Passes:</strong> Separating the left-to-right and right-to-left calculations simplifies the stack logic and makes it more manageable than trying to do it in a single pass.</li>
<li><strong>Pre-calculation of <code>left_sum</code> and <code>right_sum</code>:</strong> This dynamic programming-like approach stores intermediate results, allowing the final step to be a simple O(N) iteration.</li>
<li><strong>Handling Stack Boundaries:</strong> Using <code>else -1</code> for <code>prev_idx_on_stack</code> and <code>else n</code> for <code>next_idx_on_stack</code> correctly extends the ranges to the array boundaries when the stack is empty.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Initializing <code>left_sum</code> and <code>right_sum</code>: O(N).</li>
<li>The left pass iterates <code>N</code> times. Each element is pushed onto the stack once and popped from the stack at most once. Operations inside the loop (stack manipulations, arithmetic) are O(1). Thus, the left pass is O(N) amortized.</li>
<li>The right pass is symmetric to the left pass, also O(N) amortized.</li>
<li>The final calculation loop iterates <code>N</code> times: O(N).</li>
<li>Total time complexity is O(N).</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li><code>left_sum</code> and <code>right_sum</code> arrays: O(N) each.</li>
<li>The <code>stack</code>: In the worst case (e.g., <code>maxHeights</code> is strictly increasing or decreasing), the stack can hold up to <code>N</code> elements. O(N).</li>
<li>Total space complexity is O(N).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>maxHeights</code>:</strong> <code>n</code> would be 0. The loops won't execute. <code>max_total_sum</code> remains <code>0</code>, which is the correct sum for an empty array.</li>
<li><strong>Single element <code>maxHeights</code> (e.g., <code>[5]</code>):</strong><ul>
<li><code>left_sum[0]</code> will be <code>5</code>.</li>
<li><code>right_sum[0]</code> will be <code>5</code>.</li>
<li><code>max_total_sum</code> becomes <code>max(0, 5 + 5 - 5) = 5</code>. Correct.</li>
</ul>
</li>
<li><strong>All elements are the same (e.g., <code>[7, 7, 7]</code>):</strong> The logic correctly handles equality.<ul>
<li><code>left_sum</code> will be <code>[7, 14, 21]</code>.</li>
<li><code>right_sum</code> will be <code>[21, 14, 7]</code>.</li>
<li>Final sums for peaks at <code>0, 1, 2</code> will be <code>(7+21-7)</code>, <code>(14+14-7)</code>, <code>(21+7-7)</code>, all resulting in <code>21</code>. Max is <code>21</code>. Correct.</li>
</ul>
</li>
<li><strong>Strictly increasing/decreasing arrays:</strong> The monotonic stack mechanism correctly identifies the ranges and applies the appropriate heights. For instance, with <code>[1, 2, 3]</code> (increasing), the peak must be at <code>3</code> to maximize the sum, resulting in <code>1+2+3=6</code>. The code yields this correctly.</li>
<li><strong><code>prev_idx_on_stack</code> and <code>next_idx_on_stack</code>:</strong> The use of <code>else -1</code> and <code>else n</code> when the stack is empty is crucial. It ensures that when a value <code>maxHeights[i]</code> becomes the "cap" for a range extending all the way to the array's edge, the segment length calculation <code>(i - (-1))</code> or <code>(n - i)</code> correctly includes the entire segment.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong><ul>
<li>The conditional assignments for <code>prev_idx_on_stack</code> and <code>next_idx_on_stack</code> are repeated. Extracting them to a helper function or assigning them to a local variable just before use could slightly improve clarity.</li>
<li>Comments explaining the role of the monotonic stack, especially the <code>while</code> loop's pop condition and the <code>current_sum</code> updates (why subtract, why add, what <code>(idx - prev_idx_on_stack)</code> represents), would be beneficial for future maintainers.</li>
</ul>
</li>
<li><strong>No Major Performance Alternatives:</strong> The O(N) time complexity is optimal because every height must be considered. Any brute-force approach iterating over all possible peaks and recalculating sums would be O(N^2), which is too slow for larger <code>N</code>.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Integer Overflow (Not an issue in Python):</strong> In languages with fixed-size integers (e.g., C++, Java), if <code>n</code> and <code>maxHeights[i]</code> are very large, <code>current_sum</code> or <code>max_total_sum</code> could exceed the maximum value of a standard <code>int</code> or <code>long</code>. Python's arbitrary-precision integers automatically handle this, so it's not a concern here.</li>
<li><strong>Memory Usage:</strong> For <code>N</code> up to typical competitive programming limits (e.g., <code>10^5</code> to <code>10^6</code>), O(N) space for three arrays and a stack is generally acceptable within typical memory limits (e.g., 256MB). Each integer in Python can take more space than in C++/Java, but for the typical constraints of this problem, it should be fine.</li>
</ul>


### Code:
```python
from typing import List

class Solution:
    def maximumSumOfHeights(self, maxHeights: List[int]) -> int:
        n = len(maxHeights)
        
        left_sum = [0] * n
        stack = [] 
        current_sum = 0

        for i in range(n):
            while stack and maxHeights[stack[-1]] >= maxHeights[i]:
                pop_idx = stack.pop()
                prev_idx_on_stack = stack[-1] if stack else -1
                current_sum -= (pop_idx - prev_idx_on_stack) * maxHeights[pop_idx]
            
            prev_idx_on_stack = stack[-1] if stack else -1
            current_sum += (i - prev_idx_on_stack) * maxHeights[i]
            
            stack.append(i)
            left_sum[i] = current_sum
            
        right_sum = [0] * n
        stack = [] 
        current_sum = 0

        for i in range(n - 1, -1, -1):
            while stack and maxHeights[stack[-1]] >= maxHeights[i]:
                pop_idx = stack.pop()
                next_idx_on_stack = stack[-1] if stack else n
                current_sum -= (next_idx_on_stack - pop_idx) * maxHeights[pop_idx]
            
            next_idx_on_stack = stack[-1] if stack else n
            current_sum += (next_idx_on_stack - i) * maxHeights[i]
            
            stack.append(i)
            right_sum[i] = current_sum
            
        max_total_sum = 0
        for i in range(n):
            max_total_sum = max(max_total_sum, left_sum[i] + right_sum[i] - maxHeights[i])
            
        return max_total_sum
```
