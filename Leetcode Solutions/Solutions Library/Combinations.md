## Combinations
**Language:** python
**Tags:** backtracking,recursion,combinations,python

### Description:
<p>This code implements a classic backtracking algorithm to find all unique combinations of <code>k</code> numbers chosen from the range <code>[1, n]</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of the <code>combine(n, k)</code> function is to generate all possible combinations of <code>k</code> unique numbers from the set of integers <code>[1, 2, ..., n]</code>. For example, if <code>n=4</code> and <code>k=2</code>, it should return <code>[[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]</code>.</p>
<h3>2. How It Works</h3>
<p>The function uses a recursive helper function, <code>backtrack</code>, to explore all potential combinations:</p>
<ol>
<li><strong>Initialization:</strong> An empty list <code>result</code> is created to store all valid combinations found.</li>
<li><strong><code>backtrack(current_combination, start_num)</code>:</strong><ul>
<li><code>current_combination</code>: A list holding the numbers currently selected for a potential combination.</li>
<li><code>start_num</code>: An integer indicating the smallest number that can be considered for the current combination in the next step. This prevents duplicates and ensures numbers are chosen in increasing order.</li>
</ul>
</li>
<li><strong>Base Case:</strong> If <code>len(current_combination)</code> equals <code>k</code>, a complete combination of <code>k</code> elements has been formed. A <em>copy</em> of <code>current_combination</code> is added to <code>result</code>, and the function returns.</li>
<li><strong>Recursive Step:</strong><ul>
<li>The code iterates through numbers from <code>start_num</code> up to <code>n</code>.</li>
<li>For each number <code>i</code>:<ul>
<li>It <code>append</code>s <code>i</code> to <code>current_combination</code>.</li>
<li>It makes a recursive call to <code>backtrack</code> with the updated <code>current_combination</code> and <code>i + 1</code> (to ensure the next number picked is strictly greater than <code>i</code>, avoiding duplicates like <code>[2, 1]</code> if <code>[1, 2]</code> was already found).</li>
<li>After the recursive call returns (meaning all combinations starting with the current <code>current_combination</code> and <code>i</code> have been explored), it <code>pop</code>s <code>i</code> from <code>current_combination</code>. This is the "backtracking" step, allowing the exploration of other paths by removing the last choice.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Initial Call:</strong> The process starts by calling <code>backtrack([], 1)</code> with an empty combination and allowing numbers from <code>1</code> to <code>n</code> to be considered.</li>
<li><strong>Return:</strong> Finally, the function returns the <code>result</code> list containing all generated combinations.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Backtracking Algorithm:</strong> This is a standard and effective approach for combinatorial problems like combinations and permutations. It systematically explores all possibilities by making choices and undoing them.</li>
<li><strong>Mutable <code>current_combination</code>:</strong> Using a list for <code>current_combination</code> allows efficient <code>append</code> and <code>pop</code> operations (amortized O(1)), which are crucial for backtracking.</li>
<li><strong><code>start_num</code> Parameter:</strong> This is a critical design choice to:<ul>
<li>Prevent duplicate combinations (e.g., <code>[1, 2]</code> and <code>[2, 1]</code> are considered the same combination).</li>
<li>Ensure that numbers within a combination are always in non-decreasing order, simplifying logic and avoiding redundant calculations.</li>
</ul>
</li>
<li><strong><code>result.append(list(current_combination))</code>:</strong> Appending a <em>copy</em> of <code>current_combination</code> is essential. If a copy wasn't made, <code>result</code> would contain references to the same <code>current_combination</code> list, which would be empty by the time the backtracking completes.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: <code>O(C(n, k) * k)</code></strong><ul>
<li><code>C(n, k)</code> (read "n choose k") is the number of unique combinations, calculated as <code>n! / (k! * (n-k)!)</code>.</li>
<li>The algorithm generates each of these <code>C(n, k)</code> combinations.</li>
<li>For each combination, it performs <code>k</code> <code>append</code> operations and <code>k</code> <code>pop</code> operations to build it, plus an <code>O(k)</code> operation to copy the list when it's added to <code>result</code>.</li>
<li>While the recursion tree traversal involves more operations, the dominant factor for the overall time complexity is typically considered to be generating and copying all valid combinations.</li>
</ul>
</li>
<li><strong>Space Complexity: <code>O(C(n, k) * k)</code></strong><ul>
<li><strong><code>result</code> list:</strong> Stores <code>C(n, k)</code> combinations, each containing <code>k</code> integers. This takes <code>O(C(n, k) * k)</code> space.</li>
<li><strong>Recursion Stack:</strong> The maximum depth of the recursion is <code>k</code> (when <code>current_combination</code> has <code>k</code> elements). This contributes <code>O(k)</code> space.</li>
<li><strong><code>current_combination</code> list:</strong> Stores at most <code>k</code> integers, contributing <code>O(k)</code> space.</li>
<li>The overall space complexity is dominated by the storage of the <code>result</code> list.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>k = 0</code>:</strong> The base case <code>len(current_combination) == k</code> becomes <code>len([]) == 0</code>, which is true. <code>result.append([])</code> will be called once. This is correct, as there's exactly one way to choose 0 items (choose nothing).</li>
<li><strong><code>k &gt; n</code>:</strong> The <code>for</code> loop <code>range(start_num, n + 1)</code> and subsequent recursive calls will naturally fail to form a combination of length <code>k</code>. The <code>result</code> list will remain empty. This is correct, as it's impossible to choose <code>k</code> unique numbers from a set of <code>n</code> numbers when <code>k &gt; n</code>.</li>
<li><strong><code>n = 1, k = 1</code>:</strong><ul>
<li><code>backtrack([], 1)</code> is called.</li>
<li>Loop <code>i</code> from <code>1</code> to <code>1</code>. <code>i=1</code>.</li>
<li><code>current_combination</code> becomes <code>[1]</code>.</li>
<li><code>backtrack([1], 2)</code> is called.</li>
<li>Base case <code>len([1]) == 1</code> is true. <code>result.append([1])</code>. Returns.</li>
<li><code>current_combination.pop()</code> removes <code>1</code>. <code>current_combination</code> becomes <code>[]</code>.</li>
<li>Loop finishes. Returns <code>[[1]]</code>. Correct.</li>
</ul>
</li>
<li><strong>Correctness relies on:</strong><ul>
<li>The <code>start_num</code> parameter correctly guiding the selection to ensure elements are chosen in increasing order and no number is reused within a single combination.</li>
<li>The <code>list(current_combination)</code> call at the base case ensures that <em>snapshots</em> of complete combinations are stored, rather than mutable references that would be altered by later backtracking steps.</li>
<li>The <code>pop()</code> operation correctly undoing choices to allow exploration of all alternative paths.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Pruning (Optimization):</strong><ul>
<li>The <code>for</code> loop can be optimized to skip iterations that cannot possibly lead to a valid combination.</li>
<li>If <code>k - len(current_combination)</code> is the number of elements still needed, and <code>n - i + 1</code> is the number of elements remaining from <code>i</code> to <code>n</code>, then we must have <code>n - i + 1 &gt;= k - len(current_combination)</code>.</li>
<li>This implies <code>i &lt;= n - (k - len(current_combination)) + 1</code>.</li>
<li>The <code>for</code> loop can be modified to: <code>for i in range(start_num, n + 1 - (k - len(current_combination))):</code> (adjusting range end by +1 for Python's exclusive upper bound). This can significantly reduce the number of unnecessary recursive calls.</li>
</ul>
</li>
<li><strong>Iterative Solution:</strong> While often more complex to implement, combinatorial problems can sometimes be solved iteratively using a stack, avoiding Python's recursion depth limits for very large inputs (though usually <code>k</code> is small enough that recursion depth isn't an issue here).</li>
<li><strong>Generator Function:</strong> Instead of building and returning the entire <code>result</code> list, the function could be refactored as a generator that <code>yield</code>s each combination. This would be more memory-efficient if the caller only needs to process combinations one by one and doesn't need to store all of them simultaneously.</li>
<li><strong><code>itertools.combinations</code>:</strong> For production code in Python, <code>itertools.combinations(range(1, n + 1), k)</code> provides an optimized, built-in solution that is typically more efficient and Pythonic than a custom implementation.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck:</strong> The primary performance concern is the combinatorial explosion. <code>C(n, k)</code> grows very rapidly. For large <code>n</code> and <code>k</code>, generating and storing all combinations can be computationally intensive and memory-intensive, regardless of the specific backtracking implementation.</li>
<li><strong>Memory Usage:</strong> Storing <code>O(C(n, k) * k)</code> data can lead to high memory consumption for large inputs, potentially causing <code>MemoryError</code> for sufficiently large <code>n</code> and <code>k</code>. The generator alternative (mentioned above) can mitigate this if intermediate storage of all results isn't required.</li>
<li>No specific security vulnerabilities are present in this purely algorithmic code.</li>
</ul>


### Code:
```python
class Solution(object):
    def combine(self, n, k):
        """
        :type n: int
        :type k: int
        :rtype: List[List[int]]
        """
        result = []
        
        def backtrack(current_combination, start_num):
            # Base case: if the current combination has k elements, add it to the result
            if len(current_combination) == k:
                result.append(list(current_combination))
                return
            
            for i in range(start_num, n + 1):
                # Add the current number to the combination
                current_combination.append(i)
                # Recurse with the next number (i + 1)
                backtrack(current_combination, i + 1)
                # Backtrack: remove the last added number to explore other combinations
                current_combination.pop()
                
        # Start the backtracking process with an empty combination and starting number 1
        backtrack([], 1)
        return result
```
