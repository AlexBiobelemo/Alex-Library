## Find First and Last Position of Element in Sorted Array
**Language:** python
**Tags:** python,binary search,array,algorithm

### Description:
<p>This code snippet implements a solution to find the starting and ending positions of a given <code>target</code> value in a sorted array <code>nums</code>. If the target is not found, it returns <code>[-1, -1]</code>.</p>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of the <code>searchRange</code> method is to locate the first and last occurrences of a specific <code>target</code> integer within a sorted list of integers <code>nums</code>. It's designed for efficiency, particularly for large arrays, leveraging the sorted nature of the input.</p>
<h3>2. How It Works</h3>
<p>The solution employs two modified binary search algorithms:</p>
<ul>
<li><strong><code>find_first(arr, val)</code></strong>: This function performs a binary search to find the <em>leftmost</em> (first) occurrence of <code>val</code>.<ul>
<li>When <code>arr[mid]</code> equals <code>val</code>, it stores <code>mid</code> as a potential <code>first_pos</code> but continues searching in the <em>left half</em> (<code>high = mid - 1</code>) to see if an even earlier occurrence exists.</li>
<li>If <code>arr[mid]</code> is less than <code>val</code>, the target must be in the right half (<code>low = mid + 1</code>).</li>
<li>If <code>arr[mid]</code> is greater than <code>val</code>, the target must be in the left half (<code>high = mid - 1</code>).</li>
</ul>
</li>
<li><strong><code>find_last(arr, val)</code></strong>: This function performs a binary search to find the <em>rightmost</em> (last) occurrence of <code>val</code>.<ul>
<li>When <code>arr[mid]</code> equals <code>val</code>, it stores <code>mid</code> as a potential <code>last_pos</code> but continues searching in the <em>right half</em> (<code>low = mid + 1</code>) to see if a later occurrence exists.</li>
<li>The comparison logic for <code>arr[mid] &lt; val</code> and <code>arr[mid] &gt; val</code> is the same as <code>find_first</code>.</li>
</ul>
</li>
<li>Finally, <code>searchRange</code> calls both helper functions with the input <code>nums</code> and <code>target</code> and returns their results as a list <code>[first, last]</code>.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Two Binary Searches</strong>: The core design decision is to use two distinct binary searches. One is specialized to find the absolute first occurrence, and the other for the absolute last. This is more robust and generally cleaner than trying to combine both into a single, overly complex search.</li>
<li><strong>Modified Binary Search Logic</strong>:<ul>
<li>For <code>find_first</code>, upon finding a match (<code>arr[mid] == val</code>), the search range is shifted to <code>high = mid - 1</code>. This greedily tries to find an even smaller index, guaranteeing the smallest possible index.</li>
<li>For <code>find_last</code>, upon finding a match (<code>arr[mid] == val</code>), the search range is shifted to <code>low = mid + 1</code>. This greedily tries to find an even larger index, guaranteeing the largest possible index.</li>
</ul>
</li>
<li><strong>Initialization of <code>first_pos</code>/<code>last_pos</code></strong>: Both are initialized to <code>-1</code>. This elegantly handles the case where the <code>target</code> is not found, as <code>-1</code> will be returned.</li>
<li><strong>Midpoint Calculation</strong>: <code>mid = low + (high - low) // 2</code> is used instead of <code>(low + high) // 2</code> to prevent potential integer overflow in languages like C++ or Java if <code>low</code> and <code>high</code> are very large. In Python, this is less of a concern due to arbitrary-precision integers, but it's a good practice to maintain.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log N)</strong><ul>
<li>Both <code>find_first</code> and <code>find_last</code> are standard binary search algorithms, each taking O(log N) time, where N is the number of elements in <code>nums</code>.</li>
<li>Since these are executed sequentially, the total time complexity remains O(log N).</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a constant amount of extra space for variables like <code>low</code>, <code>high</code>, <code>mid</code>, <code>first_pos</code>, and <code>last_pos</code>. No auxiliary data structures that scale with input size are created.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly:</p>
<ul>
<li><strong>Empty Array (<code>nums = []</code>)</strong>: <code>len(arr) - 1</code> becomes -1. The <code>while low &lt;= high</code> loop condition <code>0 &lt;= -1</code> (or similar for <code>low</code> and <code>high</code> values if length is 0) will immediately be false, and <code>find_first</code> and <code>find_last</code> will return <code>-1</code>, leading to <code>[-1, -1]</code>, which is correct.</li>
<li><strong>Target Not Present</strong>: If the <code>target</code> is not in <code>nums</code>, <code>first_pos</code> and <code>last_pos</code> will remain <code>-1</code> (their initial values), and <code>[-1, -1]</code> will be returned. This is correct.</li>
<li><strong>Target Present Once</strong>: Both <code>find_first</code> and <code>find_last</code> will correctly identify the single index where the target resides, returning <code>[index, index]</code>.</li>
<li><strong>Target Present Multiple Times</strong>: The modified binary searches correctly find the extreme boundaries.</li>
<li><strong>Target at Beginning/End</strong>: The algorithm correctly handles targets located at the first or last indices of the array.</li>
<li><strong>All Elements are Target</strong>: For <code>nums = [7, 7, 7, 7]</code> and <code>target = 7</code>, <code>find_first</code> will return <code>0</code> and <code>find_last</code> will return <code>3</code>, resulting in <code>[0, 3]</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Python's <code>bisect</code> Module</strong>: For increased conciseness and potentially slightly better performance (as they are implemented in C), Python's <code>bisect_left</code> and <code>bisect_right</code> functions can be used:</p>
<pre><code class="language-python">import bisect

class Solution(object):
    def searchRange(self, nums, target):
        left_idx = bisect.bisect_left(nums, target)
        if left_idx == len(nums) or nums[left_idx] != target:
            return [-1, -1]
        
        right_idx = bisect.bisect_right(nums, target) - 1
        return [left_idx, right_idx]
</code></pre>
<p>This version is more idiomatic Python for this specific problem. Note that <code>bisect_right</code> returns an insertion point <em>after</em> existing entries, so <code> - 1</code> is needed to get the index of the last occurrence. An extra check is needed for <code>bisect_left</code> to ensure the <code>target</code> was actually found, as <code>bisect_left</code> will return an insertion point even if the target is not present.</p>
</li>
<li><p><strong>Combining Searches (Less Optimal)</strong>: One could theoretically perform a standard binary search to find <em>any</em> occurrence of the target, and then, if found, use two separate linear scans (or modified binary searches on sub-arrays) to expand left and right from that point. This would likely be less efficient in the worst-case (e.g., array full of targets), as the linear scans could degrade to O(N). The current two-binary-search approach is optimal.</p>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The current implementation is highly performant (O(log N)) and optimal for the given constraints (sorted array). There are no obvious performance bottlenecks.</li>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this code, as it deals with numerical data processing and does not interact with external systems, user input that could be malicious, or sensitive data.</li>
</ul>


### Code:
```python
class Solution(object):
    def searchRange(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        
        def find_first(arr, val):
            low, high = 0, len(arr) - 1
            first_pos = -1
            while low <= high:
                mid = low + (high - low) // 2
                if arr[mid] == val:
                    first_pos = mid
                    high = mid - 1  # Try to find an earlier occurrence
                elif arr[mid] < val:
                    low = mid + 1
                else:
                    high = mid - 1
            return first_pos

        def find_last(arr, val):
            low, high = 0, len(arr) - 1
            last_pos = -1
            while low <= high:
                mid = low + (high - low) // 2
                if arr[mid] == val:
                    last_pos = mid
                    low = mid + 1  # Try to find a later occurrence
                elif arr[mid] < val:
                    low = mid + 1
                else:
                    high = mid - 1
            return last_pos

        first = find_first(nums, target)
        last = find_last(nums, target)

        return [first, last]
```
