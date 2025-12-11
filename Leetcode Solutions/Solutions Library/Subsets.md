## Subsets
**Language:** python
**Tags:** subsets,power set,iterative algorithm,list manipulation

### Description:
<p>This code generates all possible subsets (also known as the power set) of a given set of distinct integers.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code implements an algorithm to find all unique subsets of a list of integers <code>nums</code>. The intent is to produce a list where each element is itself a list representing a distinct subset of the original <code>nums</code>. The problem assumes <code>nums</code> contains distinct integers.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses an iterative approach:</p>
<ol>
<li><strong>Initialization</strong>: It starts with <code>result</code> containing only an empty list <code>[[]]</code>, representing the empty set, which is always a subset.</li>
<li><strong>Iteration over numbers</strong>: It iterates through each <code>num</code> in the input <code>nums</code> list.</li>
<li><strong>Generating new subsets</strong>: For each <code>num</code>, it considers all existing <code>subset</code>s currently in <code>result</code>.<ul>
<li>For every <code>subset</code>, it creates a <em>new</em> subset by appending <code>num</code> to it (<code>subset + [num]</code>).</li>
<li>These newly formed subsets are temporarily stored in <code>current_level_subsets</code>.</li>
</ul>
</li>
<li><strong>Accumulation</strong>: After processing one <code>num</code>, all the subsets generated in that step (<code>current_level_subsets</code>) are added to the main <code>result</code> list using <code>extend()</code>.</li>
<li><strong>Final Result</strong>: After iterating through all numbers in <code>nums</code>, <code>result</code> will contain all possible subsets, which is then returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Iterative Approach</strong>: Instead of a recursive backtracking approach (which is common for this problem), this solution builds the power set iteratively. Each number <code>num</code> in <code>nums</code> effectively doubles the number of subsets by creating a "new set" of subsets that include <code>num</code>.</li>
<li><strong>List Concatenation for New Subsets</strong>: <code>subset + [num]</code> creates a brand new list. This is a crucial design choice as it prevents modification of existing subsets within <code>result</code> and ensures that each <code>subset</code> is treated as an immutable entity for the purpose of creating new ones.</li>
<li><strong>In-place Expansion of <code>result</code></strong>: The <code>result</code> list dynamically grows as new subsets are discovered. This means <code>result</code> always holds all subsets found <em>so far</em> based on the elements processed.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: <code>O(N * 2^N)</code></strong></p>
<ul>
<li>The outer loop runs <code>N</code> times (where <code>N</code> is the number of elements in <code>nums</code>).</li>
<li>In each iteration <code>i</code> of the outer loop (processing <code>nums[i]</code>), <code>result</code> contains <code>2^i</code> subsets.</li>
<li>The inner loop iterates <code>2^i</code> times.</li>
<li>Inside the inner loop, <code>subset + [num]</code> involves creating a new list by copying <code>subset</code> and appending an element. If <code>subset</code> has <code>k</code> elements, this takes <code>O(k)</code> time. The average length of subsets at step <code>i</code> is roughly <code>i/2</code>.</li>
<li>The total number of elements across all <code>2^N</code> subsets is <code>N * 2^(N-1)</code>.</li>
<li>Therefore, the total time spent in list concatenations and appending elements dominates and amounts to <code>O(N * 2^N)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity: <code>O(N * 2^N)</code></strong></p>
<ul>
<li>The <code>result</code> list ultimately stores <code>2^N</code> subsets.</li>
<li>The total number of integers stored across all these subsets is <code>N * 2^(N-1)</code>.</li>
<li>The <code>current_level_subsets</code> list temporarily holds <code>2^i</code> subsets, each up to <code>i</code> elements long. At its maximum, this temporary list can also contribute <code>O(N * 2^N)</code> in the worst case (if <code>N</code> is large and <code>i</code> is close to <code>N</code>).</li>
<li>Thus, the space complexity is dominated by the need to store all the generated subsets.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Input <code>nums = []</code></strong>:<ul>
<li><code>result</code> starts as <code>[[]]</code>.</li>
<li>The <code>for num in nums:</code> loop does not execute.</li>
<li>Returns <code>[[]]</code>. This is correct, as the empty set has one subset: itself.</li>
</ul>
</li>
<li><strong>Single Element Input <code>nums = [1]</code></strong>:<ul>
<li><code>result = [[]]</code></li>
<li><code>num = 1</code>:<ul>
<li><code>current_level_subsets = []</code></li>
<li><code>subset = []</code>: <code>current_level_subsets.append([] + [1])</code> -&gt; <code>[[1]]</code></li>
<li><code>result.extend([[1]])</code> -&gt; <code>result</code> becomes <code>[[], [1]]</code></li>
</ul>
</li>
<li>Returns <code>[[], [1]]</code>. Correct.</li>
</ul>
</li>
<li><strong>Distinct Integers</strong>: The algorithm implicitly assumes <code>nums</code> contains distinct integers. If <code>nums = [1, 1]</code>, it would produce <code>[[], [1], [1], [1, 1]]</code>. While technically representing all combinations, for "unique subsets," typical problems would expect <code>[[], [1], [1, 1]]</code> (requiring additional logic or a different approach). Given the standard "subsets" problem, distinct integers are usually implied.</li>
<li><strong>Correctness by Induction</strong>: The algorithm's correctness can be proven by induction.<ul>
<li>Base case: For <code>N=0</code> (empty <code>nums</code>), <code>result = [[]]</code> is correct.</li>
<li>Inductive step: Assume <code>result</code> correctly holds all <code>2^k</code> subsets for the first <code>k</code> elements of <code>nums</code>. When the <code>(k+1)</code>-th element <code>num</code> is processed, for each of the existing <code>2^k</code> subsets, a new one is formed by adding <code>num</code>. This effectively doubles the number of subsets from <code>2^k</code> to <code>2^k + 2^k = 2^(k+1)</code>, correctly generating all subsets for the first <code>k+1</code> elements.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Readability</strong>: The variable names are clear and descriptive. The code is already quite readable for its purpose.</p>
</li>
<li><p><strong>Alternative: Backtracking/Recursion</strong>:</p>
<pre><code class="language-python">def subsets_recursive(self, nums):
    result = []
    subset = []

    def backtrack(start):
        result.append(list(subset)) # Add a copy of the current subset

        for i in range(start, len(nums)):
            subset.append(nums[i])      # Include nums[i]
            backtrack(i + 1)            # Explore further subsets
            subset.pop()                # Backtrack: remove nums[i]

    backtrack(0)
    return result
</code></pre>
<p>This recursive approach is often considered more idiomatic for combinatorial problems. It builds a single <code>subset</code> list and makes a copy only when a valid subset is complete. The Big-O complexity remains the same, but constant factors might differ due to how lists are managed.</p>
</li>
<li><p><strong>Alternative: Bit Manipulation</strong>:</p>
<pre><code class="language-python">def subsets_bit_manipulation(self, nums):
    n = len(nums)
    result = []
    for i in range(1 &lt;&lt; n): # Iterate from 0 to 2^n - 1
        subset = []
        for j in range(n):
            if (i &gt;&gt; j) &amp; 1: # Check if j-th bit is set in i
                subset.append(nums[j])
        result.append(subset)
    return result
</code></pre>
<p>This approach maps each integer from <code>0</code> to <code>2^N - 1</code> to a unique subset. The binary representation of the integer <code>i</code> indicates which elements from <code>nums</code> are included in the subset. This can be very concise and efficient.</p>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(N * 2^N)</code> time and space complexity is optimal because there are <code>2^N</code> subsets, and their total size (sum of lengths) is <code>N * 2^(N-1)</code>. Any algorithm generating all of them must at least perform operations proportional to this output size. Python's list concatenations (<code>+</code>) create new lists, which might involve copying memory. While effective, for very large <code>N</code>, this could be a bottleneck compared to in-place modifications if not handled carefully, but the <code>O(N * 2^N)</code> complexity holds true regardless.</li>
<li><strong>Security</strong>: No direct security vulnerabilities are present in this code. It simply processes a list of integers and produces another list of lists.</li>
</ul>


### Code:
```python
class Solution(object):
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        result = [[]] # Start with the empty set

        for num in nums:
            current_level_subsets = []
            for subset in result:
                current_level_subsets.append(subset + [num])
            result.extend(current_level_subsets)
            
        return result
```
