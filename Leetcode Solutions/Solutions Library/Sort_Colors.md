## Sort Colors
**Language:** python
**Tags:** python,sorting,dutch national flag,in-place algorithm

### Description:
<p>This code implements a classic sorting algorithm often referred to as the <strong>Dutch National Flag problem</strong>. It's designed to sort an array containing only three distinct values (typically 0, 1, and 2) in-place and in linear time.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: The <code>sortColors</code> method sorts an array <code>nums</code> containing integers 0, 1, and 2. The goal is to arrange the elements such that all 0s come first, followed by all 1s, and then all 2s.</li>
<li><strong>In-Place</strong>: The sorting must be performed "in-place," meaning it modifies the input array directly without allocating significant extra space.</li>
<li><strong>Context</strong>: This problem frequently appears in interviews and is a good demonstration of efficient, specialized sorting algorithms.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a <strong>three-pointer approach</strong> to partition the array into three sections:</p>
<ol>
<li><strong><code>low</code></strong>: Points to the boundary between the 0s section and the unclassified section. All elements <code>nums[0...low-1]</code> are 0s.</li>
<li><strong><code>mid</code></strong>: The current element being examined. It traverses the array from left to right.</li>
<li><strong><code>high</code></strong>: Points to the boundary between the unclassified section and the 2s section. All elements <code>nums[high+1...n-1]</code> are 2s.</li>
</ol>
<p>The <code>while mid &lt;= high</code> loop continues as long as there are unclassified elements:</p>
<ul>
<li><strong>If <code>nums[mid] == 0</code></strong>:<ul>
<li>This element belongs in the 0s section.</li>
<li>It's swapped with the element at <code>nums[low]</code>.</li>
<li>Both <code>low</code> and <code>mid</code> pointers are incremented. <code>low</code> moves past the newly placed 0, and <code>mid</code> moves to the next unclassified element.</li>
</ul>
</li>
<li><strong>If <code>nums[mid] == 1</code></strong>:<ul>
<li>This element is already in its correct relative position (between 0s and 2s).</li>
<li>Only <code>mid</code> is incremented to move to the next unclassified element.</li>
</ul>
</li>
<li><strong>If <code>nums[mid] == 2</code></strong>:<ul>
<li>This element belongs in the 2s section.</li>
<li>It's swapped with the element at <code>nums[high]</code>.</li>
<li>Only <code>high</code> is decremented. Crucially, <code>mid</code> is <em>not</em> incremented because the element that was swapped <em>into</em> <code>nums[mid]</code> (which came from <code>nums[high]</code>) has not yet been checked and could be a 0, 1, or 2. It needs to be re-evaluated in the next iteration.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>In-place sorting</strong>: The core requirement is met by using swaps to rearrange elements within the original array, leading to O(1) auxiliary space.</li>
<li><strong>Three-pointer partitioning</strong>: This is the most efficient way to solve the Dutch National Flag problem because it allows for a single pass through the array. It cleverly maintains partitions for 0s, 1s (implicitly), and 2s.</li>
<li><strong>Leveraging limited distinct values</strong>: The algorithm's efficiency relies on knowing there are only three possible values (0, 1, 2). This allows for direct comparisons and precise placement logic, unlike general sorting algorithms.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(n)</strong><ul>
<li>The <code>mid</code> pointer traverses the array from <code>0</code> to <code>n-1</code>. In each step, <code>mid</code> either increments or <code>high</code> decrements, effectively processing one element or shrinking the unclassified region.</li>
<li>Each element is visited and potentially swapped at most a constant number of times.</li>
<li>Thus, the algorithm makes a single pass over the array.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>Only a few constant-space variables (<code>low</code>, <code>mid</code>, <code>high</code>) are used to manage the pointers. No additional data structures are allocated that grow with the input size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty list (<code>nums = []</code>)</strong>:<ul>
<li><code>high</code> becomes -1. The <code>while mid &lt;= high</code> condition (<code>0 &lt;= -1</code>) is immediately false. The loop does not execute, and the function correctly does nothing.</li>
</ul>
</li>
<li><strong>List with one element (<code>nums = [0]</code>, <code>[1]</code>, or <code>[2]</code>)</strong>:<ul>
<li>The loop runs once. The element is correctly classified. For example, <code>[2]</code> will swap <code>nums[0]</code> with <code>nums[0]</code>, then <code>high</code> becomes -1, terminating the loop.</li>
</ul>
</li>
<li><strong>List with all identical elements (<code>[0,0,0]</code>, <code>[1,1,1]</code>, <code>[2,2,2]</code>)</strong>:<ul>
<li>The logic correctly handles these cases, performing swaps only when necessary (e.g., <code>[2,2,2]</code> will swap <code>mid</code> with <code>high</code> repeatedly until <code>mid &gt; high</code>).</li>
</ul>
</li>
<li><strong>Correctness Invariants</strong>:<ul>
<li>Elements <code>nums[0...low-1]</code> are always 0.</li>
<li>Elements <code>nums[high+1...n-1]</code> are always 2.</li>
<li>Elements <code>nums[low...mid-1]</code> are eventually 1s (though this region might contain unclassified elements during execution if <code>low</code> hasn't caught up with <code>mid</code>).</li>
<li>When the loop terminates (<code>mid &gt; high</code>), all elements have been correctly placed.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite clear and follows standard variable naming for this algorithm. No significant readability improvements are needed.</li>
<li><strong>Alternative Algorithms</strong>:<ul>
<li><strong>Counting Sort</strong>: Count the occurrences of 0s, 1s, and 2s. Then, overwrite the original array by placing the counted number of 0s, then 1s, then 2s.<ul>
<li><strong>Time</strong>: O(n + k) where k is the number of distinct values (here, k=3). So, O(n).</li>
<li><strong>Space</strong>: O(k). It uses a small auxiliary array to store counts. This is slightly less space-efficient than the three-pointer approach but still excellent for small <code>k</code>.</li>
</ul>
</li>
<li><strong>General-purpose Sorting</strong>: Using Python's built-in <code>nums.sort()</code> or algorithms like Merge Sort, Quick Sort, etc.<ul>
<li><strong>Time</strong>: O(n log n).</li>
<li><strong>Space</strong>: O(log n) to O(n) depending on the specific algorithm and implementation.</li>
<li><strong>Trade-off</strong>: While simpler to write (<code>nums.sort()</code>), it's asymptotically less efficient for this specific problem due to its generalized nature.</li>
</ul>
</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this code. It operates purely on the provided array in memory.</li>
<li><strong>Performance</strong>:<ul>
<li>The algorithm is <strong>optimally fast</strong> in terms of time complexity (O(n)), as every element must be examined at least once to sort it.</li>
<li>It is also <strong>optimally space-efficient</strong> (O(1) auxiliary space) due to its in-place nature.</li>
<li>For extremely large arrays, the cache locality of iterating linearly through the array is generally good, contributing to practical performance.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: None 
        """
        low = 0
        mid = 0
        high = len(nums) - 1

        while mid <= high:
            if nums[mid] == 0:
                nums[low], nums[mid] = nums[mid], nums[low]
                low += 1
                mid += 1
            elif nums[mid] == 1:
                mid += 1
            else:  # nums[mid] == 2
                nums[mid], nums[high] = nums[high], nums[mid]
                high -= 1
```
