## Next Permutation
**Language:** python
**Tags:** next permutation,array manipulation,in-place,permutations

### Description:
<p>This code implements a classic algorithm to find the lexicographically next greater permutation of a given list of integers.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose:</strong> The <code>nextPermutation</code> function rearranges the numbers in the input list <code>nums</code> to form the lexicographically next greater permutation.</li>
<li><strong>Examples:</strong><ul>
<li><code>[1,2,3]</code> -&gt; <code>[1,3,2]</code></li>
<li><code>[3,2,1]</code> -&gt; <code>[1,2,3]</code> (the smallest permutation, as <code>[3,2,1]</code> is the largest)</li>
<li><code>[1,1,5]</code> -&gt; <code>[1,5,1]</code></li>
</ul>
</li>
<li><strong>In-place Modification:</strong> The function modifies the input list <code>nums</code> directly and does not return a new list. This is specified by the return type <code>None</code> and the docstring.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm follows a four-step process to find the next permutation:</p>
<ol>
<li><p><strong>Find the Pivot:</strong></p>
<ul>
<li>Starting from the right, it scans for the first element <code>nums[i]</code> that is <em>smaller</em> than its immediate right neighbor <code>nums[i+1]</code>. This <code>i</code> is our "pivot" – the element that needs to be increased.</li>
<li>Example: For <code>[1,5,8,4,7,6,5,3,1]</code>, <code>i</code> would be at index 4 (value <code>4</code>), because <code>4 &lt; 7</code>.</li>
<li>If no such element <code>i</code> is found (i.e., the array is sorted in descending order, like <code>[3,2,1]</code>), it means the current permutation is the largest possible. In this case, the entire array is reversed to produce the smallest possible permutation (ascending order).</li>
</ul>
</li>
<li><p><strong>Find the Swap Element:</strong></p>
<ul>
<li>If a pivot <code>i</code> was found, it then scans from the right end of the array to find the smallest element <code>nums[j]</code> that is <em>greater</em> than <code>nums[i]</code>.</li>
<li>Example: Continuing with <code>[1,5,8,4,7,6,5,3,1]</code> and <code>i=4</code> (value <code>4</code>), <code>j</code> would be at index 6 (value <code>5</code>), because <code>5</code> is the smallest value to the right of <code>i</code> that is greater than <code>4</code>.</li>
</ul>
</li>
<li><p><strong>Swap:</strong></p>
<ul>
<li>The pivot element <code>nums[i]</code> and the found swap element <code>nums[j]</code> are swapped.</li>
<li>Example: <code>[1,5,8,4,7,6,5,3,1]</code> with <code>i=4</code> and <code>j=6</code> becomes <code>[1,5,8,5,7,6,4,3,1]</code>.</li>
</ul>
</li>
<li><p><strong>Reverse the Suffix:</strong></p>
<ul>
<li>The subarray to the right of the pivot (<code>nums[i+1]</code> to the end) is reversed.</li>
<li>This step is crucial because, after swapping <code>nums[i]</code>, we want the smallest possible increase. Reversing the suffix ensures that the remaining elements are in ascending order, which produces the lexicographically smallest arrangement for that suffix.</li>
<li>Example: <code>[1,5,8,5,7,6,4,3,1]</code> with <code>i=4</code>. The suffix is <code>[7,6,4,3,1]</code>. Reversing it yields <code>[1,3,4,6,7]</code>.</li>
<li>The final result: <code>[1,5,8,5,1,3,4,6,7]</code>.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm Choice:</strong> The chosen algorithm is a well-established and efficient method for finding the next lexicographical permutation. It directly manipulates the array based on mathematical properties of permutations.</li>
<li><strong>In-Place Modification:</strong> The requirement to modify <code>nums</code> in-place dictates the use of direct array indexing and swapping operations rather than creating new lists. This is a common constraint in competitive programming and for memory efficiency.</li>
<li><strong>Array (<code>List[int]</code>) as Data Structure:</strong> A mutable list is ideal for this problem because it allows efficient random access, in-place swaps, and partial reversals.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: O(N)</strong></p>
<ul>
<li>Step 1 (finding <code>i</code>): In the worst case, it iterates almost through the entire array from right to left (e.g., <code>[1,2,3,4]</code>), taking O(N) time.</li>
<li>Step 2 (finding <code>j</code>): In the worst case, it iterates almost through the entire array from right to left, taking O(N) time.</li>
<li>Step 3 (swapping): O(1) time.</li>
<li>Step 4 (reversing suffix): In the worst case, it reverses nearly half of the array, taking O(N) time.</li>
<li>Total time complexity is dominated by these linear scans, resulting in O(N).</li>
</ul>
</li>
<li><p><strong>Space Complexity: O(1)</strong></p>
<ul>
<li>The algorithm performs all operations in-place, using only a few constant additional variables (<code>n</code>, <code>i</code>, <code>j</code>, <code>left</code>, <code>right</code>). No auxiliary data structures proportional to the input size are created.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Already Largest Permutation (Descending Order):</strong><ul>
<li>Input: <code>[3,2,1]</code></li>
<li><code>i</code> loop completes with <code>i = -1</code>.</li>
<li>The <code>if i == -1:</code> condition triggers, <code>nums.reverse()</code> is called.</li>
<li>Output: <code>[1,2,3]</code> (the smallest permutation). This is correct.</li>
</ul>
</li>
<li><strong>Smallest Permutation (Ascending Order):</strong><ul>
<li>Input: <code>[1,2,3]</code></li>
<li><code>i</code> found at index 1 (<code>nums[1]=2 &lt; nums[2]=3</code>).</li>
<li><code>j</code> found at index 2 (<code>nums[2]=3 &gt; nums[1]=2</code>).</li>
<li>Swap <code>nums[1]</code> and <code>nums[2]</code>: <code>[1,3,2]</code>.</li>
<li>Reverse suffix from <code>i+1=2</code>: <code>[2]</code> is already reversed.</li>
<li>Output: <code>[1,3,2]</code>. This is correct.</li>
</ul>
</li>
<li><strong>List with Duplicates:</strong><ul>
<li>Input: <code>[1,1,5]</code></li>
<li><code>i</code> found at index 0 (<code>nums[0]=1 &lt; nums[1]=1</code> is false, <code>nums[0]=1 &lt; nums[1]=5</code> is true, loop continues until <code>i</code> is at index 0 because <code>nums[0] &lt; nums[1]</code> is <code>1 &lt; 1</code> (false), and <code>i</code> decreases from <code>n-2</code> to <code>0</code>). Wait, re-evaluating:<ul>
<li><code>n=3</code>. <code>i=1</code>. <code>nums[1]=1</code>, <code>nums[2]=5</code>. <code>1 &lt; 5</code> is true. Loop condition <code>nums[i] &gt;= nums[i+1]</code> is false. So <code>i</code> stops at <code>1</code>.</li>
<li><code>i=1</code>. <code>j=2</code>. <code>nums[2]=5 &gt; nums[1]=1</code>. <code>j</code> stops at <code>2</code>.</li>
<li>Swap <code>nums[1]</code> and <code>nums[2]</code>: <code>[1,5,1]</code>.</li>
<li>Reverse suffix <code>nums[i+1:]</code> (i.e., <code>nums[2:]</code>): <code>[1]</code>. Reversing <code>[1]</code> is <code>[1]</code>.</li>
<li>Output: <code>[1,5,1]</code>. This is correct.</li>
</ul>
</li>
<li>The strict inequality for <code>j</code> (<code>nums[j] &gt; nums[i]</code>) ensures that when duplicates are present, the algorithm picks the <em>rightmost</em> smallest element greater than <code>nums[i]</code>, which maintains lexicographical order correctly. The <code>&gt;=</code> for <code>i</code> ensures it stops at the first actual <em>decrease</em> when looking from the right.</li>
</ul>
</li>
<li><strong>Single Element List:</strong><ul>
<li>Input: <code>[1]</code></li>
<li><code>n=1</code>. <code>i</code> starts at <code>n-2 = -1</code>.</li>
<li><code>while</code> loop for <code>i</code> doesn't run. <code>i</code> remains <code>-1</code>.</li>
<li><code>if i == -1:</code> is true. <code>nums.reverse()</code> is called on <code>[1]</code>, which does nothing.</li>
<li>Output: <code>[1]</code>. This is correct as a single element has no other permutations.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is quite readable. The comments clearly outline the steps of the algorithm, which is very helpful. Variable names like <code>i</code> and <code>j</code> are standard for this algorithm.</li>
<li><strong>Pythonic Reversal:</strong> The suffix reversal <code>while left &lt; right:</code> loop could be replaced with <code>nums[i+1:] = nums[i+1:][::-1]</code>. While more concise, the explicit loop avoids creating a temporary slice and then assigning it back, which might be slightly more memory-efficient or performant in some Python implementations for very large lists, though the difference is often negligible. The current implementation is perfectly fine.</li>
<li><strong>Performance:</strong> The algorithm is already optimal in terms of time (O(N)) and space (O(1)). There are no significant performance improvements possible for the core logic of finding the next permutation.</li>
<li><strong>Alternative for <code>nums.reverse()</code>:</strong> <code>nums[:] = nums[::-1]</code> could be used, but <code>nums.reverse()</code> is the idiomatic and efficient in-place method for reversing the entire list.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code does not interact with external systems, user input in a way that could lead to injection, or sensitive data. There are no inherent security concerns.</li>
<li><strong>Performance:</strong> As noted, the algorithm is highly optimized for performance, achieving linear time complexity with constant extra space. It's suitable for large inputs where efficiency is critical.</li>
</ul>


### Code:
```python
class Solution(object):
    def nextPermutation(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        n = len(nums)
        
        # Step 1: Find the first decreasing element from the right
        # Find index i such that nums[i] < nums[i+1]
        i = n - 2
        while i >= 0 and nums[i] >= nums[i+1]:
            i -= 1
            
        # If no such i is found, the array is in descending order (e.g., [3,2,1])
        # In this case, reverse the entire array to get the lowest possible order (ascending)
        if i == -1:
            nums.reverse()
            return
            
        # Step 2: Find the smallest element to the right of i that is greater than nums[i]
        # Find index j such that nums[j] > nums[i]
        j = n - 1
        while j > i and nums[j] <= nums[i]:
            j -= 1
            
        # Step 3: Swap nums[i] and nums[j]
        nums[i], nums[j] = nums[j], nums[i]
        
        # Step 4: Reverse the subarray from i+1 to the end
        # This ensures the suffix is in the smallest possible order (ascending)
        left = i + 1
        right = n - 1
        while left < right:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
            right -= 1
```
