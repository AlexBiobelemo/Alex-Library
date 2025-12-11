## Range Sum Query - Immutable
**Language:** python
**Tags:** prefix sums,range sum query,data structure,algorithm

### Description:
<p>This code implements a solution for efficiently calculating the sum of elements within a given range of an array, specifically optimized for scenarios where the array elements do not change after initialization.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given an integer array <code>nums</code>, find the sum of the elements between indices <code>left</code> and <code>right</code> (inclusive) for multiple queries.</li>
<li><strong>Intent:</strong> To provide <code>O(1)</code> time complexity for each range sum query after an initial <code>O(N)</code> setup, where <code>N</code> is the number of elements in <code>nums</code>. This makes it highly efficient for scenarios with many <code>sumRange</code> calls on an immutable array.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to precompute a "prefix sum" array during the initialization phase.</p>
<ul>
<li><p><strong>Initialization (<code>__init__</code>)</strong>:</p>
<ol>
<li>A <code>prefix_sum</code> array is created, starting with <code>0</code>. This <code>0</code> at index <code>0</code> is a sentinel value that simplifies calculations for ranges starting at index <code>0</code>.</li>
<li>It iterates through the input <code>nums</code> array. For each number, it adds it to the previous prefix sum and appends the result to <code>self.prefix_sum</code>.</li>
<li><code>self.prefix_sum[i]</code> stores the sum of <code>nums[0]</code> up to <code>nums[i-1]</code>.<ul>
<li>Example: If <code>nums = [1, 2, 3]</code>, then <code>prefix_sum</code> becomes <code>[0, 1, 3, 6]</code>.<ul>
<li><code>prefix_sum[0] = 0</code> (sum of empty prefix)</li>
<li><code>prefix_sum[1] = nums[0] = 1</code></li>
<li><code>prefix_sum[2] = nums[0] + nums[1] = 1 + 2 = 3</code></li>
<li><code>prefix_sum[3] = nums[0] + nums[1] + nums[2] = 1 + 2 + 3 = 6</code></li>
</ul>
</li>
</ul>
</li>
</ol>
</li>
<li><p><strong>Sum Range Query (<code>sumRange</code>)</strong>:</p>
<ol>
<li>To find the sum of <code>nums</code> from index <code>left</code> to <code>right</code> (inclusive), the method uses the precomputed <code>prefix_sum</code> array.</li>
<li>The sum is calculated as <code>self.prefix_sum[right + 1] - self.prefix_sum[left]</code>.<ul>
<li>This works because <code>self.prefix_sum[right + 1]</code> contains the sum <code>nums[0] + ... + nums[right]</code>, and <code>self.prefix_sum[left]</code> contains the sum <code>nums[0] + ... + nums[left-1]</code>. Subtracting the latter from the former isolates the sum <code>nums[left] + ... + nums[right]</code>.</li>
</ul>
</li>
</ol>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Prefix Sum Array</strong>: The fundamental data structure chosen. It allows for <code>O(1)</code> query time at the cost of <code>O(N)</code> initialization time and <code>O(N)</code> space.</li>
<li><strong>Sentinel Value at Index 0</strong>: Storing <code>0</code> at <code>self.prefix_sum[0]</code> is a clever technique. It simplifies the <code>sumRange</code> logic by ensuring that <code>prefix_sum[left]</code> is always accessible and correctly represents the sum <em>before</em> index <code>left</code>, even when <code>left</code> is <code>0</code>. Without it, handling <code>left = 0</code> would require a special condition (e.g., <code>prefix_sum[right+1]</code>).</li>
<li><strong>Immutable Array Assumption</strong>: The design implicitly assumes that the <code>nums</code> array does not change after initialization. If elements were to be updated, this approach would be inefficient as it would require rebuilding (or partially updating) the entire <code>prefix_sum</code> array, taking <code>O(N)</code> time per update.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><code>__init__(self, nums)</code>: <code>O(N)</code>, where <code>N</code> is the length of <code>nums</code>. This is due to the single loop iterating through all elements to build the <code>prefix_sum</code> array.</li>
<li><code>sumRange(self, left, right)</code>: <code>O(1)</code>. This involves only two array lookups and one subtraction, all constant-time operations.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>__init__(self, nums)</code>: <code>O(N)</code>. An additional <code>prefix_sum</code> array of size <code>N + 1</code> is created to store the prefix sums.</li>
<li><code>sumRange(self, left, right)</code>: <code>O(1)</code>. It only uses a constant amount of extra space for variables.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>nums</code> array</strong>:<ul>
<li>If <code>nums</code> is empty, <code>self.prefix_sum</code> will be <code>[0]</code>. According to typical problem constraints, <code>sumRange</code> would not be called with valid <code>left</code>/<code>right</code> indices in this case, as <code>0 &lt;= left &lt;= right &lt; len(nums)</code> would not hold. If called with <code>left=0, right=-1</code> (an empty range), it would return <code>prefix_sum[0] - prefix_sum[0] = 0</code>, which is correct.</li>
</ul>
</li>
<li><strong><code>left == right</code></strong>: Sum of a single element. <code>prefix_sum[left + 1] - prefix_sum[left]</code> correctly yields <code>nums[left]</code>.</li>
<li><strong><code>left == 0</code></strong>: Sum starting from the beginning. <code>prefix_sum[right + 1] - prefix_sum[0]</code> correctly yields <code>nums[0] + ... + nums[right]</code>, as <code>prefix_sum[0]</code> is <code>0</code>.</li>
<li><strong><code>right == len(nums) - 1</code></strong>: Sum up to the end of the original array. This works correctly as <code>prefix_sum[len(nums)]</code> holds the total sum of <code>nums</code>.</li>
<li><strong>Negative numbers</strong>: The logic of summing and subtracting works flawlessly with negative integers, correctly accumulating their values.</li>
</ul>
<p>The implementation handles these cases correctly due to the robust definition of the prefix sum and the strategic use of the <code>0</code> sentinel value.</p>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite readable. Variable names (<code>prefix_sum</code>, <code>num</code>, <code>left</code>, <code>right</code>) are clear, and the method names are descriptive. Python's type hints (e.g., <code>:type nums: List[int]</code>) further enhance clarity.</li>
<li><strong>Alternatives for Mutability</strong>:<ul>
<li><strong>Segment Tree</strong>: If <code>nums</code> needed to be updated frequently <em>and</em> range sums queried, a Segment Tree would be a better choice. It supports <code>O(log N)</code> for both updates and range sum queries. Its initialization is <code>O(N)</code> and space <code>O(N)</code>. More complex to implement.</li>
<li><strong>Fenwick Tree (Binary Indexed Tree - BIT)</strong>: Similar to a Segment Tree, often simpler to implement for sum queries. Also provides <code>O(log N)</code> for updates and queries.</li>
<li><strong>Naive Approach</strong>: For very few <code>sumRange</code> queries (e.g., only one), one could simply loop from <code>left</code> to <code>right</code> in <code>sumRange</code>. This would make <code>__init__</code> <code>O(1)</code> but <code>sumRange</code> <code>O(N)</code>. This approach is only viable if <code>N</code> is small or queries are infrequent.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: This implementation provides optimal performance for range sum queries on an <em>immutable</em> array, achieving <code>O(1)</code> query time. Any solution that provides faster query time would inherently require more preprocessing or a different data structure, but <code>O(1)</code> is the theoretical minimum for a precomputed approach.</li>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this simple numerical calculation code. It does not interact with external systems, user input (beyond the array data itself), or sensitive resources.</li>
</ul>


### Code:
```python
class NumArray(object):

    def __init__(self, nums):
        """
        :type nums: List[int]
        """
        self.prefix_sum = [0]
        for num in nums:
            self.prefix_sum.append(self.prefix_sum[-1] + num)

    def sumRange(self, left, right):
        """
        :type left: int
        :type right: int
        :rtype: int
        """
        return self.prefix_sum[right + 1] - self.prefix_sum[left]
```
