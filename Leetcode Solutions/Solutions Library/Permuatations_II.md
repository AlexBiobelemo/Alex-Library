## Permuatations II
**Language:** python
**Tags:** python,backtracking,permutations,recursion,uniqueness

### Description:
<p> This is a classic problem solved elegantly using backtracking.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem</strong>: The function <code>permuteUnique(self, nums)</code> aims to find all unique permutations of a given list of integers <code>nums</code>, which may contain duplicate numbers.</li>
<li><strong>Input</strong>: A list of integers (<code>nums</code>).</li>
<li><strong>Output</strong>: A list of lists, where each inner list represents a unique permutation of the input <code>nums</code>.</li>
<li><strong>Core Idea</strong>: The solution uses a backtracking algorithm combined with a clever pruning step to avoid generating duplicate permutations.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm works as follows:</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li>The input list <code>nums</code> is first <code>sort()</code>ed. This step is crucial for the duplicate-skipping logic.</li>
<li><code>n</code> stores the length of <code>nums</code>.</li>
<li><code>results</code> is an empty list to store all unique permutations found.</li>
<li><code>current_permutation</code> is an empty list used to build a permutation during the recursion.</li>
<li><code>visited</code> is a boolean array of length <code>n</code>, initialized to <code>False</code>, to keep track of which numbers in <code>nums</code> have been included in the <code>current_permutation</code> at any given point.</li>
</ul>
</li>
<li><p><strong><code>backtrack()</code> Function</strong>: This is a recursive helper function that explores all possible permutations.</p>
<ul>
<li><strong>Base Case</strong>: If the length of <code>current_permutation</code> equals <code>n</code>, it means a complete permutation has been formed. A <em>copy</em> of <code>current_permutation</code> is added to <code>results</code>, and the function returns.</li>
<li><strong>Recursive Step</strong>: The function iterates through <code>nums</code> using an index <code>i</code> from <code>0</code> to <code>n-1</code>.<ul>
<li><strong>Skip Visited Elements</strong>: If <code>nums[i]</code> has already been visited (i.e., <code>visited[i]</code> is <code>True</code>), it skips this element as it's already part of the <code>current_permutation</code> branch.</li>
<li><strong>Skip Duplicate Elements</strong>: This is the core logic for uniqueness:
<code>if i &gt; 0 and nums[i] == nums[i-1] and not visited[i-1]: continue</code><ul>
<li>This condition ensures that if the current number (<code>nums[i]</code>) is the same as the previous number (<code>nums[i-1]</code>), and the <em>previous</em> number (<code>nums[i-1]</code>) was <em>not</em> visited (meaning it was available but we chose <em>not</em> to use it in a previous recursive call or it was already processed in a different branch of the same level), then we should skip <code>nums[i]</code>. This prevents generating permutations that are identical due to swapping identical numbers (e.g., for <code>[1,1,2]</code>, it avoids generating <code>[1_a, 1_b, 2]</code> and then <code>[1_b, 1_a, 2]</code>).</li>
</ul>
</li>
<li><strong>Choose</strong>: If <code>nums[i]</code> is suitable (not visited and not skipped due to duplicate logic), it's "chosen":<ul>
<li><code>visited[i]</code> is set to <code>True</code>.</li>
<li><code>nums[i]</code> is appended to <code>current_permutation</code>.</li>
</ul>
</li>
<li><strong>Explore</strong>: Recursively call <code>backtrack()</code> to build the rest of the permutation.</li>
<li><strong>Unchoose (Backtrack)</strong>: After the recursive call returns, the chosen element is "unchosen" to explore other possibilities:<ul>
<li><code>current_permutation.pop()</code>.</li>
<li><code>visited[i]</code> is set back to <code>False</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Initiate &amp; Return</strong>: The main <code>permuteUnique</code> function calls <code>backtrack()</code> once and then returns the <code>results</code> list.</p>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Backtracking Algorithm</strong>: A standard and effective approach for permutation and combination problems, allowing systematic exploration of all possibilities.</li>
<li><strong>Sorting <code>nums</code></strong>: Absolutely critical. It groups identical elements together, making the duplicate-skipping condition <code>nums[i] == nums[i-1]</code> reliable. Without sorting, <code>nums[i-1]</code> would not necessarily be the "previous identical element."</li>
<li><strong><code>visited</code> Array</strong>: Essential for ensuring that each number in <code>nums</code> is used at most once within a single permutation.</li>
<li><strong><code>current_permutation</code> as a List</strong>: Provides efficient <code>append()</code> and <code>pop()</code> operations (amortized O(1)) for building and undoing permutations.</li>
<li><strong>Appending a Copy (<code>list(current_permutation)</code>)</strong>: When a complete permutation is found, a new list object is created (<code>list(current_permutation)</code>) before appending it to <code>results</code>. This is crucial because <code>current_permutation</code> is modified later, and without a copy, all entries in <code>results</code> would end up pointing to the <em>same</em> mutable list object, which would reflect its final state (an empty list).</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><strong>Sorting</strong>: <code>O(N log N)</code> due to <code>nums.sort()</code>.</li>
<li><strong>Backtracking</strong>: In the worst case (all elements are distinct), this would explore <code>N!</code> permutations. For each permutation, building it and appending it takes <code>O(N)</code>. So, worst-case is <code>O(N * N!)</code>.</li>
<li>When duplicates exist, the pruning step reduces the number of explored paths. Let <code>P_unique</code> be the number of unique permutations. The complexity is <code>O(N log N + N * P_unique)</code>. In the absolute worst case (<code>P_unique</code> can be <code>N!</code>), it's <code>O(N * N!)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><strong><code>results</code></strong>: Stores <code>P_unique</code> permutations, each of length <code>N</code>. So, <code>O(N * P_unique)</code>.</li>
<li><strong><code>current_permutation</code></strong>: <code>O(N)</code> for the maximum recursion depth.</li>
<li><strong><code>visited</code></strong>: <code>O(N)</code>.</li>
<li><strong>Recursion Stack</strong>: <code>O(N)</code> for the maximum depth of the recursion.</li>
<li>Total Space: <code>O(N * P_unique)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty list (<code>[]</code>)</strong>:<ul>
<li><code>n=0</code>. <code>backtrack()</code> is called. <code>len(current_permutation)</code> (0) is equal to <code>n</code> (0) immediately. <code>results.append(list([]))</code> will add an empty list. The final <code>results</code> would be <code>[[]]</code>. The problem usually implies non-empty lists, or expects <code>[]</code> for an empty input. If the expected output for <code>[]</code> is <code>[]</code>, the base case might need an adjustment or a check for <code>n==0</code> at the start. <em>Correction</em>: For LeetCode, <code>[[]]</code> is generally the correct output for an empty input when asking for combinations/permutations, as there is one "empty" way to permute an empty list.</li>
</ul>
</li>
<li><strong>List with one element (<code>[1]</code>)</strong>:<ul>
<li><code>n=1</code>. <code>nums=[1]</code>.</li>
<li><code>backtrack()</code> -&gt; <code>i=0</code>. <code>current_permutation=[1]</code>. <code>visited=[True]</code>.</li>
<li><code>backtrack()</code> -&gt; Base case hit. <code>results.append([1])</code>.</li>
<li>Correctly returns <code>[[1]]</code>.</li>
</ul>
</li>
<li><strong>List with all identical elements (<code>[1,1,1]</code>)</strong>:<ul>
<li><code>n=3</code>. <code>nums=[1,1,1]</code>.</li>
<li>The duplicate skipping logic <code>not visited[i-1]</code> is key.</li>
<li>First branch: <code>current_permutation=[1]</code> (using <code>nums[0]</code>).</li>
<li>Second branch: <code>current_permutation=[1,1]</code> (using <code>nums[1]</code>). <code>nums[1]==nums[0]</code> is True. <code>visited[0]</code> is True, so <code>not visited[0]</code> is False. The skip condition is False, so <code>nums[1]</code> is picked.</li>
<li>Third branch: <code>current_permutation=[1,1,1]</code> (using <code>nums[2]</code>). Same logic.</li>
<li><code>results.append([1,1,1])</code>.</li>
<li>When backtracking to the outer loop, if <code>nums[1]</code> (the second <code>1</code>) is considered <em>again</em> at the <code>i=1</code> position, the condition <code>i &gt; 0 and nums[i] == nums[i-1] and not visited[i-1]</code> will be met (<code>nums[1]==nums[0]</code> and <code>visited[0]</code> would be <code>False</code> because we backtracked from it). This correctly skips the redundant path.</li>
<li>Correctly returns <code>[[1,1,1]]</code>.</li>
</ul>
</li>
<li><strong>General correctness</strong>: The sorting ensures identical elements are adjacent. The <code>visited</code> array ensures no element is used more than once in a single permutation. The duplicate skipping condition <code>not visited[i-1]</code> ensures that if we have <code>[1_a, 1_b]</code>, we don't start a branch with <code>1_b</code> if <code>1_a</code> was available and unvisited at the same level of the recursion tree, as this would lead to a structurally identical permutation.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability of Duplicate Condition</strong>: While standard for this problem, the duplicate skipping condition (<code>if i &gt; 0 and nums[i] == nums[i-1] and not visited[i-1]: continue</code>) can be tricky to grasp initially. A more verbose comment or breaking it down into named boolean variables could slightly improve immediate readability for novices, but it's well-established for this algorithm.</li>
<li><strong>Iterative Solution</strong>: While recursive solutions are often more intuitive for backtracking, an iterative solution using a stack can avoid Python's recursion depth limit for extremely large <code>N</code> (though <code>N</code> for permutations problems is typically small, like &lt; 15 due to <code>N!</code> growth). This would be significantly more complex to implement.</li>
<li><strong>Python's <code>itertools</code> Module</strong>: For generating permutations, <code>itertools.permutations</code> is a built-in, highly optimized solution. To get <em>unique</em> permutations, one would typically:<pre><code class="language-python">import itertools
class Solution:
    def permuteUnique(self, nums):
        # Generate all permutations (including duplicates)
        all_permutations = itertools.permutations(nums)
        # Use a set to store unique permutations (tuples are hashable)
        unique_permutations_set = set(all_permutations)
        # Convert back to list of lists
        return [list(p) for p in unique_permutations_set]
</code></pre>
This <code>itertools</code> approach is often simpler to write but can be less performant than the direct backtracking approach for this problem if there are many duplicates, as it generates <em>all</em> <code>N!</code> permutations first and then filters, rather than pruning the search space upfront. Its time complexity would also involve <code>O(N * N!)</code> (to generate and convert to tuples) and then <code>O(N * N!)</code> for set insertion in the worst case.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The current backtracking solution is generally the most efficient for generating <em>unique</em> permutations directly, as it prunes the search space. Python's recursion overhead is usually acceptable for typical competitive programming constraints (N usually &lt;= 10-12). Memory usage scales with the number of unique permutations, which can be substantial (<code>O(N * N!)</code>).</li>
<li><strong>Security</strong>: This code presents no direct security vulnerabilities as it's a purely algorithmic problem dealing with integer lists and not external input or system resources.</li>
</ul>


### Code:
```python
class Solution(object):
    def permuteUnique(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums.sort()
        n = len(nums)
        results = []
        current_permutation = []
        visited = [False] * n

        def backtrack():
            if len(current_permutation) == n:
                results.append(list(current_permutation))
                return

            for i in range(n):
                if visited[i]:
                    continue
                
          
                if i > 0 and nums[i] == nums[i-1] and not visited[i-1]:
                    continue

                visited[i] = True
                current_permutation.append(nums[i])
                backtrack()
                current_permutation.pop()
                visited[i] = False

        backtrack()
        return results
```
