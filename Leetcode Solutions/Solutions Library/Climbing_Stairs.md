## Climbing Stairs
**Language:** python
**Tags:** python,dynamic programming,space optimization,fibonacci

### Description:
<p>This code solves a classic dynamic programming problem, often used to introduce the concept of Fibonacci sequences and space optimization.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>climbStairs</code> function calculates the number of distinct ways to climb to the top of a staircase with <code>n</code> steps. At each step, you can either climb 1 or 2 steps.</p>
<ul>
<li><strong>Problem:</strong> Count unique combinations of 1-step and 2-step climbs to reach <code>n</code> steps.</li>
<li><strong>Output:</strong> An integer representing the total number of ways.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The problem can be modeled as a Fibonacci sequence.
To reach step <code>i</code>, you must have come from either:</p>
<ol>
<li>Step <code>i-1</code> by taking a 1-step climb.</li>
<li>Step <code>i-2</code> by taking a 2-step climb.</li>
</ol>
<p>Therefore, the number of ways to reach step <code>i</code> is the sum of ways to reach step <code>i-1</code> and ways to reach step <code>i-2</code>. This is <code>ways[i] = ways[i-1] + ways[i-2]</code>.</p>
<p>The code implements this recursively defined sequence iteratively with space optimization:</p>
<ul>
<li><strong>Base Case:</strong> If <code>n</code> is 1, there's only 1 way (take 1 step).</li>
<li><strong>Initialization:</strong><ul>
<li><code>a</code> is initialized to 1 (ways to reach step 1).</li>
<li><code>b</code> is initialized to 2 (ways to reach step 2: (1,1) or (2)).</li>
</ul>
</li>
<li><strong>Iteration:</strong><ul>
<li>The loop starts from <code>i = 3</code> up to <code>n</code>.</li>
<li>In each iteration, <code>temp</code> calculates the ways to reach the current step <code>i</code> by summing <code>a</code> (ways to reach <code>i-2</code>) and <code>b</code> (ways to reach <code>i-1</code>).</li>
<li><code>a</code> is then updated to <code>b</code> (the new "previous-previous" step).</li>
<li><code>b</code> is updated to <code>temp</code> (the new "previous" step).</li>
</ul>
</li>
<li><strong>Result:</strong> After the loop completes, <code>b</code> holds the number of ways to reach step <code>n</code>.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (DP):</strong> The problem exhibits optimal substructure and overlapping subproblems, making DP a natural fit.</li>
<li><strong>Iterative Approach:</strong> Instead of recursion (which could lead to redundant calculations without memoization or stack overflow for large <code>n</code>), an iterative loop is used.</li>
<li><strong>Space Optimization:</strong> Instead of storing the results in a full <code>dp</code> array (e.g., <code>dp[n]</code>), only the two most recent values (<code>dp[i-1]</code> and <code>dp[i-2]</code>) are needed to calculate <code>dp[i]</code>. This reduces space complexity from O(n) to O(1).</li>
<li><strong>Direct Initialization:</strong> The initial values <code>a=1</code> and <code>b=2</code> are directly set, handling the initial conditions for the Fibonacci sequence (F(1)=1, F(2)=2 if we map step <code>n</code> to F(n)).</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(n)</strong><ul>
<li>The <code>for</code> loop runs <code>n - 2</code> times (from <code>i = 3</code> to <code>n</code>).</li>
<li>Each operation inside the loop (addition, assignment) takes constant time, O(1).</li>
<li>Therefore, the total time complexity is linear with respect to <code>n</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>Only a constant number of variables (<code>n</code>, <code>a</code>, <code>b</code>, <code>temp</code>) are used, regardless of the input <code>n</code>.</li>
<li>No auxiliary data structures (like arrays or lists) grow with <code>n</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n = 1</code>:</strong><ul>
<li>Correctly handled by the <code>if n == 1: return 1</code> statement. There's 1 way (take 1 step).</li>
</ul>
</li>
<li><strong><code>n = 2</code>:</strong><ul>
<li>The <code>if n == 1</code> is skipped.</li>
<li><code>a</code> is 1, <code>b</code> is 2.</li>
<li>The loop <code>for i in range(3, 2 + 1)</code> which is <code>range(3, 3)</code> is empty and does not execute.</li>
<li>The function returns <code>b</code>, which is 2.</li>
<li>This is correct: (1, 1) and (2) are the two ways.</li>
</ul>
</li>
<li><strong><code>n = 3</code>:</strong><ul>
<li><code>a=1</code>, <code>b=2</code>. Loop <code>i</code> starts at 3.</li>
<li><code>i = 3</code>: <code>temp = a + b = 1 + 2 = 3</code>. <code>a = 2</code>, <code>b = 3</code>. Loop ends.</li>
<li>Returns <code>b = 3</code>.</li>
<li>Correct: (1,1,1), (1,2), (2,1) are the three ways.</li>
</ul>
</li>
<li><strong>Constraints:</strong> Assumes <code>n</code> is a positive integer. If <code>n=0</code> or negative <code>n</code> were possible inputs, the code would need modifications. Python's arbitrary-precision integers handle large results without overflow.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Clarity of variable names:</strong> The comments already explain <code>a</code> and <code>b</code> well. For a self-documenting approach, <code>ways_prev_prev</code> and <code>ways_prev</code> could be used, but <code>a</code> and <code>b</code> are common in this context.</p>
</li>
<li><p><strong>Memoization (Top-down DP):</strong> A recursive solution with memoization (caching results in a dictionary or array) is an alternative. It often looks more intuitive but typically has higher overhead for function calls and potentially larger space complexity (O(n) for the cache) than the iterative space-optimized version.</p>
<pre><code class="language-python">class Solution(object):
    def climbStairs(self, n):
        memo = {}
        def dp(k):
            if k == 1: return 1
            if k == 2: return 2
            if k in memo: return memo[k]
            memo[k] = dp(k-1) + dp(k-2)
            return memo[k]
        return dp(n)
</code></pre>
</li>
<li><p><strong>Mathematical Approach (Binet's Formula or Matrix Exponentiation):</strong> For extremely large <code>n</code> where even O(n) is too slow (e.g., <code>n</code> in the order of 10^9), Fibonacci numbers can be calculated in O(log n) time using matrix exponentiation or Binet's formula (which involves floating-point numbers and can have precision issues for very large numbers). This is typically overkill for competitive programming constraints where <code>n</code> usually fits within O(n) or O(n log n).</p>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code has no security implications. It operates purely on mathematical calculations with integer inputs.</li>
<li><strong>Performance:</strong> The iterative space-optimized DP approach (O(n) time, O(1) space) is highly efficient and generally considered the optimal practical solution for this problem given typical constraints on <code>n</code>. It avoids the overhead of recursion and the memory usage of a full DP array.</li>
</ul>


### Code:
```python
class Solution(object):
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n == 1:
            return 1
        
        # dp[i] represents the number of ways to reach step i
        # We can optimize space by only storing the last two values
        
        # a stores the number of ways to reach step i-2
        # b stores the number of ways to reach step i-1
        
        a = 1  # Ways to reach step 1
        b = 2  # Ways to reach step 2
        
        # Iterate from step 3 up to n
        for i in range(3, n + 1):
            temp = a + b  # Ways to reach current step i
            a = b         # Update a to be the previous b (ways to reach i-1)
            b = temp      # Update b to be the current temp (ways to reach i)
            
        return b
```
