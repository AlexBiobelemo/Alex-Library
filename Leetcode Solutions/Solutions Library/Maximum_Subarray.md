## Maximum Subarray
**Language:** python
**Tags:** python,kadane's algorithm,dynamic programming,arrays

### Description:
<p>This code implements Kadane's Algorithm to solve the Maximum Subarray Problem. It efficiently finds the contiguous subarray within a given array of numbers that has the largest sum.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem</strong>: Given an integer array <code>nums</code>, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.</li>
<li><strong>Goal</strong>: Calculate the maximum possible sum of any contiguous subarray within <code>nums</code>.</li>
<li><strong>Algorithm</strong>: Kadane's Algorithm, a dynamic programming approach.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm iterates through the array, keeping track of two key values:</p>
<ul>
<li><code>current_max</code>: The maximum sum of a subarray <strong>ending at the current position</strong>.</li>
<li><code>max_so_far</code>: The overall maximum sum found <strong>anywhere in the array</strong> up to the current position.</li>
</ul>
<p><strong>Steps:</strong></p>
<ol>
<li><strong>Initialization</strong>:<ul>
<li>Both <code>max_so_far</code> and <code>current_max</code> are initialized with the first element of the <code>nums</code> array (<code>nums[0]</code>). This correctly handles arrays with a single element or arrays where all numbers are negative.</li>
</ul>
</li>
<li><strong>Iteration</strong>:<ul>
<li>The code iterates through the array starting from the second element (index <code>1</code>).</li>
<li>For each element <code>nums[i]</code>:<ul>
<li><code>current_max</code> is updated: It decides whether to extend the previous subarray (<code>current_max + nums[i]</code>) or start a new subarray from the current element (<code>nums[i]</code>). It chooses the option that yields a larger sum. This means if <code>current_max</code> became negative, it's better to "discard" the previous subarray and start fresh with <code>nums[i]</code>.
<code>current_max = max(nums[i], current_max + nums[i])</code></li>
<li><code>max_so_far</code> is updated: It compares the newly calculated <code>current_max</code> with the globally tracked <code>max_so_far</code> and keeps the larger of the two.
<code>max_so_far = max(max_so_far, current_max)</code></li>
</ul>
</li>
</ul>
</li>
<li><strong>Result</strong>:<ul>
<li>After iterating through all elements, <code>max_so_far</code> will hold the maximum sum of any contiguous subarray.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Kadane's Algorithm (Dynamic Programming)</strong>:<ul>
<li>The core idea is that the maximum subarray sum ending at a given position <code>i</code> depends only on the maximum subarray sum ending at position <code>i-1</code>.</li>
<li>This property allows for a single pass through the array, making it highly efficient.</li>
</ul>
</li>
<li><strong>Greedy Choice</strong>: At each step, the algorithm makes a locally optimal decision (either extend the current subarray or start a new one). This local optimality leads to a globally optimal solution.</li>
<li><strong>State Variables</strong>:<ul>
<li><code>current_max</code>: Essential for tracking the best sum <em>ending</em> at the current point. If this sum ever drops below zero, it implies that extending this subarray further would only reduce its sum, making it a poor choice.</li>
<li><code>max_so_far</code>: Crucial for storing the overall maximum, as <code>current_max</code> is reset/recalculated based on local conditions, but <code>max_so_far</code> preserves the best sum found anywhere in the array.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(N)<ul>
<li>The algorithm makes a single pass through the <code>nums</code> array, where <code>N</code> is the number of elements.</li>
<li>Each operation inside the loop (comparisons, additions) is constant time.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(1)<ul>
<li>The algorithm uses a constant amount of extra space for the <code>max_so_far</code> and <code>current_max</code> variables, regardless of the input array's size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm correctly handles various edge cases:</p>
<ul>
<li><strong>Single Element Array</strong>: If <code>nums = [5]</code>, <code>max_so_far</code> and <code>current_max</code> are initialized to 5. The loop doesn't run, and 5 is returned. Correct.</li>
<li><strong>All Positive Numbers</strong>: <code>nums = [1, 2, 3]</code>. <code>current_max</code> will continually grow, and <code>max_so_far</code> will follow, resulting in the sum of all elements. Correct.</li>
<li><strong>All Negative Numbers</strong>: <code>nums = [-2, -1, -3]</code>.<ul>
<li>Initial: <code>max_so_far = -2</code>, <code>current_max = -2</code>.</li>
<li>For <code>-1</code>: <code>current_max = max(-1, -2 + -1) = max(-1, -3) = -1</code>. <code>max_so_far = max(-2, -1) = -1</code>.</li>
<li>For <code>-3</code>: <code>current_max = max(-3, -1 + -3) = max(-3, -4) = -3</code>. <code>max_so_far = max(-1, -3) = -1</code>.</li>
<li>Returns -1. This is correct, as the subarray <code>[-1]</code> has the largest (least negative) sum. The initialization to <code>nums[0]</code> is key here.</li>
</ul>
</li>
<li><strong>Mixed Positive and Negative Numbers</strong>: <code>nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]</code>. The algorithm correctly finds <code>[4, -1, 2, 1]</code> with sum 6. The logic of resetting <code>current_max</code> to <code>nums[i]</code> when extending the previous subarray yields a smaller sum is crucial here.</li>
<li><strong>Empty Array</strong>: The current implementation would raise an <code>IndexError</code> if <code>nums</code> is empty because of <code>nums[0]</code>. Typically, problem constraints guarantee a non-empty array or specify a return value for an empty input (e.g., 0 or negative infinity). Assuming <code>nums</code> is always non-empty as per typical LeetCode problems.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The variable names <code>max_so_far</code> and <code>current_max</code> are idiomatic for Kadane's algorithm and very clear. No significant readability improvements are needed.</li>
<li><strong>Performance</strong>: Kadane's algorithm is optimal for this problem in terms of time complexity (O(N)) and space complexity (O(1)). There are no further performance improvements possible for the general case.</li>
<li><strong>Alternatives (less efficient)</strong>:<ul>
<li><strong>Brute Force (O(N^2) or O(N^3))</strong>: Iterate through all possible start and end indices of subarrays, calculate their sums, and find the maximum. This is much less efficient.</li>
<li><strong>Divide and Conquer (O(N log N))</strong>: Recursively split the array in half, solve for each half, and also find the maximum crossing subarray (one that spans both halves). This is more complex and still slower than Kadane's.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Integer Overflow</strong>: In languages with fixed-size integers (e.g., Java <code>int</code>, C++ <code>int</code>), very large sums could lead to integer overflow if <code>nums</code> contains many large positive numbers. Python's integers automatically handle arbitrary precision, so this is not a concern here.</li>
<li><strong>Input Scale</strong>: The O(N) time complexity ensures that the algorithm scales very well with large input arrays (<code>N</code> up to 10^5 or more) without significant performance degradation.</li>
</ul>


### Code:
```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        max_so_far = nums[0]
        current_max = nums[0]

        for i in range(1, len(nums)):
            current_max = max(nums[i], current_max + nums[i])
            max_so_far = max(max_so_far, current_max)

        return max_so_far
```
