## Permutations
**Language:** python
**Tags:** python,backtracking,permutation,recursion

### Description:
<p> This code demonstrates a classic backtracking algorithm.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to generate all possible permutations of a given list of integers, <code>nums</code>. A permutation is an arrangement of all elements in a specific order. For example, the permutations of <code>[1, 2]</code> are <code>[1, 2]</code> and <code>[2, 1]</code>.</p>
<p>The code implements a recursive backtracking approach to systematically explore all possible orderings of the input elements.</p>
<h3>2. How It Works</h3>
<p>The core of the solution is the <code>backtrack_with_used</code> recursive helper function:</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li>An empty list <code>results</code> is initialized to store all complete permutations.</li>
<li>A boolean list <code>used</code> of the same length as <code>nums</code> is created, initialized to <code>False</code>. This <code>used</code> array acts as a tracker, indicating whether an element at a particular index from the original <code>nums</code> list has already been included in the <code>current_permutation</code>.</li>
</ul>
</li>
<li><p><strong>Base Case</strong>:</p>
<ul>
<li>If the <code>current_permutation</code> (the list being built up) has a length equal to <code>n</code> (the length of the original <code>nums</code> list), it means a complete permutation has been formed.</li>
<li>A <em>copy</em> of <code>current_permutation</code> is appended to <code>results</code>, and the function returns. The copy is crucial to ensure that subsequent backtracking steps don't modify the permutation already added to <code>results</code>.</li>
</ul>
</li>
<li><p><strong>Recursive Step (Choose, Explore, Unchoose)</strong>:</p>
<ul>
<li>The function iterates through each element of the original <code>nums</code> list using a <code>for</code> loop from <code>i = 0</code> to <code>n-1</code>.</li>
<li><strong>Choose</strong>: For each <code>i</code>, it checks <code>if not used[i]</code>. If the element at <code>nums[i]</code> has not yet been used in the <code>current_permutation</code>:<ul>
<li>It appends <code>nums[i]</code> to <code>current_permutation</code>.</li>
<li>It marks <code>used[i]</code> as <code>True</code>.</li>
</ul>
</li>
<li><strong>Explore</strong>: It then makes a recursive call to <code>backtrack_with_used(current_permutation)</code>. This call attempts to build the next level of the permutation by adding another element.</li>
<li><strong>Unchoose (Backtrack)</strong>: After the recursive call returns (meaning all permutations stemming from the current <code>current_permutation</code> have been explored), the code "undoes" its choices:<ul>
<li>It sets <code>used[i]</code> back to <code>False</code>.</li>
<li>It removes the last element from <code>current_permutation</code> using <code>pop()</code>. This allows the loop to try other elements at the current position.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Invocation</strong>: The process starts by calling <code>backtrack_with_used([])</code> with an empty initial permutation.</p>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm</strong>: <strong>Backtracking</strong> is a natural fit for permutation problems. It systematically explores all possible paths (element choices) and prunes invalid ones (already used elements).</li>
<li><strong>Data Structures</strong>:<ul>
<li><code>results</code> (List of Lists): Stores all generated permutations. This is a straightforward way to collect the final output.</li>
<li><code>current_permutation</code> (List): Used to build a single permutation during the recursive calls. Python lists are efficient for <code>append</code> and <code>pop</code> operations (amortized O(1)).</li>
<li><code>used</code> (List of Booleans): This is a crucial optimization. Instead of creating new lists of <code>remaining_elements</code> in each recursive call (which would involve copying), <code>used</code> efficiently tracks which original elements are available. This makes element selection <code>O(1)</code> and avoids redundant memory allocations for <code>remaining_elements</code> lists.</li>
</ul>
</li>
<li><strong>Deep Copying</strong>: Using <code>list(current_permutation)</code> when adding to <code>results</code> prevents <code>results</code> from containing references to the same <code>current_permutation</code> object, which would be empty by the time the function finishes.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: O(N * N!)</strong></p>
<ul>
<li>There are <code>N!</code> (N factorial) unique permutations for a list of <code>N</code> distinct elements.</li>
<li>For each permutation, the base case involves creating a copy of <code>current_permutation</code> of length <code>N</code>, which takes <code>O(N)</code> time.</li>
<li>Each level of recursion (up to <code>N</code> levels deep) involves a loop of <code>N</code> iterations. Inside the loop, operations like <code>append</code>, <code>pop</code>, and array access are <code>O(1)</code>.</li>
<li>Therefore, the total time complexity is proportional to <code>N</code> (for copying) multiplied by <code>N!</code> (the number of permutations), resulting in <code>O(N * N!)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity: O(N * N!)</strong></p>
<ul>
<li><code>results</code>: Stores <code>N!</code> permutations, each of length <code>N</code>. This dominates the space complexity, requiring <code>O(N * N!)</code> space.</li>
<li><code>current_permutation</code>: The maximum depth of the recursion stack is <code>N</code>. At each level, <code>current_permutation</code> holds up to <code>N</code> elements. So <code>O(N)</code> space.</li>
<li><code>used</code>: A boolean array of size <code>N</code>, so <code>O(N)</code> space.</li>
<li>Recursion Stack: The depth of the recursion is <code>N</code>, so <code>O(N)</code> space for stack frames.</li>
<li>The total space complexity is thus dominated by the storage of results: <code>O(N * N!)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty list (<code>nums = []</code>)</strong>:<ul>
<li><code>n</code> will be 0.</li>
<li><code>backtrack_with_used([])</code> is called.</li>
<li>The base case <code>len(current_permutation) == n</code> (0 == 0) is met immediately.</li>
<li><code>results.append(list([]))</code> adds an empty list to <code>results</code>.</li>
<li>Correctly returns <code>[[]]</code>, representing the single "empty permutation".</li>
</ul>
</li>
<li><strong>Single element list (<code>nums = [1]</code>)</strong>:<ul>
<li><code>n</code> will be 1.</li>
<li>The algorithm correctly identifies <code>[1]</code> as the only permutation.</li>
</ul>
</li>
<li><strong>List with distinct elements (e.g., <code>[1, 2, 3]</code>)</strong>: The algorithm correctly generates all 6 unique permutations.</li>
<li><strong>List with duplicate elements (e.g., <code>[1, 1, 2]</code>)</strong>:<ul>
<li>The current code will generate <em>all</em> permutations, treating the duplicate <code>1</code>s as distinct entities (e.g., <code>[1_a, 1_b, 2]</code>, <code>[1_b, 1_a, 2]</code>). This will result in duplicate permutations in the <code>results</code> list (e.g., <code>[[1,1,2], [1,1,2], [1,2,1], [1,2,1], [2,1,1], [2,1,1]]</code>).</li>
<li><strong>Correctness</strong>: If the problem statement implies "all permutations" even if some are identical due to repeated input elements, then it is correct. If the problem expects "all <em>unique</em> permutations" when duplicates are present in the input, then this code would need modification (e.g., sorting <code>nums</code> and adding a check to skip identical adjacent elements in the loop, or using a <code>set</code> for <code>results</code>). For the typical LeetCode "Permutations" problem (LC 46), input <code>nums</code> are guaranteed unique, so this is not an issue.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The internal commented-out sections discussing alternative backtracking signatures are helpful for understanding the thought process but should be removed from production code to improve clarity. The final <code>backtrack_with_used</code> implementation is clean and idiomatic.</li>
<li><strong>Efficiency (Minor)</strong>: For very deep recursion, Python's recursion limit might be hit (default 1000). For typical permutation problems, <code>N</code> is small (<code>&lt;15</code>) due to the factorial complexity, so this is rarely an issue.</li>
<li><strong>Alternative: In-place Swapping</strong>:
An alternative backtracking strategy involves performing swaps directly on the <code>nums</code> array (or a copy) to generate permutations. This can sometimes be marginally more memory-efficient as it avoids building <code>current_permutation</code> explicitly on each stack frame and doesn't require a <code>used</code> array.<pre><code class="language-python">def permute_in_place(nums):
    results = []
    n = len(nums)

    def backtrack_swap(index):
        if index == n:
            results.append(list(nums)) # Deep copy the current state of nums
            return

        for i in range(index, n):
            nums[index], nums[i] = nums[i], nums[index] # Choose (swap)
            backtrack_swap(index + 1)                   # Explore
            nums[index], nums[i] = nums[i], nums[index] # Unchoose (swap back)

    backtrack_swap(0)
    return results
</code></pre>
This approach directly modifies the <code>nums</code> list and then restores it, effectively achieving the same "choose" and "unchoose" behavior. It still requires copying <code>nums</code> (O(N)) at the base case.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck</strong>: The inherent factorial complexity <code>O(N!)</code> means this algorithm is only practical for small values of <code>N</code> (typically <code>N &lt;= 12-15</code>). For larger <code>N</code>, the number of permutations becomes astronomically large, making any permutation generation algorithm intractable. This is a mathematical reality, not a flaw in the code's implementation efficiency for the problem.</li>
<li><strong>Security</strong>: There are no direct security implications as this code performs purely computational logic on a given list of integers without external interactions, sensitive data handling, or resource access.</li>
</ul>


### Code:
```python
class Solution(object):
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        results = []
        n = len(nums)

        def backtrack(current_permutation, remaining_elements):
            if len(current_permutation) == n:
                results.append(list(current_permutation))
                return

            # Iterate over a copy of remaining_elements to avoid issues with modification during iteration
            # Or, more efficiently, use an index for remaining_elements and build it dynamically.
            # A common way is to pass a 'used' array or swap elements.
            # Let's stick to building remaining_elements dynamically for clarity in this version.
            
            # A more typical backtracking approach uses a 'used' set or list of booleans
            # to track which elements from the original 'nums' have been added.
            # Let's re-implement with 'used' array for better performance and standard practice.

            # Re-thinking the backtrack signature for efficiency and standard approach:
            # backtrack(index, current_permutation)
            # where 'index' indicates the position we are currently filling in current_permutation
            # and we iterate through 'nums' using a 'used' array.

        # New backtracking implementation using 'used' array
        used = [False] * n
        
        def backtrack_with_used(current_permutation):
            if len(current_permutation) == n:
                results.append(list(current_permutation))
                return

            for i in range(n):
                if not used[i]:
                    # Choose
                    current_permutation.append(nums[i])
                    used[i] = True
                    
                    # Explore
                    backtrack_with_used(current_permutation)
                    
                    # Unchoose (backtrack)
                    used[i] = False
                    current_permutation.pop()

        backtrack_with_used([])
        return results
```
