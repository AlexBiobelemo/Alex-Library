## First Missing Positive
**Language:** python
**Tags:** array,in-place,missing-number,cyclic-sort

### Description:
<p>This code addresses the classic problem of finding the smallest missing positive integer in an unsorted array, using an ingenious in-place modification strategy.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to find the smallest positive integer (i.e., 1, 2, 3, ...) that is <em>not</em> present in the input array <code>nums</code>. The solution is designed to operate with optimal time complexity (linear time) and optimal space complexity (constant extra space).</p>
<h3>2. How It Works</h3>
<p>The solution employs a two-phase approach that modifies the input array in place:</p>
<ul>
<li><p><strong>Phase 1: Cyclic Sort / Placement</strong></p>
<ul>
<li>The code iterates through the array from left to right using an index <code>i</code>.</li>
<li>For each <code>nums[i]</code>, it enters an inner <code>while</code> loop. This loop attempts to place <code>nums[i]</code> into its "correct" position.</li>
<li>A number <code>k</code> is considered to be in its correct position if it is located at index <code>k-1</code>.</li>
<li>The <code>while</code> loop continues as long as three conditions are met for <code>nums[i]</code>:<ol>
<li>It is a positive number (<code>1 &lt;= nums[i]</code>).</li>
<li>It is within the valid range of indices for the current array size <code>n</code> (<code>nums[i] &lt;= n</code>).</li>
<li>It is <em>not</em> already in its correct position (i.e., <code>nums[nums[i]-1] != nums[i]</code>). This condition also handles duplicates effectively; if <code>nums[i]</code> is <code>k</code> and <code>nums[k-1]</code> is <em>also</em> <code>k</code>, then <code>k</code> is already "accounted for" at its correct spot.</li>
</ol>
</li>
<li>If these conditions hold, <code>nums[i]</code> is swapped with the element currently at <code>nums[i]-1</code>. This moves <code>nums[i]</code> closer to its correct spot. The <code>while</code> loop then re-evaluates the <em>new</em> <code>nums[i]</code> (the element that was just swapped <em>into</em> the current position <code>i</code>), ensuring it also gets a chance to be placed correctly.</li>
<li>Numbers that are negative, zero, or greater than <code>n</code> are ignored in this phase as they cannot be the "first missing positive" within the range <code>[1, n]</code> and do not have a corresponding valid index <code>k-1</code>.</li>
</ul>
</li>
<li><p><strong>Phase 2: Verification</strong></p>
<ul>
<li>After Phase 1, if a positive integer <code>k</code> (where <code>1 &lt;= k &lt;= n</code>) is present in the array, it will ideally be at index <code>k-1</code>.</li>
<li>The code then iterates through the modified array once more.</li>
<li>It checks if <code>nums[i]</code> is equal to <code>i + 1</code>.</li>
<li>The first time this condition <code>nums[i] != i + 1</code> is true, it means that <code>i + 1</code> is the smallest positive integer missing from the array, so <code>i + 1</code> is returned.</li>
<li>If the loop completes without finding such a mismatch, it implies that all integers from <code>1</code> to <code>n</code> are present in their correct positions. In this case, the smallest missing positive integer must be <code>n + 1</code>.</li>
</ul>
</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>In-place Modification (O(1) Space):</strong> The most critical decision is to use the input array itself as a sort of hash map or presence tracker. By placing <code>k</code> at index <code>k-1</code>, we mark its presence without using additional memory. This is the cornerstone for achieving constant space complexity.</li>
<li><strong>Cyclic Sort Algorithm:</strong> The repeated swapping in the <code>while</code> loop is a form of cyclic sort. It ensures that numbers are moved to their correct positions efficiently. Each number (from 1 to <code>n</code>) is targeted to be placed at <code>index = value - 1</code>.</li>
<li><strong>Ignoring Out-of-Range/Irrelevant Numbers:</strong> The conditions <code>1 &lt;= nums[i] &lt;= n</code> are crucial.<ul>
<li>Numbers less than 1 (negatives, zero) are irrelevant because we are looking for positive integers.</li>
<li>Numbers greater than <code>n</code> are also irrelevant because if all numbers from <code>1</code> to <code>n</code> are present, the answer would be <code>n+1</code>. If any number from <code>1</code> to <code>n</code> is missing, then numbers greater than <code>n</code> don't change that. These irrelevant numbers are left in their original (or swapped) positions, as they don't hinder finding the missing positive within <code>[1, n]</code>.</li>
</ul>
</li>
<li><strong>Duplicate Handling:</strong> The condition <code>nums[nums[i]-1] != nums[i]</code> is vital for handling duplicates and preventing infinite loops. If <code>nums[i]</code> is <code>k</code> and <code>nums[k-1]</code> is <em>also</em> <code>k</code>, it means <code>k</code> is already where it should be, and this <code>nums[i]</code> is a redundant copy. The <code>while</code> loop correctly terminates in such cases.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(n)</strong><ul>
<li>The outer <code>for</code> loop iterates <code>n</code> times.</li>
<li>The inner <code>while</code> loop: While it looks like it could lead to O(n^2), each number from <code>1</code> to <code>n</code> is involved in a swap <em>into its correct position</em> at most once. An element might be moved <em>out</em> of <code>nums[i]</code> multiple times, but each such swap either places a number correctly or moves an out-of-range number. The total number of swaps and comparisons across all iterations of the <code>while</code> loops is proportional to <code>n</code>, as each successful placement decreases the "disorder" that the loop addresses.</li>
<li>The second <code>for</code> loop (verification) iterates <code>n</code> times.</li>
<li>Therefore, the total time complexity is O(n).</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm modifies the input array in place and uses only a few constant-space variables (<code>n</code>, <code>i</code>, <code>correct_index</code>). No auxiliary data structures are allocated that scale with the input size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles several edge cases correctly:</p>
<ul>
<li><strong>Empty array (<code>[]</code>):</strong> <code>n</code> becomes 0. Both <code>for</code> loops are skipped. It returns <code>n + 1</code> which is <code>1</code>. Correct.</li>
<li><strong>Array with only negative numbers/zeros (<code>[-5, 0, -10]</code>):</strong> <code>n=3</code>. Phase 1 does nothing as no <code>nums[i]</code> satisfies <code>1 &lt;= nums[i] &lt;= n</code>. Phase 2: <code>nums[0]</code> is <code>-5</code> (not <code>1</code>), returns <code>0+1=1</code>. Correct.</li>
<li><strong>Array with positive numbers, but missing <code>1</code> (<code>[2, 3, 4]</code>):</strong> <code>n=3</code>. <code>i=0, nums[0]=2</code>. Swap <code>nums[0]</code> and <code>nums[1]</code>. <code>[3, 2, 4]</code>. Now <code>nums[0]=3</code>. Swap <code>nums[0]</code> and <code>nums[2]</code>. <code>[4, 2, 3]</code>. Now <code>nums[0]=4</code>. <code>4 &gt; n</code> so loop exits. <code>i=1, nums[1]=2</code>. <code>[4, 2, 3]</code>. <code>nums[1]</code> is <code>2</code>. <code>nums[1]</code> is already correct. <code>i=2, nums[2]=3</code>. <code>nums[2]</code> is already correct. Array becomes <code>[4, 2, 3]</code>. Phase 2: <code>nums[0]=4</code> not <code>1</code>. Returns <code>0+1=1</code>. Correct.</li>
<li><strong>Array with all positives up to <code>n</code> (<code>[1, 2, 3]</code>):</strong> <code>n=3</code>. Phase 1 places all correctly. Phase 2 iterates, finds all matches <code>nums[i] == i+1</code>. Returns <code>n+1 = 4</code>. Correct.</li>
<li><strong>Array with duplicates (<code>[1, 1, 2]</code>):</strong> <code>n=3</code>. <code>i=0, nums[0]=1</code>. Already <code>nums[0] == 1</code>. Loop terminates. <code>i=1, nums[1]=1</code>. <code>nums[0]</code> is <code>1</code>. Condition <code>nums[nums[1]-1] != nums[1]</code> (i.e. <code>nums[0] != nums[1]</code>) is <code>1 != 1</code>, which is false. Loop terminates. <code>i=2, nums[2]=2</code>. Swap <code>nums[2]</code> and <code>nums[1]</code>. <code>[1, 2, 1]</code>. Loop terminates for <code>nums[2]</code> as now <code>nums[2]</code> is <code>1</code> and condition <code>1 &lt;= 1 &lt;= 3</code> but <code>nums[0]</code> is <code>1</code> so <code>nums[0] != nums[2]</code> is false. Phase 2: <code>nums[0]=1</code> is correct. <code>nums[1]=2</code> is correct. <code>nums[2]=1</code> is not <code>3</code>. Returns <code>2+1=3</code>. Correct. The first <code>1</code> is placed, the second <code>1</code> is left wherever it lands (in this case, position 2), and doesn't interfere with finding <code>3</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is quite readable, with clear variable names and helpful comments explaining the complex <code>while</code> loop conditions. No major improvements needed here.</li>
<li><strong>Performance:</strong> For the given constraints (O(N) time, O(1) space), this solution is optimal. There are no obvious performance bottlenecks that could be improved while adhering to these constraints.</li>
<li><strong>Alternative Approaches (with different trade-offs):</strong><ul>
<li><strong>Using a Hash Set:</strong><ul>
<li>Add all positive numbers from <code>nums</code> into a <code>set</code>.</li>
<li>Then, iterate from <code>k = 1</code> upwards, checking <code>if k not in set</code>. The first <code>k</code> not found is the answer.</li>
<li><em>Trade-offs:</em> O(N) time, but O(N) space for the hash set. Simpler to understand.</li>
</ul>
</li>
<li><strong>Sorting the Array:</strong><ul>
<li>Sort <code>nums</code>.</li>
<li>Iterate through the sorted array, keeping track of the expected positive integer (starting from <code>1</code>).</li>
<li><em>Trade-offs:</em> O(N log N) time (for sorting), O(1) or O(N) space depending on the sort algorithm. Also simpler to understand but less performant.</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code operates purely on an in-memory list of integers and does not interact with external systems, files, or user input in a way that would introduce common security vulnerabilities (e.g., injection attacks, directory traversal). No specific security concerns are applicable.</li>
<li><strong>Performance:</strong> The solution is highly optimized for the specified constraints. The in-place modification and single-pass-like nature of the cyclic sort ensure minimal overhead. It avoids the overhead of hash table lookups (like a set-based approach) or the comparisons of sorting.</li>
</ul>


### Code:
```python
class Solution(object):
    def firstMissingPositive(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = len(nums)

        # Phase 1: Place numbers in their correct positions
        # Iterate through the array
        for i in range(n):
            """
            While the current number nums[i] is:
            1. Positive (1 to n)
            2. Within the valid range [1, n]
            3. Not already in its correct position (nums[nums[i]-1] != nums[i])
            """
            while 1 <= nums[i] <= n and nums[nums[i] - 1] != nums[i]:
                # The target index for nums[i] is nums[i] - 1
                correct_index = nums[i] - 1
                
                # Swap nums[i] with nums[correct_index]
                nums[i], nums[correct_index] = nums[correct_index], nums[i]
                """
                After the swap, the new nums[i] might also need to be placed,
                so the while loop continues with the new value at nums[i].
                If the new nums[i] is out of range or already in place,
                the loop condition will eventually become false.
                """
        # Phase 2: Find the first missing positive integer
        # Iterate through the (modified) array
        for i in range(n):
            # If nums[i] is not equal to i + 1, then i + 1 is the smallest missing positive
            if nums[i] != i + 1:
                return i + 1
        
        # If all numbers from 1 to n are present, then the smallest missing positive
        # is n + 1
        return n + 1
```
