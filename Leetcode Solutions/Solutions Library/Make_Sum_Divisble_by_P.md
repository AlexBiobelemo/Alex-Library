## Make Sum Divisble by P
**Language:** python
**Tags:** prefix sums,modulo arithmetic,hash map,subarray

### Description:
<p>This code solves the problem of finding the minimum length of a contiguous subarray that needs to be removed from <code>nums</code> such that the sum of the remaining elements is divisible by <code>p</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of the <code>minSubarray</code> function is to identify the smallest possible contiguous subarray whose removal makes the total sum of the <em>remaining</em> elements perfectly divisible by <code>p</code>.</p>
<p>This can be rephrased:</p>
<ul>
<li>Let <code>total_sum</code> be the sum of all elements in <code>nums</code>.</li>
<li>Let <code>subarray_sum</code> be the sum of the elements in the subarray to be removed.</li>
<li>We want <code>(total_sum - subarray_sum) % p == 0</code>.</li>
<li>This is mathematically equivalent to <code>total_sum % p == subarray_sum % p</code>.</li>
<li>Therefore, the problem is to find the shortest contiguous subarray whose sum has the same remainder modulo <code>p</code> as the total sum of <code>nums</code> modulo <code>p</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution uses a clever approach involving prefix sums and modulo arithmetic, leveraging a hash map to track remainders.</p>
<ol>
<li><strong>Calculate Total Remainder:</strong> It first computes <code>total_sum_remainder = sum(nums) % p</code>.<ul>
<li>If <code>total_sum_remainder</code> is 0, it means the original array sum is already divisible by <code>p</code>, so no subarray needs to be removed. It returns <code>0</code>.</li>
</ul>
</li>
<li><strong>Initialize Data Structures:</strong><ul>
<li><code>remainder_to_index = {0: -1}</code>: A dictionary (hash map) to store the <em>latest</em> index <code>j</code> where a particular <code>current_prefix_sum_remainder</code> was encountered. The <code>{0: -1}</code> entry is a crucial sentinel to handle subarrays starting from index 0. <code>prefix_sum[-1]</code> is conventionally 0.</li>
<li><code>min_len = n</code>: Initializes the minimum length found so far to the maximum possible length (<code>n</code>, the length of <code>nums</code>).</li>
<li><code>current_prefix_sum_remainder = 0</code>: Tracks the prefix sum's remainder modulo <code>p</code> up to the current index <code>j</code>.</li>
</ul>
</li>
<li><strong>Iterate and Search:</strong> It iterates through the <code>nums</code> array from <code>j = 0</code> to <code>n-1</code>:<ul>
<li>Updates <code>current_prefix_sum_remainder</code> by adding <code>nums[j]</code> and taking the result modulo <code>p</code>.</li>
<li>Calculates <code>needed_rem</code>: This is the remainder that a <em>prefix sum up to index <code>i</code></em> must have for the subarray <code>nums[i+1 ... j]</code> to have a sum whose remainder matches <code>total_sum_remainder</code>. The formula <code>(current_prefix_sum_remainder - total_sum_remainder + p) % p</code> correctly handles potential negative results from subtraction before taking the modulo.</li>
<li><strong>Lookup:</strong> If <code>needed_rem</code> is found in <code>remainder_to_index</code>, it means a previous prefix sum (ending at index <code>i = remainder_to_index[needed_rem]</code>) had the required remainder. The length of the subarray <code>nums[i+1 ... j]</code> is <code>j - i</code>. <code>min_len</code> is updated with the minimum of its current value and <code>j - i</code>.</li>
<li><strong>Store Current Remainder:</strong> The <code>current_prefix_sum_remainder</code> and its current index <code>j</code> are stored in <code>remainder_to_index</code>. This ensures that for any given remainder, the dictionary always holds the <em>latest</em> index where it was seen, which is vital for finding the minimum length <code>j-i</code> (by maximizing <code>i</code> for a given <code>j</code>).</li>
</ul>
</li>
<li><strong>Final Result:</strong> After the loop, if <code>min_len</code> is still <code>n</code> (its initial value), it means no valid subarray (smaller than the entire array) was found to satisfy the condition, so it returns <code>-1</code>. Otherwise, it returns the calculated <code>min_len</code>.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Prefix Sums with Modulo Arithmetic</strong>: The problem's core requirement of "sum divisible by <code>p</code>" immediately suggests using prefix sums. Applying the modulo operator at each step (<code>% p</code>) prevents integer overflow for large sums and keeps the intermediate values manageable, which is crucial for dictionary keys.</li>
<li><strong>Hash Map (<code>remainder_to_index</code>)</strong>: This is the most critical data structure.<ul>
<li>It allows for <strong>O(1) average time lookups</strong> to find if a <code>needed_rem</code> has been seen before.</li>
<li>It stores the <em>index</em> associated with each remainder, enabling the calculation of subarray lengths (<code>j - i</code>).</li>
</ul>
</li>
<li><strong><code>{0: -1}</code> Initialization</strong>: This sentinel value handles the edge case where the subarray to be removed starts at the very beginning of <code>nums</code> (i.e., <code>nums[0...j]</code>). If <code>needed_rem</code> is <code>0</code>, finding <code>0</code> mapped to <code>-1</code> means the prefix sum up to <code>j</code> itself matches <code>total_sum_remainder</code>, and the subarray <code>nums[0...j]</code> is a candidate for removal. The length is <code>j - (-1) = j + 1</code>.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Calculating <code>sum(nums)</code> takes O(N) time.</li>
<li>The main loop iterates <code>n</code> times. Inside the loop, all operations (arithmetic, dictionary lookup, dictionary insertion) take O(1) time on average.</li>
<li>Therefore, the total time complexity is dominated by the initial sum and the loop, resulting in O(N).</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li>The <code>remainder_to_index</code> dictionary stores at most <code>N+1</code> distinct prefix sum remainders (in the worst case where all remainders are unique).</li>
<li>Thus, the space complexity is O(N).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Array already divisible by <code>p</code></strong>: Handled by the initial <code>if total_sum_remainder == 0: return 0</code>. This is correct.</li>
<li><strong>No such subarray exists</strong>: If no subarray (other than potentially the entire array itself) can satisfy the condition, <code>min_len</code> will remain <code>n</code>. The code correctly returns <code>-1</code> in this scenario, as per common problem interpretations where removing the entire array is only considered if no smaller subarray works, and even then often leads to -1.</li>
<li><strong>Subarray starts at index 0</strong>: The <code>remainder_to_index = {0: -1}</code> initialization correctly allows the first element <code>nums[0]</code> (or any prefix <code>nums[0...j]</code>) to be part of the candidate subarray for removal. When <code>current_prefix_sum_remainder == total_sum_remainder</code>, <code>needed_rem</code> will be <code>0</code>, and the lookup finds <code>i = -1</code>, correctly giving a length of <code>j - (-1) = j + 1</code>.</li>
<li><strong><code>p = 1</code></strong>: If <code>p</code> is 1, any integer is divisible by <code>p</code>. <code>total_sum_remainder</code> will always be 0, so the function correctly returns <code>0</code> immediately.</li>
<li><strong>Negative numbers in <code>nums</code></strong>: Python's modulo operator (<code>%</code>) handles negative numbers correctly (e.g., <code>-5 % 3</code> is <code>1</code> in Python, matching mathematical definition <code>a = q*n + r</code> where <code>0 &lt;= r &lt; n</code>). The expression <code>(current_prefix_sum_remainder - total_sum_remainder + p) % p</code> further ensures the <code>needed_rem</code> is always non-negative and within <code>[0, p-1]</code>, maintaining correctness regardless of element signs.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite readable. Variable names are descriptive. The logic is concise and follows standard patterns for this type of problem.</li>
<li><strong>Performance</strong>: The O(N) time and O(N) space complexity is optimal for this problem, as we must examine each element and potentially store all prefix sum remainders. There are no obvious algorithmic improvements for better time or space complexity.</li>
<li><strong>No alternatives</strong> are generally more efficient for this problem. Other approaches might involve brute-force checking all <code>O(N^2)</code> subarrays, which would be much slower.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this code. It performs numerical computations on input arrays without external interactions or risky operations.</li>
<li><strong>Performance</strong>: The solution is efficient and performs well for typical constraints. For extremely large <code>N</code> (e.g., millions), the O(N) space for the dictionary might become a concern if memory is highly restricted, but for competitive programming contexts or standard use cases, it's perfectly acceptable. Python's arbitrary precision integers ensure that <code>sum(nums)</code> won't overflow even for very large numbers, before the modulo operation is applied.</li>
</ul>


### Code:
```python
class Solution(object):
    def minSubarray(self, nums, p):
        """
        :type nums: List[int]
        :type p: int
        :rtype: int
        """
        n = len(nums)
        total_sum_remainder = sum(nums) % p
        if total_sum_remainder == 0:
            return 0
        
        remainder_to_index = {0: -1}
        current_prefix_sum_remainder = 0
        min_len = n

        for j in range(n):
            current_prefix_sum_remainder = (current_prefix_sum_remainder + nums[j]) % p
            needed_rem = (current_prefix_sum_remainder - total_sum_remainder + p) % p

            if needed_rem in remainder_to_index:
                i = remainder_to_index[needed_rem]
                min_len = min(min_len, j - i)
            
            remainder_to_index[current_prefix_sum_remainder] = j
        
        if min_len == n:
            return -1
        else:
            return min_len
```
