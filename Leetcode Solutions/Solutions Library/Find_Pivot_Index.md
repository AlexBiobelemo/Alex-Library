## Find Pivot Index
**Language:** python
**Tags:** python,arrays,algorithms,prefix sum

### Description:
<p>This Python code defines a method <code>pivotIndex</code> that finds the "pivot index" of a list of numbers.</p>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of the <code>pivotIndex</code> method is to locate the leftmost index in a given list of integers <code>nums</code> such that the sum of all elements strictly to the left of the index is equal to the sum of all elements strictly to the right of the index. If no such index exists, it should return -1.</p>
<h3>2. How It Works</h3>
<p>The algorithm operates in two conceptual phases:</p>
<ul>
<li><strong>Initial Sum Calculation</strong>: First, it computes the <code>total_sum</code> of all numbers in the input list <code>nums</code>. This is done once at the beginning.</li>
<li><strong>Single Pass Iteration</strong>: It then iterates through the <code>nums</code> list using a <code>for</code> loop, keeping track of the <code>left_sum</code> (sum of elements encountered so far, <em>excluding</em> the current element).<ul>
<li>For each element <code>num</code> at index <code>i</code>:<ul>
<li>It calculates <code>right_sum</code> by subtracting the <code>left_sum</code> and the <code>current num</code> from the <code>total_sum</code>. This effectively gives the sum of elements to the right of the current index.</li>
<li>It compares <code>left_sum</code> and <code>right_sum</code>. If they are equal, the current index <code>i</code> is a pivot index, and the method immediately returns <code>i</code>.</li>
<li>If not equal, the current <code>num</code> is added to <code>left_sum</code> to prepare for the next iteration, making it the sum of elements <em>to the left of the next element</em>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>No Pivot Found</strong>: If the loop completes without finding a pivot index, it means no such index exists, and the method returns -1.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>One-Pass Optimization</strong>: The core design decision is to calculate the <code>total_sum</code> once upfront. This allows the <code>right_sum</code> to be calculated in O(1) time during each iteration (<code>total_sum - left_sum - num</code>), rather than re-summing the right side of the array repeatedly, which would lead to an O(N^2) solution.</li>
<li><strong>In-Place Sum Tracking</strong>: It uses a <code>left_sum</code> variable that is updated incrementally. This avoids the need for an additional data structure like a prefix sum array, keeping memory usage minimal.</li>
<li><strong>Leftmost Pivot</strong>: The design inherently returns the <em>leftmost</em> pivot index because it returns immediately upon finding the first matching index during its left-to-right scan.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(N)<ul>
<li><code>sum(nums)</code> takes O(N) time to iterate through the list once.</li>
<li>The <code>for</code> loop also iterates through the list once, performing constant-time operations inside (subtraction, addition, comparison).</li>
<li>Therefore, the total time complexity is O(N) + O(N) = O(N).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(1)<ul>
<li>Only a few extra variables (<code>total_sum</code>, <code>left_sum</code>, <code>right_sum</code>, <code>i</code>, <code>num</code>) are used, regardless of the input list size. This constitutes constant extra space.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles several edge cases correctly:</p>
<ul>
<li><strong>Empty list (<code>[]</code>)</strong>:<ul>
<li><code>total_sum</code> will be 0.</li>
<li>The <code>for</code> loop will not execute.</li>
<li>Returns -1, which is correct as an empty list has no pivot index.</li>
</ul>
</li>
<li><strong>Single element list (<code>[x]</code>)</strong>:<ul>
<li><code>total_sum</code> will be <code>x</code>.</li>
<li>For <code>i=0, num=x</code>: <code>left_sum</code> is 0. <code>right_sum = x - 0 - x = 0</code>.</li>
<li><code>left_sum == right_sum</code> (0 == 0) is true.</li>
<li>Returns 0, which is correct (the sum to the left is 0, and the sum to the right is 0).</li>
</ul>
</li>
<li><strong>List with zero values (<code>[0, 0, 0]</code>)</strong>:<ul>
<li><code>total_sum</code> is 0.</li>
<li>For <code>i=0, num=0</code>: <code>left_sum=0</code>, <code>right_sum=0</code>. Returns 0. Correct.</li>
</ul>
</li>
<li><strong>No pivot index found</strong>: If the loop completes without <code>left_sum == right_sum</code> ever being true, it correctly returns -1.</li>
<li><strong>Negative numbers</strong>: The sum calculations handle negative numbers correctly without issues.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The variable names (<code>total_sum</code>, <code>left_sum</code>, <code>right_sum</code>) are clear and self-explanatory. The logic is straightforward, making the code highly readable.</li>
<li><strong>Performance</strong>: The current solution is optimal in terms of time complexity (O(N)) and space complexity (O(1)). There are no significant performance improvements possible for the general case.</li>
<li><strong>Alternative (Conceptual - not necessarily better)</strong>:<ul>
<li><strong>Prefix Sum Array</strong>: One could pre-compute a prefix sum array (or suffix sum array). This would require O(N) additional space but would allow <code>left_sum</code> and <code>right_sum</code> to be fetched in O(1) after the initial O(N) pre-computation. However, the current solution achieves the same O(N) time with O(1) space, making it more memory-efficient.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: As noted, the solution is highly efficient (O(N) time, O(1) space) and scales linearly with the input size. There are no inherent performance bottlenecks for typical input constraints.</li>
<li><strong>Integer Overflow</strong>: In languages with fixed-size integers (e.g., C, C++, Java), if the sum of numbers can exceed the maximum value of the integer type, an overflow could occur. Python's integers handle arbitrary precision, so this is not a concern for this specific Python implementation. However, it's a critical consideration if porting this logic to other languages.</li>
<li><strong>Security</strong>: For this purely computational algorithm, there are no direct security implications or vulnerabilities to consider, as it does not interact with external systems, handle sensitive data, or involve complex parsing.</li>
</ul>


### Code:
```python
class Solution(object):
    def pivotIndex(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        total_sum = sum(nums)
        left_sum = 0
        for i, num in enumerate(nums):
            right_sum = total_sum - left_sum - num
            if left_sum == right_sum:
                return i
            left_sum += num
        return -1
```
