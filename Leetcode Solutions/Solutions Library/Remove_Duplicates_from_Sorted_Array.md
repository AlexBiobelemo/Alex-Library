## Remove Duplicates from Sorted Array
**Language:** python
**Tags:** python,array,two-pointers,in-place,duplicates

### Description:
<p>This code implements an efficient algorithm to remove duplicate elements from a <strong>sorted</strong> array in-place, returning the new length of the modified array.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Goal:</strong> Modify a sorted list of integers (<code>nums</code>) so that each unique element appears only once.</li>
<li><strong>In-Place Modification:</strong> The operation must be performed directly on the input list without allocating extra space for a new list.</li>
<li><strong>Return Value:</strong> The function returns an integer representing the new length of the array after removing duplicates. The first <code>k</code> elements of <code>nums</code> (where <code>k</code> is the returned value) will contain the unique elements in their original relative order.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a two-pointer approach to achieve in-place modification:</p>
<ul>
<li><strong>Initialization:</strong><ul>
<li>It first handles the edge case of an empty list, returning <code>0</code>.</li>
<li><code>insert_index</code> is initialized to <code>1</code>. This pointer tracks the next available position in the array where a new unique element should be placed. It also implicitly acts as the count of unique elements found so far (since <code>nums[0]</code> is always unique).</li>
</ul>
</li>
<li><strong>Iteration:</strong><ul>
<li>A <code>for</code> loop iterates through the array using an index <code>i</code>, starting from the second element (<code>nums[1]</code>). This <code>i</code> acts as a "read" pointer.</li>
</ul>
</li>
<li><strong>Comparison and Placement:</strong><ul>
<li>Inside the loop, <code>nums[i]</code> (the current element being read) is compared with <code>nums[insert_index - 1]</code> (the last unique element that was placed).</li>
<li>If <code>nums[i]</code> is <em>different</em> from <code>nums[insert_index - 1]</code>, it means <code>nums[i]</code> is a new unique element.</li>
<li>This new unique element <code>nums[i]</code> is then written to <code>nums[insert_index]</code>.</li>
<li><code>insert_index</code> is incremented to point to the next available slot for a unique element.</li>
</ul>
</li>
<li><strong>Result:</strong><ul>
<li>After iterating through the entire array, <code>insert_index</code> holds the total count of unique elements, which is returned. The elements from <code>nums[0]</code> to <code>nums[insert_index - 1]</code> now contain the unique elements.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Two-Pointer Technique:</strong> This is the core design.<ul>
<li>A "read" pointer (<code>i</code>) scans through all elements.</li>
<li>A "write" pointer (<code>insert_index</code>) places only unique elements.</li>
</ul>
</li>
<li><strong>In-Place Operation:</strong> By writing unique elements directly into the initial part of the input array, the algorithm avoids using extra space, fulfilling a common requirement for such problems.</li>
<li><strong>Leveraging Sorted Property:</strong> The algorithm critically depends on the input array being sorted. This allows direct comparison of <code>nums[i]</code> with the <em>immediately previous unique element</em> to detect duplicates. If the array were unsorted, a hash set or a different approach would be necessary.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The code iterates through the <code>nums</code> list exactly once with a single <code>for</code> loop.</li>
<li>Each operation inside the loop (comparison, assignment, increment) takes constant time, O(1).</li>
<li>Therefore, the total time complexity is linear, proportional to the number of elements <code>N</code> in the <code>nums</code> list.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>insert_index</code>, <code>i</code>) regardless of the input array's size.</li>
<li>No auxiliary data structures (like new lists, dictionaries, or sets) are allocated that grow with the input size.</li>
<li>Thus, the space complexity is constant.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty List (<code>nums = []</code>):</strong><ul>
<li>The <code>if not nums:</code> check correctly handles this, returning <code>0</code>. Correct.</li>
</ul>
</li>
<li><strong>List with One Element (<code>nums = [5]</code>):</strong><ul>
<li><code>insert_index</code> becomes <code>1</code>. The loop <code>for i in range(1, 1)</code> does not execute.</li>
<li>Returns <code>1</code>. Correct.</li>
</ul>
</li>
<li><strong>List with All Unique Elements (<code>nums = [1, 2, 3, 4]</code>):</strong><ul>
<li>Each element <code>nums[i]</code> will be different from <code>nums[insert_index - 1]</code>.</li>
<li><code>insert_index</code> will be incremented for every element, resulting in the original length <code>N</code>. Correct.</li>
</ul>
</li>
<li><strong>List with All Duplicate Elements (<code>nums = [7, 7, 7, 7]</code>):</strong><ul>
<li><code>insert_index</code> starts at <code>1</code>.</li>
<li><code>nums[i]</code> will always be equal to <code>nums[insert_index - 1]</code> (which is <code>nums[0]</code>).</li>
<li><code>insert_index</code> will never be incremented.</li>
<li>Returns <code>1</code>. Correct (only one unique element, <code>7</code>). The array effectively becomes <code>[7, 7, 7, 7]</code>, but only <code>nums[0]</code> is considered relevant.</li>
</ul>
</li>
<li><strong>Mixed Duplicates (<code>nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]</code>):</strong><ul>
<li>The logic correctly identifies <code>0</code>, then skips <code>0</code> (duplicate), identifies <code>1</code>, skips <code>1</code> (duplicates), identifies <code>2</code>, skips <code>2</code> (duplicate), etc.</li>
<li>The <code>nums</code> array will be modified to contain <code>[0, 1, 2, 3, 4, ..., ]</code> in its initial part, and <code>insert_index</code> will correctly return <code>5</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already highly readable, with clear variable names and helpful comments.</li>
<li><strong>No Significant Performance Improvements:</strong> For the given constraints (sorted array, in-place, O(1) space), the current O(N) time complexity is optimal, meaning no further algorithmic speed-ups are possible.</li>
<li><strong>Pythonic Alternatives (if not in-place or O(1) space were required):</strong><ul>
<li><code>list(set(nums))</code>: Converts to a set to remove duplicates, then back to a list. <strong>Caveat:</strong> Does not preserve original order and requires O(N) extra space.</li>
<li><code>list(collections.OrderedDict.fromkeys(nums))</code>: Preserves order and removes duplicates. <strong>Caveat:</strong> Requires O(N) extra space.</li>
</ul>
</li>
<li><strong>Generalization for Unsorted Arrays:</strong> If the input array were <em>not</em> sorted, this specific two-pointer approach would not work. An alternative would be to use a hash set to keep track of seen elements, requiring O(N) space.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> Not applicable. This algorithm does not involve any security-sensitive operations (e.g., input validation for external data, network communication, file access).</li>
<li><strong>Performance:</strong> As noted, the algorithm is optimally performant for the given constraints. There are no obvious performance bottlenecks or common misuses that would severely degrade its performance. It's a textbook solution for this specific problem.</li>
</ul>


### Code:
```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0

        # Pointer for the next position to place a unique element
        # Also represents the count of unique elements (k)
        insert_index = 1 

        # Iterate through the array starting from the second element
        for i in range(1, len(nums)):
            # If the current element is different from the previous unique element
            # (which is at nums[insert_index - 1]), it's a new unique element.
            if nums[i] != nums[insert_index - 1]:
                nums[insert_index] = nums[i]
                insert_index += 1
        
        return insert_index
```
