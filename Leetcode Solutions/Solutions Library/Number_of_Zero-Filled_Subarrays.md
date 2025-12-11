## Number of Zero-Filled Subarrays
**Language:** python
**Tags:** python,array,counting,subarray,linear scan

### Description:
<p>This code solves a common array problem efficiently. Let's break it down.</p>
<h3>1. Overview &amp; Intent</h3>
<p>This code counts the total number of "zero-filled subarrays" within a given list of integers (<code>nums</code>). A zero-filled subarray is any contiguous sequence of zeros. For example, in <code>[0, 0, 1, 0]</code>, the zero-filled subarrays are <code>[0]</code>, <code>[0, 0]</code> (from the first two zeros), and <code>[0]</code> (from the last zero), totaling 4.</p>
<h3>2. How It Works</h3>
<p>The algorithm uses a single pass (iteration) through the input list:</p>
<ul>
<li>It maintains two variables:<ul>
<li><code>total_subarrays</code>: Stores the cumulative count of all zero-filled subarrays found so far.</li>
<li><code>current_zero_count</code>: Tracks the number of consecutive zeros encountered right before the current position.</li>
</ul>
</li>
<li><strong>Iterate <code>nums</code></strong>: For each number (<code>num</code>) in the input list:<ul>
<li><strong>If <code>num</code> is 0</strong>:<ul>
<li>Increment <code>current_zero_count</code> by 1 (we found another zero in the current sequence).</li>
<li>Add <code>current_zero_count</code> to <code>total_subarrays</code>. This is the crucial step: if we have <code>k</code> consecutive zeros, this <code>k</code>-th zero forms <code>k</code> new subarrays ending at this position (e.g., <code>[0]</code>, <code>[0,0]</code>, ..., up to <code>k</code> zeros). By adding <code>current_zero_count</code> (which is <code>k</code> in this example), we effectively sum these new subarrays.</li>
</ul>
</li>
<li><strong>If <code>num</code> is not 0</strong>:<ul>
<li>Reset <code>current_zero_count</code> to 0, breaking any previous sequence of zeros.</li>
</ul>
</li>
</ul>
</li>
<li>After iterating through all numbers, <code>total_subarrays</code> holds the final count, which is then returned.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm</strong>: A simple, greedy, single-pass iteration (linear scan). This approach is highly efficient for this problem.</li>
<li><strong>Data Structures</strong>: Minimalistic use of two integer variables (<code>total_subarrays</code>, <code>current_zero_count</code>). No complex data structures are needed.</li>
<li><strong>Trade-offs</strong>: The chosen approach prioritizes simplicity, directness, and optimal time/space complexity. It avoids any overhead associated with more complex approaches like using <code>groupby</code> or creating intermediate lists.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The code iterates through the <code>nums</code> list exactly once, performing constant-time operations for each element. <code>N</code> is the length of the <code>nums</code> list.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed amount of extra space for two integer variables, regardless of the input list's size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The logic correctly handles various scenarios:</p>
<ul>
<li><strong>Empty list <code>[]</code></strong>: The loop doesn't run, <code>total_subarrays</code> remains 0. Correct.</li>
<li><strong>List with no zeros <code>[1, 2, 3]</code></strong>: <code>current_zero_count</code> always remains 0, <code>total_subarrays</code> always remains 0. Correct.</li>
<li><strong>List with all zeros <code>[0, 0, 0]</code></strong>:<ul>
<li><code>num=0</code>: <code>current_zero_count=1</code>, <code>total_subarrays=1</code> (for <code>[0]</code>)</li>
<li><code>num=0</code>: <code>current_zero_count=2</code>, <code>total_subarrays=1+2=3</code> (for <code>[0,0]</code> and <code>[0]</code>)</li>
<li><code>num=0</code>: <code>current_zero_count=3</code>, <code>total_subarrays=3+3=6</code> (for <code>[0,0,0]</code>, <code>[0,0]</code>, <code>[0]</code>)</li>
<li>This correctly sums the triangular numbers for a block of <code>k</code> zeros (<code>k * (k + 1) / 2</code>). For <code>k=3</code>, <code>3 * 4 / 2 = 6</code>. Correct.</li>
</ul>
</li>
<li><strong>Mixed zeros and non-zeros <code>[0, 0, 1, 0]</code></strong>:<ul>
<li><code>num=0</code>: <code>curr=1</code>, <code>total=1</code></li>
<li><code>num=0</code>: <code>curr=2</code>, <code>total=1+2=3</code></li>
<li><code>num=1</code>: <code>curr=0</code> (resets)</li>
<li><code>num=0</code>: <code>curr=1</code>, <code>total=3+1=4</code></li>
<li>Correctly counts the subarrays from distinct zero blocks.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already very readable due to clear variable names and straightforward logic. No significant readability improvements are necessary.</li>
<li><strong>Performance</strong>: The current implementation is already optimal in terms of asymptotic time and space complexity. No further performance improvements are generally possible without changing the problem constraints.</li>
<li><strong>Alternative (less efficient but functionally equivalent)</strong>:<pre><code class="language-python">import itertools

class Solution:
    def zeroFilledSubarray(self, nums):
        total_subarrays = 0
        for k, group in itertools.groupby(nums, lambda x: x == 0):
            if k: # If the group consists of zeros
                length = len(list(group))
                total_subarrays += length * (length + 1) // 2
        return total_subarrays
</code></pre>
This alternative is conceptually similar by identifying blocks of zeros but introduces the overhead of <code>itertools.groupby</code> and list creation, making it likely slightly less performant than the direct iterative approach, despite having the same O(N) time complexity. The original code is generally preferred for its simplicity and directness.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no inherent security concerns with this code. It performs purely numerical computation on input arrays without external interactions or sensitive data handling.</li>
<li><strong>Performance</strong>: The current implementation is highly performant and efficient, running in linear time with constant extra space. It's an excellent solution to the problem. The use of basic arithmetic operations and a single loop minimizes overhead.</li>
</ul>


### Code:
```python
class Solution(object):
    def zeroFilledSubarray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        total_subarrays = 0
        current_zero_count = 0

        for num in nums:
            if num == 0:
                current_zero_count += 1
                total_subarrays += current_zero_count
            else:
                current_zero_count = 0
        
        return total_subarrays
```
