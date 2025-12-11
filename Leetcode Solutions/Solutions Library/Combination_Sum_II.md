## Combination Sum II
**Language:** python
**Tags:** python,backtracking,recursion,combinations,duplicates

### Description:
<p>This code solves the "Combination Sum II" problem, a classic problem often encountered in technical interviews. It uses a recursive backtracking approach to find all unique combinations of numbers from a given list that sum up to a specific target. Each number in the <code>candidates</code> list can be used at most once in a combination, and the solution must not contain duplicate combinations (e.g., <code>[1,2]</code> and <code>[2,1]</code> are the same combination, and if <code>candidates</code> contains multiple <code>2</code>s, <code>[1,2_a]</code> and <code>[1,2_b]</code> should not both appear if <code>2_a</code> and <code>2_b</code> are meant to be indistinguishable).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to find all unique combinations of numbers from a list <code>candidates</code> that sum up to a given <code>target</code>. The key constraints are:</p>
<ul>
<li>Each number in <code>candidates</code> can be used only once per combination.</li>
<li>The <code>candidates</code> list may contain duplicate numbers.</li>
<li>The final list of combinations must not contain duplicate <em>combinations</em>.</li>
</ul>
<p>This implies that if <code>candidates = [1, 1, 2]</code> and <code>target = 2</code>, the only valid unique combination is <code>[1, 1]</code>.</p>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a <strong>backtracking</strong> algorithm, which is a common technique for exploring all possible combinations or permutations.</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li>An empty list <code>results</code> is created to store all valid combinations found.</li>
<li>The <code>candidates</code> list is <strong>sorted</strong> in ascending order. This step is crucial for two reasons:<ul>
<li>It enables efficient pruning of branches where the <code>current_sum</code> exceeds <code>target</code> (as larger numbers are encountered later).</li>
<li>More importantly, it allows for easy detection and skipping of duplicate numbers to ensure unique combinations.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong><code>backtrack</code> Function (Recursive Helper)</strong>:</p>
<ul>
<li><strong>Parameters</strong>:<ul>
<li><code>start_index</code>: An integer indicating the starting index for the current iteration within <code>candidates</code>. This ensures that numbers are not reused (by always starting from <code>i + 1</code> in the recursive call) and helps manage duplicate candidate elements.</li>
<li><code>current_sum</code>: The sum of numbers in the <code>current_combination</code> so far.</li>
<li><code>current_combination</code>: A list representing the combination being built in the current recursive path.</li>
</ul>
</li>
<li><strong>Base Cases</strong>:<ul>
<li>If <code>current_sum == target</code>: A valid combination has been found. A <em>copy</em> of <code>current_combination</code> is added to <code>results</code>, and the function returns.</li>
<li>If <code>current_sum &gt; target</code>: The current path has exceeded the target, so it's pruned. The function returns, as adding more positive numbers will only increase the sum further.</li>
</ul>
</li>
<li><strong>Recursive Step (Loop)</strong>:<ul>
<li>The function iterates through the <code>candidates</code> list, starting from <code>start_index</code>.</li>
<li><strong>Duplicate Handling</strong>: <code>if i &gt; start_index and candidates[i] == candidates[i-1]: continue</code><ul>
<li>This is the core logic for handling duplicates. If the current number <code>candidates[i]</code> is the same as the previous number <code>candidates[i-1]</code>, AND we are not at the very beginning of the current iteration (<code>i &gt; start_index</code>), then we skip <code>candidates[i]</code>. This ensures that, for a given recursive level, if we have choices like <code>[..., 2a, 2b, ...]</code> we only consider <code>2a</code> to start a path at this level, and <code>2b</code> won't initiate a <em>new</em> path from the same <code>start_index</code> if <code>2a</code> has already been processed. This prevents combinations like <code>[1, 2a, 5]</code> and <code>[1, 2b, 5]</code> from being generated if <code>2a</code> and <code>2b</code> are identical values.</li>
</ul>
</li>
<li><strong>Pruning</strong>: <code>if current_sum + num &lt;= target:</code><ul>
<li>This is a micro-optimization to avoid making a recursive call if the sum would immediately exceed the target. (The <code>current_sum &gt; target</code> base case would catch it anyway, but this prunes earlier).</li>
</ul>
</li>
<li><strong>Build Combination</strong>: The current number <code>num</code> is added to <code>current_combination</code>.</li>
<li><strong>Recursive Call</strong>: <code>backtrack(i + 1, current_sum + num, current_combination)</code><ul>
<li>A recursive call is made with <code>i + 1</code> as the new <code>start_index</code>. This is crucial because each number can be used only <em>once</em> in a combination.</li>
</ul>
</li>
<li><strong>Backtrack</strong>: <code>current_combination.pop()</code><ul>
<li>After the recursive call returns (meaning all combinations starting with the current <code>num</code> have been explored), <code>num</code> is removed from <code>current_combination</code>. This allows the loop to explore other choices for the current <code>start_index</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Initiate Backtracking</strong>: The <code>backtrack</code> function is initially called with <code>(0, 0, [])</code>, starting from the first candidate, with a sum of 0, and an empty combination list.</p>
</li>
<li><p><strong>Return Results</strong>: Finally, the accumulated <code>results</code> list containing all unique combinations is returned.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Backtracking</strong>: This is a natural choice for problems requiring the generation of all combinations or permutations, as it systematically explores all possibilities by building a solution step-by-step and undoing choices (backtracking) when a path doesn't lead to a solution or all sub-solutions from that path have been found.</li>
<li><strong>Sorting <code>candidates</code></strong>:<ul>
<li><strong>Enables Duplicate Skipping</strong>: As described above, sorting allows the <code>if i &gt; start_index and candidates[i] == candidates[i-1]</code> check to effectively prevent duplicate combinations. Without sorting, this check would not work reliably.</li>
<li><strong>Optimized Pruning</strong>: While less critical, sorting ensures that if <code>current_sum</code> exceeds <code>target</code> at some point, subsequent elements in the sorted list will only be larger (or equal), reinforcing that further exploration down that path is futile.</li>
</ul>
</li>
<li><strong><code>start_index</code> Parameter</strong>: This parameter is vital for two reasons:<ul>
<li><strong>Preventing Reuse</strong>: By passing <code>i + 1</code>, it ensures that each number in <code>candidates</code> is used at most once in a given path.</li>
<li><strong>Handling Order (Combinations vs. Permutations)</strong>: For combinations, <code>[1, 2]</code> is the same as <code>[2, 1]</code>. By always progressing <code>start_index</code>, we naturally generate combinations in a non-decreasing order, implicitly avoiding permutations.</li>
</ul>
</li>
<li><strong>Copying <code>current_combination</code> (<code>list(current_combination)</code>)</strong>: <code>current_combination</code> is a mutable list that is modified throughout the recursion. When a valid combination is found, a <em>deep copy</em> is added to <code>results</code>. If a copy were not made, <code>results</code> would end up containing multiple references to the <em>same</em> <code>current_combination</code> list, which would be empty or incorrect by the time the function finishes.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: <code>O(2^N * N)</code> in the worst case</strong>, where <code>N</code> is the number of <code>candidates</code>.<ul>
<li><strong>Worst-Case Exponential</strong>: Combination Sum problems inherently have an exponential worst-case time complexity because, at each step, we roughly have a choice to either include or exclude an element (or a number of similar choices). The number of total combinations can grow exponentially.</li>
<li><strong>Factors</strong>:<ul>
<li><code>2^N</code>: Represents the potential number of states in the recursion tree.</li>
<li><code>N</code>: For sorting the candidates (initial step, <code>O(N log N)</code>) and for creating copies of combinations (<code>list(current_combination)</code>) which can have length up to <code>N</code>.</li>
</ul>
</li>
<li>The pruning and duplicate skipping optimizations significantly reduce the <em>actual</em> explored search space for many inputs, but the theoretical upper bound remains exponential.</li>
</ul>
</li>
<li><strong>Space Complexity: <code>O(N + M * L)</code></strong><ul>
<li><code>O(N)</code>: For the recursion stack depth (at most <code>N</code> nested calls if all numbers are picked).</li>
<li><code>O(N)</code>: For <code>current_combination</code> (stores up to <code>N</code> elements).</li>
<li><code>O(M * L)</code>: For <code>results</code>, where <code>M</code> is the number of valid combinations found, and <code>L</code> is the maximum length of any combination. In the worst case, <code>M</code> can also be exponential, and <code>L</code> can be <code>N</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>candidates</code> list</strong>:<ul>
<li><code>len(candidates)</code> will be 0. The <code>for</code> loop in <code>backtrack</code> will not execute. <code>results</code> will remain <code>[]</code>, which is correct.</li>
</ul>
</li>
<li><strong><code>target</code> is 0</strong>:<ul>
<li>The initial call <code>backtrack(0, 0, [])</code> will immediately hit <code>if current_sum == target:</code>, adding <code>[]</code> to <code>results</code>. This is usually considered a valid combination for a target of 0.</li>
</ul>
</li>
<li><strong><code>candidates</code> with all elements greater than <code>target</code></strong>:<ul>
<li>The <code>current_sum + num &gt; target</code> condition (or the <code>current_sum &gt; target</code> base case after <code>num</code> is added) will quickly prune all paths, resulting in <code>[]</code> being returned, which is correct.</li>
</ul>
</li>
<li><strong><code>candidates</code> contain only duplicates (e.g., <code>[2, 2, 2]</code>, <code>target = 4</code>)</strong>:<ul>
<li>The sorting and duplicate skipping logic handles this correctly. It will produce <code>[[2, 2]]</code> exactly once.</li>
</ul>
</li>
<li><strong>All elements sum to less than <code>target</code></strong>:<ul>
<li>No valid combinations will be found, and <code>results</code> will be <code>[]</code>, which is correct.</li>
</ul>
</li>
</ul>
<p>The solution correctly handles all these edge cases due to the robust backtracking structure, sorting, and the crucial duplicate-skipping logic.</p>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite readable, with clear variable names and comments. No significant readability improvements are needed.</li>
<li><strong>Performance (Minor)</strong>: The <code>if current_sum + num &lt;= target:</code> check before the recursive call is a good micro-optimization. It prevents unnecessary function calls that would immediately hit the <code>current_sum &gt; target</code> base case.</li>
<li><strong>Alternative Approaches</strong>:<ul>
<li><strong>Dynamic Programming</strong>: While possible for some variations of combination sum (especially for counting combinations or using items multiple times), generating all unique combinations with the "each item once" constraint and duplicate inputs often makes backtracking with sorting the most straightforward and sometimes more memory-efficient approach compared to complex DP table constructions that store lists of combinations.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Recursion Depth</strong>: For very large inputs <code>N</code> (e.g., thousands of candidates), the recursion depth could exceed Python's default recursion limit (usually 1000 or 3000). In competitive programming contexts, this might necessitate increasing the limit (<code>sys.setrecursionlimit()</code>) or switching to an iterative DFS approach using an explicit stack, though for typical LeetCode constraints <code>N</code> is usually small enough for recursion to pass.</li>
<li><strong>Memory Usage</strong>: Storing all combinations in <code>results</code> can consume significant memory if the number of combinations <code>M</code> or their average length <code>L</code> is very large. This is an inherent property of problems requiring all solutions.</li>
</ul>


### Code:
```python
class Solution(object):
    def combinationSum2(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        results = []
        # Sort candidates to handle duplicates and make pruning more efficient
        candidates.sort()

        def backtrack(start_index, current_sum, current_combination):
            # Base case: if current_sum equals target, we found a valid combination
            if current_sum == target:
                results.append(list(current_combination))
                return

            # Pruning: if current_sum exceeds target, no need to continue
            if current_sum > target:
                return

            # Iterate through candidates starting from start_index
            for i in range(start_index, len(candidates)):
                # Skip duplicates: if the current number is the same as the previous one
                # and we are not at the very first element of the current iteration,
                # skip it to avoid duplicate combinations.
                if i > start_index and candidates[i] == candidates[i-1]:
                    continue

                num = candidates[i]

                # If adding num doesn't exceed target, proceed
                if current_sum + num <= target:
                    current_combination.append(num)
                    # Recursive call: move to the next index (i + 1) because each number can be used only once
                    backtrack(i + 1, current_sum + num, current_combination)
                    current_combination.pop() # Backtrack: remove the last added number

        backtrack(0, 0, [])
        return results
```
