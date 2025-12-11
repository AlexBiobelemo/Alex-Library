## Search In Rotated Sorted Array
**Language:** python
**Tags:** binary search,algorithm,array,rotated sorted array

### Description:
<p>This code implements a modified binary search algorithm to find a target value in a rotated sorted array.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given a sorted array that has been rotated at some pivot point (e.g., <code>[0,1,2,4,5,6,7]</code> might become <code>[4,5,6,7,0,1,2]</code>), find the index of a given <code>target</code> value.</li>
<li><strong>Goal:</strong> Return the index of <code>target</code> if it exists in <code>nums</code>, otherwise return <code>-1</code>.</li>
<li><strong>Approach:</strong> Adapt the efficient binary search algorithm to handle the rotation.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a binary search approach, but with an added step to determine which half of the current search space is <em>sorted</em>.</p>
<ol>
<li><p><strong>Initialization:</strong></p>
<ul>
<li><code>low</code> is set to the first index (0).</li>
<li><code>high</code> is set to the last index (<code>len(nums) - 1</code>).</li>
</ul>
</li>
<li><p><strong>Iterative Search:</strong> The <code>while low &lt;= high</code> loop continues as long as there's a valid search space.</p>
<ul>
<li><strong>Midpoint Calculation:</strong> <code>mid = (low + high) // 2</code> calculates the middle index.</li>
<li><strong>Target Found:</strong> If <code>nums[mid]</code> equals <code>target</code>, its index <code>mid</code> is returned immediately.</li>
<li><strong>Determine Sorted Half:</strong><ul>
<li><strong>Case 1: <code>nums[low] &lt;= nums[mid]</code></strong>: This indicates that the left half (<code>nums[low]</code> to <code>nums[mid]</code>) is sorted.<ul>
<li><strong>Target in Left Half?</strong>: Check if <code>target</code> falls within the range of the sorted left half (<code>nums[low] &lt;= target &lt; nums[mid]</code>). If yes, discard the right half by setting <code>high = mid - 1</code>.</li>
<li><strong>Target in Right Half (Unsorted)?</strong>: If not, <code>target</code> must be in the unsorted right half, so discard the left half by setting <code>low = mid + 1</code>.</li>
</ul>
</li>
<li><strong>Case 2: <code>nums[low] &gt; nums[mid]</code></strong>: This indicates that the right half (<code>nums[mid]</code> to <code>nums[high]</code>) is sorted. (The pivot point is in the left half).<ul>
<li><strong>Target in Right Half?</strong>: Check if <code>target</code> falls within the range of the sorted right half (<code>nums[mid] &lt; target &lt;= nums[high]</code>). If yes, discard the left half by setting <code>low = mid + 1</code>.</li>
<li><strong>Target in Left Half (Unsorted)?</strong>: If not, <code>target</code> must be in the unsorted left half, so discard the right half by setting <code>high = mid - 1</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Target Not Found:</strong> If the loop finishes without finding the target (i.e., <code>low &gt; high</code>), it means the target is not present in the array, and <code>-1</code> is returned.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm:</strong> Modified Binary Search. This is a crucial choice as it leverages the "partially sorted" nature of the array, even after rotation, to achieve logarithmic time complexity.</li>
<li><strong>Data Structure:</strong> List/Array. The algorithm operates directly on the input list, requiring random access to elements, which arrays provide efficiently.</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Benefit:</strong> Highly efficient for large datasets due to O(log N) time complexity.</li>
<li><strong>Cost:</strong> More complex conditional logic compared to a standard binary search, as it needs to identify the sorted subarray in each step.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log N)</strong><ul>
<li>In each iteration of the <code>while</code> loop, the search space (<code>high - low + 1</code>) is roughly halved. This is the defining characteristic of binary search, leading to logarithmic time complexity.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a constant amount of extra space for the <code>low</code>, <code>high</code>, and <code>mid</code> pointers, regardless of the input array size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Array (<code>nums = []</code>):</strong><ul>
<li><code>len(nums) - 1</code> will be -1. <code>low</code> (0) will be greater than <code>high</code> (-1) initially. The <code>while low &lt;= high</code> loop will not execute, and <code>-1</code> will be returned, which is correct.</li>
</ul>
</li>
<li><strong>Single Element Array (<code>nums = [5]</code>, <code>target = 5</code>):</strong><ul>
<li><code>low = 0</code>, <code>high = 0</code>, <code>mid = 0</code>. <code>nums[0] == target</code>, returns <code>0</code>. Correct.</li>
</ul>
</li>
<li><strong>Single Element Array (<code>nums = [5]</code>, <code>target = 3</code>):</strong><ul>
<li><code>low = 0</code>, <code>high = 0</code>, <code>mid = 0</code>. <code>nums[0] != target</code>. <code>nums[low] &lt;= nums[mid]</code> (5 &lt;= 5) is true. <code>nums[low] &lt;= target &lt; nums[mid]</code> (5 &lt;= 3 &lt; 5) is false. <code>low</code> becomes <code>mid + 1</code> (1). Loop terminates (<code>low</code> &gt; <code>high</code>). Returns <code>-1</code>. Correct.</li>
</ul>
</li>
<li><strong>Target at Extremes (first/last element):</strong> Handled correctly by the <code>low &lt;= high</code> condition and pointer adjustments.</li>
<li><strong>Array Not Rotated (<code>nums = [1,2,3,4,5]</code>):</strong><ul>
<li>The <code>if nums[low] &lt;= nums[mid]</code> condition will always be true, effectively performing a standard binary search. Correct.</li>
</ul>
</li>
<li><strong>Pivot at the very beginning/end:</strong> (e.g., <code>[1,2,3,4,5]</code> or <code>[2,3,4,5,1]</code>). The logic correctly identifies the sorted half and narrows the search.</li>
<li><strong>Duplicates:</strong> The problem statement for this specific LeetCode problem (33. Search in Rotated Sorted Array) usually implies unique elements. If duplicates were allowed (e.g., <code>[1,1,1,1,0,1]</code>), this exact implementation might fail or behave unexpectedly. However, for the standard "unique elements" version, it is correct.</li>
<li><strong>Correctness Rationale:</strong> The core idea is that at least one half of the array (from <code>low</code> to <code>mid</code>, or <code>mid</code> to <code>high</code>) <em>must</em> be sorted. By identifying this sorted half, we can determine if the <code>target</code> could possibly reside there. If it does, we narrow our search to that sorted half. If not, the target <em>must</em> be in the <em>other</em>, unsorted half, and we narrow our search there. This guarantees the search space is always reduced logarithmically.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The current code is quite readable for anyone familiar with binary search and its variations. No major improvements needed, but sometimes adding comments for the "Left half is sorted" vs. "Right half is sorted" distinction can be helpful for beginners.</li>
<li><strong>Performance:</strong> The algorithm is already optimal in terms of asymptotic time complexity (O(log N)) for a comparison-based search. No further performance improvements are generally possible without changing the problem constraints (e.g., using a hash map if modifications were allowed, but that changes space complexity).</li>
<li><strong>Robustness:</strong><ul>
<li>For a production system, one might add input validation to ensure <code>nums</code> is indeed a list and contains comparable elements.</li>
</ul>
</li>
<li><strong>Alternatives:</strong><ul>
<li><strong>Linear Scan:</strong> O(N) time complexity, much worse for large inputs.</li>
<li>There isn't a significantly better <em>asymptotic</em> alternative for this specific problem while maintaining O(1) space.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> Excellent. The O(log N) time complexity makes this solution highly performant, capable of handling very large arrays efficiently.</li>
<li><strong>Security:</strong> This algorithm has no direct security implications. It simply searches for a value in an array. Integer overflow for <code>(low + high) // 2</code> is not a concern in Python due to its arbitrary-precision integers, though it is a common bug in languages like Java/C++ where <code>(low + high)</code> could exceed the maximum integer value. The <code>low + (high - low) // 2</code> alternative is often used in those languages to mitigate this, but isn't necessary here.</li>
</ul>


### Code:
```python
class Solution(object):
    def search(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        low = 0
        high = len(nums) - 1

        while low <= high:
            mid = (low + high) // 2

            if nums[mid] == target:
                return mid

            # Determine which half is sorted
            if nums[low] <= nums[mid]:  # Left half is sorted
                if nums[low] <= target < nums[mid]:
                    # Target is in the sorted left half
                    high = mid - 1
                else:
                    # Target is in the unsorted right half
                    low = mid + 1
            else:  # Right half is sorted (nums[mid] < nums[high])
                if nums[mid] < target <= nums[high]:
                    # Target is in the sorted right half
                    low = mid + 1
                else:
                    # Target is in the unsorted left half
                    high = mid - 1
        
        return -1
```
