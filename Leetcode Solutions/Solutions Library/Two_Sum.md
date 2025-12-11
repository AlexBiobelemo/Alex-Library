## Two Sum
**Language:** python
**Tags:** two sum,hash map,algorithm,array,python

### Description:
---

### AI Explanation
<p>This Python code implements a highly efficient solution to the classic "Two Sum" problem. It's a common interview question that tests understanding of data structures and time-space trade-offs.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given an array of integers <code>nums</code> and an integer <code>target</code>, return indices of the two numbers such that they add up to <code>target</code>.</li>
<li><strong>Assumptions (typical for this problem):</strong><ul>
<li>Exactly one solution exists.</li>
<li>You may not use the same element twice.</li>
</ul>
</li>
<li><strong>Goal:</strong> Find and return the 0-based indices of the two numbers that sum to <code>target</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a single pass through the array, leveraging a hash map (Python dictionary) to store previously encountered numbers and their indices.</p>
<ol>
<li><strong>Initialize <code>num_map</code>:</strong> An empty dictionary <code>num_map</code> is created. This will store <code>(number: index)</code> pairs for numbers encountered so far.</li>
<li><strong>Iterate through <code>nums</code>:</strong> The code loops through each number (<code>num</code>) and its index (<code>i</code>) in the <code>nums</code> list.</li>
<li><strong>Calculate <code>complement</code>:</strong> For each <code>num</code>, it calculates the <code>complement</code> needed to reach the <code>target</code> (i.e., <code>complement = target - num</code>).</li>
<li><strong>Check <code>num_map</code>:</strong><ul>
<li>If the <code>complement</code> is found as a key in <code>num_map</code>, it means we've previously seen a number that, when added to the current <code>num</code>, equals the <code>target</code>.</li>
<li>In this case, the function immediately returns a list containing the index of the <code>complement</code> (retrieved from <code>num_map</code>) and the current index <code>i</code>.</li>
</ul>
</li>
<li><strong>Add to <code>num_map</code>:</strong> If the <code>complement</code> is <em>not</em> found, the current <code>num</code> and its index <code>i</code> are added to <code>num_map</code>. This makes <code>num</code> available for future <code>complement</code> checks.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure:</strong><ul>
<li><strong>Hash Map (Python <code>dict</code>):</strong> The core design decision is to use a hash map. This allows for near O(1) average-case time complexity for checking if a <code>complement</code> exists and for storing/retrieving numbers and their indices.</li>
</ul>
</li>
<li><strong>Algorithm:</strong><ul>
<li><strong>Single Pass Iteration:</strong> The algorithm processes the list in a single pass. This avoids nested loops, which would lead to a higher time complexity.</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Time vs. Space:</strong> This solution sacrifices O(N) auxiliary space (for the hash map) to achieve an optimal O(N) time complexity. A brute-force approach (nested loops) would use O(1) space but take O(N^2) time. For typical input sizes, the time improvement is highly desirable.</li>
<li><strong>Order of Operations:</strong> Adding <code>num</code> to <code>num_map</code> <em>after</em> checking for its <code>complement</code> ensures that a number is not considered its own complement. If <code>target = 2 * num</code>, and <code>num</code> was added <em>before</em> the check, it could incorrectly return <code>[i, i]</code>. By adding it after the check, we only consider numbers that appeared <em>earlier</em> in the array.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong> O(N)<ul>
<li>The code iterates through the <code>nums</code> list once (N elements).</li>
<li>Inside the loop, hash map operations (<code>in</code> check, <code>getitem</code>, <code>setitem</code>) take, on average, O(1) time.</li>
<li>Therefore, the total time complexity is dominated by the single pass, resulting in O(N).</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> O(N)<ul>
<li>In the worst case (e.g., no pair sums to target until the very end, or all numbers are unique), the <code>num_map</code> could store up to N key-value pairs.</li>
<li>Thus, the space complexity is proportional to the number of elements in the input list.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Duplicate Numbers in Input:</strong><ul>
<li><code>nums = [3, 3], target = 6</code>:<ul>
<li><code>i=0, num=3</code>: <code>complement = 3</code>. Not in <code>num_map</code>. <code>num_map = {3:0}</code>.</li>
<li><code>i=1, num=3</code>: <code>complement = 3</code>. Found in <code>num_map</code> (index <code>0</code>). Returns <code>[0, 1]</code>. Correct.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Negative Numbers:</strong> The algorithm works correctly with negative numbers as <code>dict</code> keys handle them fine.</li>
<li><strong>Zero in Target/Numbers:</strong> Handled correctly. E.g., <code>nums = [0, 4, 3, 0], target = 0</code>.<ul>
<li><code>i=0, num=0</code>: <code>complement = 0</code>. Not in <code>num_map</code>. <code>num_map = {0:0}</code>.</li>
<li><code>i=1, num=4</code>: <code>complement = -4</code>. Not in <code>num_map</code>. <code>num_map = {0:0, 4:1}</code>.</li>
<li><code>i=2, num=3</code>: <code>complement = -3</code>. Not in <code>num_map</code>. <code>num_map = {0:0, 4:1, 3:2}</code>.</li>
<li><code>i=3, num=0</code>: <code>complement = 0</code>. Found in <code>num_map</code> (index <code>0</code>). Returns <code>[0, 3]</code>. Correct.</li>
</ul>
</li>
<li><strong>Empty or Single-Element List:</strong> The problem constraints for LeetCode's <code>twoSum</code> usually specify <code>nums.length &gt;= 2</code>, so these cases are typically not tested. If they were possible, an empty list would result in <code>None</code> being returned (as the loop wouldn't run and no explicit return exists).</li>
<li><strong>No Solution Exists:</strong> This specific implementation assumes a solution <em>always</em> exists, which is a common problem constraint for <code>twoSum</code>. If no solution were found, the loop would complete, and the function would implicitly return <code>None</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Robustness (Handling No Solution):</strong><ul>
<li>If the problem statement allowed for no solution, the function should explicitly handle this, for example, by raising an error or returning an empty list at the end:<pre><code class="language-python">class Solution(object):
    def twoSum(self, nums, target):
        num_map = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_map:
                return [num_map[complement], i]
            num_map[num] = i
        # If the loop completes, no solution was found
        # Depending on requirements, could raise an error or return an empty list
        # raise ValueError("No two sum solution found")
        return []
</code></pre>
</li>
</ul>
</li>
<li><strong>Alternative Approaches:</strong><ul>
<li><strong>Brute Force:</strong> Two nested loops. O(N^2) time complexity, O(1) space complexity. Much less efficient for larger inputs.</li>
<li><strong>Sort and Two-Pointers:</strong> If preserving original indices wasn't a requirement, or if we stored <code>(value, original_index)</code> pairs and then sorted, we could use a two-pointer approach. This would be O(N log N) for sorting and O(N) space (to store pairs if original indices are needed). While viable, the hash map approach is generally simpler and faster (O(N) vs O(N log N)).</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong><ul>
<li>The choice of a hash map (Python's <code>dict</code>) is critical for achieving the optimal O(N) average-case time complexity. Python's dictionary implementation is highly optimized.</li>
<li>In rare, adversarial cases or with extremely poor hash functions (not typical for Python's built-in types like integers), hash map operations could degrade to O(N) in the worst case, leading to an overall O(N^2) complexity. However, for practical purposes with typical integer inputs, the average O(1) assumption holds.</li>
</ul>
</li>
<li><strong>Security:</strong><ul>
<li>For this specific algorithm, there are no inherent security vulnerabilities. It's a purely computational task.</li>
<li>If <code>nums</code> or <code>target</code> were user-provided inputs in a larger application, general input validation (e.g., type checking, range checks) would be good practice to prevent unexpected behavior or errors in the overall system, but this is outside the scope of this isolated <code>twoSum</code> function.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        num_map = {} # Value -> Index
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_map:
                return [num_map[complement], i]
            num_map[num] = i
```
