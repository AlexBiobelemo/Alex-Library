## Permutation Sequence
**Language:** python
**Tags:** permutation,algorithm,factorials,combinatorics

### Description:
<p>This code snippet implements an algorithm to find the <em>k</em>-th lexicographical permutation of numbers from 1 to <em>n</em>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to efficiently determine the <em>k</em>-th permutation in lexicographical order for a given set of <code>n</code> numbers (from 1 to <code>n</code>). Instead of generating all permutations and then selecting the <em>k</em>-th one (which would be computationally expensive for larger <code>n</code>), it directly constructs the desired permutation.</p>
<h3>2. How It Works</h3>
<p>The algorithm constructs the permutation digit by digit from left to right (most significant to least significant).</p>
<ol>
<li><strong>Pre-calculate Factorials</strong>: It first pre-computes factorials up to <code>n!</code>. These factorials are crucial because <code>(N-1)!</code> tells us how many distinct permutations can be formed by the remaining <code>N-1</code> digits.</li>
<li><strong>Initialize Numbers</strong>: A list <code>nums</code> is created containing the numbers '1' through 'n' as strings.</li>
<li><strong>Adjust <code>k</code></strong>: The input <code>k</code> is 1-indexed (e.g., the 1st permutation, 2nd permutation). The algorithm works better with 0-indexed values, so <code>k</code> is decremented by 1.</li>
<li><strong>Iterative Construction</strong>: The code iterates from <code>n-1</code> down to <code>0</code>. In each step <code>i</code> (representing the <code>i</code>-th position from the right, or the <code>(n-1-i)</code>-th position from the left):<ul>
<li><strong>Determine Index</strong>: <code>idx = k // factorials[i]</code> calculates which block of permutations <code>k</code> falls into. For example, if there are <code>m</code> remaining numbers, and <code>factorials[i]</code> is the count of permutations for the remaining <code>i</code> positions, then <code>factorials[i]</code> acts as the block size. <code>idx</code> tells us which of the <em>available</em> numbers should be placed at the current position.</li>
<li><strong>Select Digit</strong>: <code>result.append(nums.pop(idx))</code> takes the number at the calculated <code>idx</code> from the <code>nums</code> list, appends it to the <code>result</code>, and removes it from <code>nums</code> (as it has been used).</li>
<li><strong>Update <code>k</code></strong>: <code>k %= factorials[i]</code> updates <code>k</code> to be relative to the chosen block. This effectively "resets" <code>k</code> for the next position's calculation, considering only the permutations within the chosen block.</li>
</ul>
</li>
<li><strong>Join Result</strong>: Finally, the elements in the <code>result</code> list (which are strings) are joined together to form the final permutation string.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Factorial Pre-computation</strong>: This is a critical optimization. Calculating factorials once and storing them in an array allows for <code>O(1)</code> lookup during the main loop, preventing repeated calculations.</li>
<li><strong>List of Strings (<code>nums</code>)</strong>: Using a list of strings allows for easy string concatenation at the end and simplifies the <code>pop</code> operation for removing used numbers.</li>
<li><strong><code>k</code> Adjustment (<code>k -= 1</code>)</strong>: Standard practice in algorithms that convert a 1-indexed input (like the <em>k</em>-th item) to a 0-indexed system (like array indices).</li>
<li><strong>Greedy/Constructive Approach</strong>: The algorithm builds the permutation one digit at a time from left to right. This is an effective strategy for directly computing ranked permutations.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li>Factorial calculation: <code>O(n)</code></li>
<li><code>nums</code> list initialization: <code>O(n)</code></li>
<li>Main loop: Runs <code>n</code> times.<ul>
<li><code>nums.pop(idx)</code>: This operation, when <code>idx</code> is not the last element, requires shifting subsequent elements, taking <code>O(n)</code> time in the worst case (e.g., <code>idx = 0</code>).</li>
</ul>
</li>
<li><code>"".join(result)</code>: <code>O(n)</code></li>
<li><strong>Overall Time Complexity</strong>: <code>O(n^2)</code> due to the <code>n</code> calls to <code>pop</code> on a list, each potentially taking <code>O(n)</code> time.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>factorials</code> list: <code>O(n)</code></li>
<li><code>nums</code> list: <code>O(n)</code></li>
<li><code>result</code> list: <code>O(n)</code></li>
<li><strong>Overall Space Complexity</strong>: <code>O(n)</code></li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Small <code>n</code></strong>:<ul>
<li><code>n=1, k=1</code>: The code correctly returns "1".</li>
<li><code>n=2, k=1</code>: Returns "12".</li>
<li><code>n=2, k=2</code>: Returns "21".</li>
</ul>
</li>
<li><strong><code>k=1</code> (First Permutation)</strong>: <code>k</code> becomes 0. <code>idx</code> will always be 0, picking the smallest available number at each step, correctly generating "123...n".</li>
<li><strong><code>k=n!</code> (Last Permutation)</strong>: <code>k</code> becomes <code>n! - 1</code>. The algorithm correctly works its way through the largest indices, ultimately producing "n(n-1)...1".</li>
<li><strong>Constraints</strong>: The problem constraints (e.g., <code>1 &lt;= n &lt;= 9</code>) mean that <code>n</code> is very small. The <code>O(n^2)</code> time complexity is perfectly acceptable for <code>n=9</code> (9^2 = 81 operations for the core loop). <code>n!</code> also fits within standard integer types without issue.</li>
<li>The logic correctly accounts for <code>k</code> being 1-indexed by immediately converting it to 0-indexed, and then using the modulo and integer division properties to map <code>k</code> to the correct choices at each step.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance for Large <code>n</code></strong>:<ul>
<li>For significantly larger <code>n</code> (where <code>O(n^2)</code> becomes too slow, e.g., <code>n &gt; 10^4</code>), the <code>nums.pop(idx)</code> operation is the bottleneck. This can be optimized to <code>O(log n)</code> using data structures like a Fenwick tree (BIT) or segment tree. These structures can find the <code>k</code>-th available element and mark it as used in <code>O(log n)</code> time, reducing the overall time complexity to <code>O(n log n)</code>. However, this is overkill for <code>n &lt;= 9</code>.</li>
</ul>
</li>
<li><strong>Readability</strong>: The code is quite clear and idiomatic Python. Adding comments for the <code>k -= 1</code> step or the core logic if targeting a less experienced audience could enhance readability, but it's generally self-explanatory for those familiar with permutation algorithms.</li>
<li><strong>Input Validation</strong>: In a production environment, you might add checks to ensure <code>1 &lt;= n &lt;= 9</code> and <code>1 &lt;= k &lt;= factorials[n]</code> to handle invalid inputs gracefully, though problem statements usually guarantee valid inputs.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: As mentioned, <code>O(n^2)</code> is excellent for the given constraints. For <code>n &gt; 15-20</code>, <code>n!</code> quickly becomes too large to store or compute directly (overflowing even 64-bit integers), so this specific problem formulation (<code>n</code> up to 9) is designed for this type of algorithm. Python's arbitrary-precision integers handle large factorials fine, but the <em>count</em> of permutations becomes astronomical.</li>
<li><strong>Security</strong>: There are no apparent security vulnerabilities. The code operates purely on numerical inputs and produces a string. It doesn't interact with external systems, files, or user-provided strings that could lead to injection or other vulnerabilities.</li>
</ul>


### Code:
```python
class Solution(object):
    def getPermutation(self, n, k):
        factorials = [1] * (n + 1)
        for i in range(1, n + 1):
            factorials[i] = factorials[i-1] * i
            
        nums = [str(i) for i in range(1, n + 1)]
        
        result = []
        
        k -= 1
        
        for i in range(n - 1, -1, -1):
            idx = k // factorials[i]
            result.append(nums.pop(idx))
            k %= factorials[i]
            
        return "".join(result)
```
