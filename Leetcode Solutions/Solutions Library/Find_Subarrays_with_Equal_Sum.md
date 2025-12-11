## Find Subarrays with Equal Sum
**Language:** python
**Tags:** python,set,array,iteration,hashing

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>findSubarrays</code> method aims to determine if there exist at least two distinct subarrays of length 2 within the input list <code>nums</code> that have the same sum.</p>
<ul>
<li><strong>Input</strong>: A list of integers, <code>nums</code>.</li>
<li><strong>Output</strong>: A boolean value (<code>True</code> if such subarrays exist, <code>False</code> otherwise).</li>
<li><strong>Goal</strong>: Efficiently detect duplicate sums of adjacent pairs.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm iterates through the <code>nums</code> list, focusing on consecutive pairs of elements, and uses a set to keep track of the sums encountered so far.</p>
<ol>
<li><strong>Initialization</strong>: An empty set named <code>seen_sums</code> is created. This set will store the sums of all length-2 subarrays processed.</li>
<li><strong>Iteration</strong>: The code iterates from the first element up to the second-to-last element of <code>nums</code> using <code>range(len(nums) - 1)</code>. This ensures that for each <code>i</code>, <code>nums[i+1]</code> is a valid index.</li>
<li><strong>Sum Calculation</strong>: In each iteration, the sum of the current element and the next element (<code>nums[i] + nums[i+1]</code>) is calculated, representing the sum of a length-2 subarray.</li>
<li><strong>Duplicate Check</strong>: The <code>current_sum</code> is checked against the <code>seen_sums</code> set.<ul>
<li>If <code>current_sum</code> is already in <code>seen_sums</code>, it means we have previously encountered a length-2 subarray with the exact same sum. In this case, the condition is met, and the method immediately returns <code>True</code>.</li>
<li>If <code>current_sum</code> is not in <code>seen_sums</code>, it means this sum is new.</li>
</ul>
</li>
<li><strong>Add to Set</strong>: The <code>current_sum</code> is then added to the <code>seen_sums</code> set for future comparisons.</li>
<li><strong>No Duplicates</strong>: If the loop completes without finding any duplicate sums, it means no two length-2 subarrays had the same sum, and the method returns <code>False</code>.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><p><strong>Data Structure: <code>set</code> for <code>seen_sums</code></strong>:</p>
<ul>
<li><strong>Why</strong>: Sets provide <code>O(1)</code> average-case time complexity for adding elements (<code>add</code>) and checking for membership (<code>in</code>). This is crucial for efficient duplicate detection.</li>
<li><strong>Trade-offs</strong>: A <code>list</code> would have <code>O(N)</code> lookup time, making the overall algorithm <code>O(N^2)</code>. A <code>dictionary</code> could also work (e.g., storing <code>sum: count</code>), but a set is simpler and sufficient since we only care about existence, not frequency.</li>
</ul>
</li>
<li><p><strong>Algorithm: Single Pass Iteration</strong>:</p>
<ul>
<li><strong>Why</strong>: By iterating through the array once and processing each adjacent pair, the algorithm visits each potential length-2 subarray exactly once, ensuring minimal processing.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of the input list <code>nums</code>.</p>
<ul>
<li><p><strong>Time Complexity</strong>: <code>O(N)</code></p>
<ul>
<li>The <code>for</code> loop runs <code>N-1</code> times.</li>
<li>Inside the loop, calculating <code>current_sum</code> is <code>O(1)</code>.</li>
<li>Set operations (<code>in</code> and <code>add</code>) are <code>O(1)</code> on average.</li>
<li>Therefore, the total time complexity is dominated by the single pass through the array, making it <code>O(N)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>: <code>O(N)</code></p>
<ul>
<li>In the worst case (e.g., all length-2 subarray sums are unique), the <code>seen_sums</code> set will store <code>N-1</code> distinct sums.</li>
<li>This makes the space complexity proportional to the input size, <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>nums</code> is empty (<code>[]</code>)</strong>: <code>len(nums) - 1</code> is -1. <code>range(-1)</code> is an empty range. The loop does not execute, and <code>False</code> is correctly returned (no length-2 subarrays possible).</li>
<li><strong><code>nums</code> has one element (<code>[5]</code>)</strong>: <code>len(nums) - 1</code> is 0. <code>range(0)</code> is an empty range. The loop does not execute, and <code>False</code> is correctly returned.</li>
<li><strong><code>nums</code> has two elements (<code>[1, 2]</code>)</strong>: <code>range(1)</code> executes once. <code>current_sum = 3</code> is added to <code>seen_sums</code>. Loop ends. <code>False</code> is correctly returned (only one length-2 subarray, so no duplicates).</li>
<li><strong><code>nums</code> has three elements, all unique sums (<code>[1, 2, 3]</code>)</strong>:<ul>
<li><code>i=0</code>: <code>1+2=3</code> added to <code>seen_sums</code>.</li>
<li><code>i=1</code>: <code>2+3=5</code> added to <code>seen_sums</code>.</li>
<li>Loop ends. <code>False</code> is correctly returned.</li>
</ul>
</li>
<li><strong><code>nums</code> has three elements, duplicate sums (<code>[1, 2, 1]</code>)</strong>:<ul>
<li><code>i=0</code>: <code>1+2=3</code> added to <code>seen_sums</code>.</li>
<li><code>i=1</code>: <code>2+1=3</code>. <code>3</code> is <em>in</em> <code>seen_sums</code>. <code>True</code> is correctly returned.</li>
</ul>
</li>
</ul>
<p>The logic handles these cases correctly because the loop bounds are carefully chosen, and the set efficiently tracks encountered sums.</p>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already highly readable, with clear variable names and comments. No significant improvements needed here.</li>
<li><strong>Performance</strong>:<ul>
<li>The <code>O(N)</code> time and <code>O(N)</code> space complexity is optimal for this problem, as you fundamentally need to examine all <code>N-1</code> length-2 subarrays and potentially store their sums. No algorithmic improvement will yield a better asymptotic complexity.</li>
<li>Micro-optimizations (e.g., using <code>itertools</code> for pairs) would likely not change the Big-O complexity and might even slightly decrease readability or introduce minor overhead.</li>
</ul>
</li>
<li><strong>Robustness</strong>:<ul>
<li><strong>Input Validation</strong>: For production code where inputs might be unpredictable, one could add checks for <code>nums</code> being <code>None</code> or not a <code>List[int]</code>. However, for typical competitive programming/LeetCode contexts, input types are usually guaranteed.</li>
<li><strong>Integer Overflow</strong>: Python's arbitrary-precision integers mean <code>nums[i] + nums[i+1]</code> will not overflow, regardless of how large the numbers are. In languages with fixed-size integers (e.g., C++, Java), this could be a concern, requiring <code>long</code> or <code>BigInteger</code>.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Hash Collisions</strong>: The performance of <code>set</code> operations (average <code>O(1)</code>) relies on a good hash function and distribution of hash values. In extremely rare or adversarial scenarios where input numbers could be crafted to cause many hash collisions, set operations might degrade to <code>O(N)</code> in the worst case, theoretically making the overall algorithm <code>O(N^2)</code>. However, for typical integer inputs and Python's built-in hashing, this is not a practical concern.</li>
</ul>


### Code:
```python
from typing import List

class Solution:
    def findSubarrays(self, nums: List[int]) -> bool:
      
        # A set to store the sums of all subarrays of length 2 encountered so far.
        seen_sums = set()
        
        # Iterate up to the second to last element to form pairs (i, i+1)
        for i in range(len(nums) - 1):
            current_sum = nums[i] + nums[i + 1]
            
            # If we have seen this sum before, we found two subarrays with equal sum.
            if current_sum in seen_sums:
                return True
            
            seen_sums.add(current_sum)
            
        # If the loop completes without returning True, no such subarrays exist.
        return False
```
