## Remove Duplicates from Sorted Array II
**Language:** python
**Tags:** python,array,two pointers,duplicates,in-place

### Description:
<p>This code snippet implements a solution to the classic "Remove Duplicates from Sorted Array II" problem, where each element can appear at most twice.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given a sorted integer array <code>nums</code>, remove the duplicates in-place such that each unique element appears at most twice. The relative order of the elements should be preserved.</li>
<li><strong>Goal:</strong> Return the new length of the array after removing the excess duplicates. The modified array should contain the result in its initial <code>j</code> elements.</li>
<li><strong>Approach:</strong> The solution uses a two-pointer technique to modify the array in-place efficiently.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Handle Empty Array:</strong> It first checks if the input <code>nums</code> list is empty. If so, it immediately returns <code>0</code> as there are no elements.</li>
<li><strong>Initialize Write Pointer:</strong> A pointer <code>j</code> (often called <code>write_ptr</code> or <code>insert_idx</code>) is initialized to <code>0</code>. This <code>j</code> tracks the position where the next valid element should be written in the modified array.</li>
<li><strong>Iterate with Read Pointer:</strong> The code then iterates through the entire <code>nums</code> array using a <code>for</code> loop with an index <code>i</code> (often called <code>read_ptr</code> or <code>current_idx</code>).</li>
<li><strong>Conditional Copying:</strong> For each element <code>nums[i]</code>:<ul>
<li>It checks a condition: <code>j &lt; 2</code> OR <code>nums[i] != nums[j-2]</code>.</li>
<li><strong><code>j &lt; 2</code></strong>: This part ensures that the first two elements encountered (regardless of their value) are always copied. This handles the case where an element appears once or twice.</li>
<li><strong><code>nums[i] != nums[j-2]</code></strong>: If <code>j</code> is <code>2</code> or greater, this is the core logic. It checks if the current element <code>nums[i]</code> is <em>different</em> from the element at <code>nums[j-2]</code> (which is two positions <em>behind</em> the current write pointer).<ul>
<li>If <code>nums[i]</code> <em>is</em> different from <code>nums[j-2]</code>, it means <code>nums[i]</code> is either a new unique number or it's the second occurrence of the number at <code>nums[j-1]</code>. In either case, it's a valid element to keep.</li>
<li>If <code>nums[i]</code> <em>is the same as</em> <code>nums[j-2]</code>, it implies that <code>nums[j-2]</code>, <code>nums[j-1]</code> (which must be equal to <code>nums[j-2]</code> because the array is sorted), and <code>nums[i]</code> are all the same. This would mean <code>nums[i]</code> is the <em>third</em> or more occurrence of that number, so it should be skipped.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Write and Advance:</strong> If the condition in step 4 is true, <code>nums[i]</code> is copied to <code>nums[j]</code>, and <code>j</code> is incremented, effectively adding the element to the "new" array.</li>
<li><strong>Return New Length:</strong> After iterating through all elements, <code>j</code> holds the final length of the modified array.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>In-Place Modification:</strong> The problem explicitly often implies or requires modifying the input array directly (<code>nums</code>) to save memory. This is achieved by overwriting elements.</li>
<li><strong>Two-Pointer Approach (<code>i</code> for reading, <code>j</code> for writing):</strong> This is a standard and highly efficient technique for problems involving in-place array manipulation, allowing a single pass.</li>
<li><strong>Core Condition <code>j &lt; 2 or nums[i] != nums[j-2]</code>:</strong><ul>
<li>This elegant condition is central to the solution. It correctly distinguishes between allowing the first two occurrences and skipping subsequent duplicates.</li>
<li>By comparing <code>nums[i]</code> with <code>nums[j-2]</code>, it looks "two steps back" in the <em>already processed and validated part</em> of the array. If the current <code>nums[i]</code> is the same as <code>nums[j-2]</code>, and the array is sorted, it implicitly means <code>nums[j-1]</code> must also be the same. Thus, we already have two occurrences, and <code>nums[i]</code> would be the third, which is disallowed.</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Space Efficiency (Pro):</strong> Achieves O(1) auxiliary space by modifying in-place.</li>
<li><strong>Readability (Con/Pro):</strong> The core condition <code>nums[i] != nums[j-2]</code> can be slightly non-obvious at first glance but is very concise once understood.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The algorithm iterates through the input array <code>nums</code> exactly once with the <code>i</code> pointer.</li>
<li>Each operation inside the loop (comparison, assignment, increment) is constant time, O(1).</li>
<li>Therefore, the total time complexity is linear with respect to the number of elements in <code>nums</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The solution modifies the input array in-place.</li>
<li>It uses only a few constant-space variables (<code>i</code>, <code>j</code>).</li>
<li>No auxiliary data structures (like new lists or hash maps) are created that scale with input size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty array (<code>[]</code>):</strong> Correctly handled by <code>if not nums: return 0</code>, returning <code>0</code>.</li>
<li><strong>Array with one element (<code>[1]</code>):</strong> <code>j</code> becomes <code>1</code>, returning <code>1</code>. Correct.</li>
<li><strong>Array with two identical elements (<code>[1,1]</code>):</strong> <code>j</code> becomes <code>2</code>, returning <code>2</code>. Correct.</li>
<li><strong>Array with distinct elements (<code>[1,2,3]</code>):</strong> All elements are copied, <code>j</code> becomes <code>3</code>, returning <code>3</code>. Correct.</li>
<li><strong>Array with all identical elements exceeding two (<code>[1,1,1,1,1]</code>):</strong><ul>
<li><code>i=0, nums[0]=1</code>: <code>j=0 &lt; 2</code> is true. <code>nums[0]=1</code>, <code>j=1</code>.</li>
<li><code>i=1, nums[1]=1</code>: <code>j=1 &lt; 2</code> is true. <code>nums[1]=1</code>, <code>j=2</code>.</li>
<li><code>i=2, nums[2]=1</code>: <code>j=2</code>. <code>j&lt;2</code> is false. <code>nums[2] (1) != nums[j-2] (nums[0]=1)</code> is false. Element skipped.</li>
<li>Subsequent identical elements are also skipped.</li>
<li><code>j</code> remains <code>2</code>, returning <code>2</code>. The array effectively contains <code>[1,1,...]</code> (first two <code>1</code>s are kept). Correct.</li>
</ul>
</li>
<li><strong>General case (<code>[1,1,2,2,2,3]</code>):</strong><ul>
<li><code>j=0</code>.</li>
<li><code>i=0, nums[0]=1</code>: <code>j&lt;2</code> is true. <code>nums[0]=1</code>, <code>j=1</code>.</li>
<li><code>i=1, nums[1]=1</code>: <code>j&lt;2</code> is true. <code>nums[1]=1</code>, <code>j=2</code>. (Array is <code>[1,1,2,2,2,3]</code>)</li>
<li><code>i=2, nums[2]=2</code>: <code>j=2</code>. <code>nums[2] (2) != nums[j-2] (nums[0]=1)</code> is true. <code>nums[2]=2</code>, <code>j=3</code>. (Array is <code>[1,1,2,2,2,3]</code>)</li>
<li><code>i=3, nums[3]=2</code>: <code>j=3</code>. <code>nums[3] (2) != nums[j-2] (nums[1]=1)</code> is true. <code>nums[3]=2</code>, <code>j=4</code>. (Array is <code>[1,1,2,2,2,3]</code>)</li>
<li><code>i=4, nums[4]=2</code>: <code>j=4</code>. <code>nums[4] (2) != nums[j-2] (nums[2]=2)</code> is false. Element skipped.</li>
<li><code>i=5, nums[5]=3</code>: <code>j=4</code>. <code>nums[5] (3) != nums[j-2] (nums[2]=2)</code> is true. <code>nums[4]=3</code>, <code>j=5</code>. (Array is <code>[1,1,2,2,3,3]</code>)</li>
<li>Returns <code>5</code>. The relevant part of <code>nums</code> is <code>[1,1,2,2,3]</code>. Correct.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability of Variable Names:</strong> While <code>i</code> and <code>j</code> are standard in algorithms, using more descriptive names like <code>read_idx</code> and <code>write_idx</code> could slightly enhance clarity for some readers, especially beginners.</li>
<li><strong>No Fundamental Performance Improvement:</strong> This solution is already optimal in terms of time (O(N)) and space (O(1)) complexity for the given problem constraints. There isn't a significantly faster or more space-efficient algorithmic approach.</li>
<li><strong>Alternative Condition Phrasing (Less Concise):</strong> One could write the condition more explicitly like:<pre><code class="language-python">if j &lt; 2: # Always allow the first two elements
    nums[j] = nums[i]
    j += 1
elif nums[i] != nums[j-2]: # Allow if current element is different from the element 2 positions back
    nums[j] = nums[i]
    j += 1
</code></pre>
However, the original <code>j &lt; 2 or nums[i] != nums[j-2]</code> is logically equivalent and more concise.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The code is highly performant. It processes the input array in a single pass with minimal overhead. There are no bottlenecks or inefficient operations.</li>
<li><strong>Security:</strong> As a pure algorithmic function operating on an integer list, there are no direct security implications such as input validation vulnerabilities, injection attacks, or resource exhaustion. The code does not interact with external systems, files, or sensitive data.</li>
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

        # j is the pointer for the modified array (where to write the next valid element)
        # i is the pointer for iterating through the original array
        j = 0

        for i in range(len(nums)):
            if j < 2 or nums[i] != nums[j-2]:
                nums[j] = nums[i]
                j += 1
        
        return j
```
