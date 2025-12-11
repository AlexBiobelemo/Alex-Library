## Median of Two Sorted Arrays
**Language:** python
**Tags:** binary search,median,sorted arrays,partition

### Description:
<p>This code solves the classic "Median of Two Sorted Arrays" problem, a popular interview question known for its optimal logarithmic time complexity solution.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code implements an efficient algorithm to find the median of two sorted arrays, <code>nums1</code> and <code>nums2</code>. The primary goal is to achieve a time complexity of <code>O(log(min(m, n)))</code>, where <code>m</code> and <code>n</code> are the lengths of the arrays, without explicitly merging them. This is significantly faster than a simple merge (<code>O(m+n)</code>).</p>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to perform a binary search on the <em>partition points</em> (cuts) of the two arrays to divide them into two logical halves such that:</p>
<ol>
<li>The left half contains <code>half_len</code> elements in total.</li>
<li>All elements in the left half are less than or equal to all elements in the right half.</li>
</ol>
<p>Once such a partition is found, the median can be determined from the elements at the boundaries of these partitions.</p>
<p>Here's the step-by-step breakdown:</p>
<ol>
<li><p><strong>Preparation</strong>:</p>
<ul>
<li>The arrays are swapped if <code>nums1</code> is longer than <code>nums2</code> to ensure the binary search is always performed on the shorter array (<code>nums1</code>). This optimizes the search space.</li>
<li><code>half_len</code> is calculated: <code>(m + n + 1) // 2</code>. This represents the total number of elements desired in the combined "left partition". The <code>+1</code> handles both odd and even total lengths elegantly, ensuring <code>max(L1, L2)</code> is always one of the median elements for even lengths, or the median itself for odd lengths.</li>
</ul>
</li>
<li><p><strong>Binary Search for the Cut</strong>:</p>
<ul>
<li>A binary search is performed on the possible cut positions <code>i</code> within <code>nums1</code> (from <code>0</code> to <code>m</code>).</li>
<li>For each <code>i</code>, the corresponding cut position <code>j</code> in <code>nums2</code> is derived as <code>j = half_len - i</code>. This ensures that the combined left partition (<code>nums1[0...i-1]</code> and <code>nums2[0...j-1]</code>) always contains <code>half_len</code> elements.</li>
</ul>
</li>
<li><p><strong>Boundary Element Identification</strong>:</p>
<ul>
<li>Four critical elements are identified around these cuts:<ul>
<li><code>L1</code>: The last element of <code>nums1</code>'s left partition (<code>nums1[i-1]</code>). Uses <code>float('-inf')</code> if <code>i=0</code>.</li>
<li><code>R1</code>: The first element of <code>nums1</code>'s right partition (<code>nums1[i]</code>). Uses <code>float('inf')</code> if <code>i=m</code>.</li>
<li><code>L2</code>: The last element of <code>nums2</code>'s left partition (<code>nums2[j-1]</code>). Uses <code>float('-inf')</code> if <code>j=0</code>.</li>
<li><code>R2</code>: The first element of <code>nums2</code>'s right partition (<code>nums2[j]</code>). Uses <code>float('inf')</code> if <code>j=n</code>.</li>
</ul>
</li>
<li>The use of <code>float('-inf')</code> and <code>float('inf')</code> is crucial for handling edge cases where a partition might be empty (e.g., <code>i=0</code> or <code>j=n</code>).</li>
</ul>
</li>
<li><p><strong>Perfect Split Check</strong>:</p>
<ul>
<li>The algorithm checks if the partition is "perfect" by verifying two conditions:<ul>
<li><code>L1 &lt;= R2</code>: The largest element in <code>nums1</code>'s left part is less than or equal to the smallest element in <code>nums2</code>'s right part.</li>
<li><code>L2 &lt;= R1</code>: The largest element in <code>nums2</code>'s left part is less than or equal to the smallest element in <code>nums1</code>'s right part.</li>
</ul>
</li>
<li>If both conditions are true, it means all elements to the left of the combined cut are indeed less than or equal to all elements to the right.</li>
</ul>
</li>
<li><p><strong>Median Calculation</strong>:</p>
<ul>
<li>If a perfect split is found:<ul>
<li><code>max_left = max(L1, L2)</code>: This is the largest element in the combined left partition.</li>
<li>If the total number of elements <code>(m + n)</code> is odd, <code>max_left</code> is the median.</li>
<li>If the total number of elements <code>(m + n)</code> is even, the median is the average of <code>max_left</code> and <code>min_right = min(R1, R2)</code> (the smallest element in the combined right partition).</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Adjusting Search Space</strong>:</p>
<ul>
<li>If <code>L1 &gt; R2</code>: <code>nums1</code>'s cut <code>i</code> is too far to the right. This implies <code>nums1</code>'s left part has elements that are too large, pushing the median to the left. The search space <code>R</code> is adjusted to <code>i - 1</code>.</li>
<li>If <code>L2 &gt; R1</code>: <code>nums2</code>'s cut <code>j</code> is too far to the right (meaning <code>i</code> is too far to the left). This implies <code>nums2</code>'s left part has elements that are too large. The search space <code>L</code> is adjusted to <code>i + 1</code>.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Binary Search on Smaller Array</strong>: Swapping arrays to ensure <code>m &lt;= n</code> is a critical optimization. It guarantees the binary search range is <code>log(min(m, n))</code>, directly contributing to the optimal time complexity.</li>
<li><strong>Partition Strategy</strong>: The algorithm cleverly doesn't try to find <code>k</code> (the median index) directly in a merged array but rather finds the <em>cuts</em> in the original arrays that would define the <code>k</code>th element.</li>
<li><strong><code>half_len = (m + n + 1) // 2</code></strong>: This formula is key to simplifying the median calculation for both odd and even total lengths. It effectively defines the number of elements in the left partition, ensuring that <code>max(L1, L2)</code> and <code>min(R1, R2)</code> correctly correspond to the median elements.</li>
<li><strong>Sentinel Values (<code>float('-inf')</code>, <code>float('inf')</code>)</strong>: Using these values for <code>L1, R1, L2, R2</code> when <code>i</code> or <code>j</code> are at the boundaries (<code>0</code> or <code>m/n</code>) makes the comparison logic <code>L1 &lt;= R2 and L2 &lt;= R1</code> universally applicable without needing special checks for empty partitions.</li>
<li><strong>Separation of Concerns</strong>: The conditions <code>L1 &gt; R2</code> and <code>L2 &gt; R1</code> clearly guide the binary search, effectively eliminating half of the search space in each iteration based on whether <code>i</code> (the cut in <code>nums1</code>) needs to move left or right.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: <code>O(log(min(m, n)))</code><ul>
<li>The initial swap and calculation of <code>half_len</code> are <code>O(1)</code>.</li>
<li>The <code>while</code> loop performs a binary search. The search space is <code>m</code> (the length of the smaller array).</li>
<li>Each iteration of the loop involves constant time operations (array indexing, comparisons, arithmetic).</li>
<li>Therefore, the total time complexity is logarithmic with respect to the length of the smaller array.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(1)</code><ul>
<li>The algorithm uses a fixed number of variables regardless of the input array sizes. No auxiliary data structures are created that grow with <code>m</code> or <code>n</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution is robust and handles various edge cases correctly:</p>
<ul>
<li><strong>Empty Arrays</strong>:<ul>
<li>If <code>nums1</code> is empty (<code>m=0</code>): The initial swap will ensure <code>nums1</code> is the shorter (or empty) array. <code>i</code> will remain <code>0</code> in the binary search. <code>L1</code> will be <code>-inf</code>, <code>R1</code> will be <code>inf</code>. The search effectively reduces to finding the median of <code>nums2</code> alone.</li>
<li>If both arrays are empty: <code>m=0, n=0</code>. <code>half_len</code> would be <code>0</code>. The loop <code>L &lt;= R</code> (<code>0 &lt;= 0</code>) runs. <code>i=0, j=0</code>. <code>L1, L2</code> are <code>-inf</code>, <code>R1, R2</code> are <code>inf</code>. <code>max_left</code> is <code>-inf</code>, <code>min_right</code> is <code>inf</code>. The median would be <code>(-inf + inf)/2</code> which is problematic. <em>Correction</em>: The problem statement usually implies non-empty arrays or that a <code>0.0</code> or <code>None</code> return is acceptable for empty inputs. This specific solution returns <code>0.0</code> if <code>L &lt;= R</code> fails (which it shouldn't for valid median problems), but for <code>m=0, n=0</code>, <code>half_len = 0</code>, <code>i=0, j=0</code>, <code>L1=-inf, R1=inf, L2=-inf, R2=inf</code>. The split condition <code>-inf &lt;= inf</code> and <code>-inf &lt;= inf</code> is true. <code>max_left</code> would be <code>-inf</code>, <code>min_right</code> <code>inf</code>. For <code>m+n=0</code> (even), it would return <code>(-inf + inf)/2</code> which would be <code>nan</code>. This is an edge case specific to <em>both</em> arrays being empty that usually isn't expected to yield a median. Assuming at least one array has elements.</li>
</ul>
</li>
<li><strong>Arrays of greatly different sizes</strong>: Handled by the initial swap and the <code>j = half_len - i</code> calculation.</li>
<li><strong>Arrays with one element</strong>: Works correctly due to <code>half_len</code> logic and sentinel values.</li>
<li><strong>All elements are the same</strong>: The comparison logic <code>L1 &lt;= R2</code> etc., correctly handles duplicates.</li>
<li><strong>Median is at the boundary of one array</strong>: E.g., median is the largest element of <code>nums1</code> or smallest of <code>nums2</code>. The <code>float('inf')</code> and <code>float('-inf')</code> values ensure these boundary conditions are treated correctly during comparisons.</li>
<li><strong>Odd vs. Even total length</strong>: Explicitly handled by the <code>if (m + n) % 2 == 1:</code> condition, returning either <code>max_left</code> or <code>(max_left + min_right) / 2.0</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite clear with good variable names and comments. No significant readability improvements are immediately obvious.</li>
<li><strong>Error Handling</strong>: For strict production code, one might consider raising an exception or returning a specific value if both arrays are empty, as the current behavior would result in <code>nan</code> (Not a Number) from <code>(-inf + inf) / 2.0</code>, which might not be desired. However, typical competitive programming problems imply valid inputs.</li>
<li><strong>Alternative Approaches</strong>:<ul>
<li><strong>Merge and Find</strong>: Create a merged sorted array of <code>nums1</code> and <code>nums2</code> and then find the median. This is simpler to implement but has <code>O(m+n)</code> time complexity, which is less efficient.</li>
<li><strong>Generalized Kth Element</strong>: This problem is a specific instance of finding the k-th smallest element in two sorted arrays. The current binary search approach is a direct application of that more general algorithm.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The chosen algorithm is highly optimized for performance, achieving the theoretical lower bound for comparison-based solutions to this problem, <code>O(log(min(m, n)))</code>. There are no further performance improvements to be made given the problem constraints and nature.</li>
<li><strong>Security</strong>: This algorithm is purely mathematical and operates on numerical data. It does not involve external input, network communication, file operations, or user interaction beyond receiving two lists of integers. Therefore, there are no direct security vulnerabilities inherent in the code itself.</li>
</ul>


### Code:
```python
class Solution(object):
    def findMedianSortedArrays(self, nums1, nums2):
        """
        Finds the median of two sorted arrays in O(log(min(m, n))) time using binary search.
        
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """

        m, n = len(nums1), len(nums2)
        
        # 1. Ensure binary search is performed on the smaller array (m <= n)
        # This keeps the binary search space smaller: O(log(m))
        if m > n:
            nums1, nums2, m, n = nums2, nums1, n, m

        # half_len is the total number of elements required on the left side of the median cut.
        # (m + n + 1) // 2 ensures we get the middle element's position for odd/even lengths.
        half_len = (m + n + 1) // 2
        
        # Binary search range for the cut 'i' in nums1 (the smaller array)
        L, R = 0, m

        while L <= R:
            # i is the cut position in nums1. i elements are on the left.
            i = (L + R) // 2
            # j is the required cut position in nums2. j elements are on the left.
            # Total left elements = i + j = half_len
            j = half_len - i

            # L1: max element in the left partition from nums1 (or -inf if i=0)
            L1 = nums1[i-1] if i > 0 else float('-inf')
            # R1: min element in the right partition from nums1 (or +inf if i=m)
            R1 = nums1[i] if i < m else float('inf')
            
            # L2: max element in the left partition from nums2 (or -inf if j=0)
            L2 = nums2[j-1] if j > 0 else float('-inf')
            # R2: min element in the right partition from nums2 (or +inf if j=n)
            R2 = nums2[j] if j < n else float('inf')

            # Check for the Perfect Split Condition: L1 <= R2 and L2 <= R1
            # All elements on the left (L1, L2) must be <= all elements on the right (R1, R2).
            if L1 <= R2 and L2 <= R1:
                # The perfect split is found!
                
                # max_left is the largest element in the combined left partition
                max_left = max(L1, L2)
                
                # If total length is odd, the median is the single element: max_left
                if (m + n) % 2 == 1:
                    return float(max_left)
                
                # If total length is even, the median is the average of max_left and min_right
                min_right = min(R1, R2)
                return (max_left + min_right) / 2.0
            
            elif L1 > R2:
                # L1 is too large, meaning the cut 'i' in nums1 is too far right.
                # Move 'i' to the left (search in the left half of [L, R])
                R = i - 1
            
            else: # L2 > R1
                # L2 is too large, meaning the cut 'j' in nums2 is too far right (or 'i' is too far left).
                # Move 'i' to the right (search in the right half of [L, R])
                L = i + 1
        return 0.0
```
