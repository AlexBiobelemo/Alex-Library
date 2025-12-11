## Unique Paths
**Language:** python
**Tags:** python,combinatorics,algorithms,mathematics

### Description:
<p>This code implements a solution to the classic "Unique Paths" problem.</p>
<h2>1. Overview &amp; Intent</h2>
<p>This Python code calculates the number of unique paths a robot can take to reach the bottom-right corner of an <code>m x n</code> grid, starting from the top-left corner. The robot can only move either down or right at any point in time.</p>
<p>The intent is to efficiently solve this combinatorial problem, which asks "how many ways are there to choose <code>k</code> items from <code>n</code> total items?".</p>
<h2>2. How It Works</h2>
<p>The core idea behind this solution is to reframe the problem as a combinatorial one:</p>
<ul>
<li><strong>Total Moves</strong>: To go from <code>(0,0)</code> to <code>(m-1, n-1)</code>, the robot must make exactly <code>m-1</code> 'down' moves and <code>n-1</code> 'right' moves.</li>
<li><strong>Total Steps</strong>: The total number of steps required is <code>(m-1) + (n-1) = m + n - 2</code>.</li>
<li><strong>Combinatorial Choice</strong>: Out of these <code>m + n - 2</code> total steps, we need to choose <code>m-1</code> of them to be 'down' moves (the remaining <code>n-1</code> will automatically be 'right' moves). Alternatively, we could choose <code>n-1</code> of them to be 'right' moves. Both lead to the same result.</li>
<li><strong>Formula</strong>: This is a direct application of the "combinations" formula, often written as C(N, K) or <code>N_C_K</code>, which is <code>N! / (K! * (N-K)!)</code>.<ul>
<li>Here, <code>N = m + n - 2</code> (total steps).</li>
<li>And <code>K = m - 1</code> (number of down moves).</li>
</ul>
</li>
</ul>
<p>The code then iteratively calculates <code>C(N, K)</code>:</p>
<ol>
<li>It first determines <code>N</code> and <code>K</code> based on <code>m</code> and <code>n</code>.</li>
<li>It optimizes <code>K</code> by using the property <code>C(N, K) = C(N, N - K)</code>. It chooses the smaller of <code>K</code> and <code>N - K</code> to reduce the number of iterations in the loop.</li>
<li>It initializes <code>res</code> to 1.</li>
<li>It then iterates <code>K</code> times. In each iteration <code>i</code>, it updates <code>res</code> using the formula <code>res = res * (N - i) // (i + 1)</code>. This avoids calculating large factorials directly and ensures that intermediate results remain manageable while maintaining integer division at each step (which is mathematically guaranteed to result in an integer for combinations).</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Algorithm</strong>: Combinatorics (N choose K). This is the most mathematically direct and often most efficient approach for this specific problem type.</li>
<li><strong>Optimization for K</strong>: The line <code>if K &gt; N - K: K = N - K</code> (which is equivalent to <code>K = min(K, N-K)</code>) is a crucial optimization. It ensures that the loop runs for <code>min(m-1, n-1)</code> iterations, which is always the smaller of the two counts. This reduces the number of multiplications and divisions, especially when one dimension is much larger than the other.</li>
<li><strong>Iterative Combination Calculation</strong>: Instead of calculating <code>N!</code>, <code>K!</code>, and <code>(N-K)!</code> separately (which could lead to massive intermediate numbers and potential overflow in languages with fixed-size integers), the code iteratively calculates <code>C(N, K)</code> using the identity <code>C(N, k) = C(N, k-1) * (N-k+1) / k</code>. This ensures that each intermediate <code>res</code> value is an integer and avoids excessively large numbers until the final result.</li>
<li><strong>Integer Division</strong>: The <code>//</code> operator ensures that <code>res</code> remains an integer throughout the calculation, which is correct for combination results.</li>
</ul>
<h2>4. Complexity</h2>
<ul>
<li><strong>Time Complexity</strong>: <code>O(min(m, n))</code><ul>
<li>The loop runs <code>K</code> times, where <code>K</code> is <code>min(m-1, n-1)</code>. Therefore, the number of operations scales linearly with the smaller of the two dimensions.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(1)</code><ul>
<li>The solution uses a fixed number of variables (<code>N</code>, <code>K</code>, <code>res</code>, <code>i</code>) regardless of the input size <code>m</code> and <code>n</code>.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong>1x1 Grid (m=1, n=1)</strong>:<ul>
<li><code>N = 1 + 1 - 2 = 0</code>.</li>
<li><code>K = 1 - 1 = 0</code>.</li>
<li>The loop <code>for i in range(K)</code> (i.e., <code>range(0)</code>) does not run.</li>
<li><code>res</code> remains <code>1</code>. Correct, as the robot is already at the destination.</li>
</ul>
</li>
<li><strong>1xN or Nx1 Grid (m=1 or n=1)</strong>:<ul>
<li>E.g., <code>m=1, n=5</code>.</li>
<li><code>N = 1 + 5 - 2 = 4</code>.</li>
<li><code>K = 1 - 1 = 0</code>.</li>
<li>The loop <code>for i in range(0)</code> does not run.</li>
<li><code>res</code> remains <code>1</code>. Correct, there's only one straight path.</li>
<li>E.g., <code>m=5, n=1</code>.</li>
<li><code>N = 5 + 1 - 2 = 4</code>.</li>
<li><code>K = 5 - 1 = 4</code>.</li>
<li><code>K</code> is then optimized: <code>if 4 &gt; (4-4)</code> is <code>if 4 &gt; 0</code>, so <code>K</code> remains <code>4</code>. (Wait, this is wrong. <code>if K &gt; N-K</code> is <code>if 4 &gt; (4-4)</code> i.e. <code>4 &gt; 0</code>. So <code>K</code> should become <code>N-K = 0</code>. Ah, I see: <code>min(m-1, n-1)</code> is <code>min(4,0)</code> which is <code>0</code>. The code implements <code>K = N - K</code> if <code>K &gt; N - K</code>, which is the same as choosing the smaller. So <code>K</code> will be <code>0</code>. The loop <code>range(0)</code> does not run. <code>res</code> is <code>1</code>. Correct.</li>
</ul>
</li>
<li><strong>Intermediate Integer Property</strong>: The expression <code>res * (N - i) // (i + 1)</code> correctly maintains <code>res</code> as an integer throughout the loop because <code>C(N, K)</code> is always an integer, and this iterative formula is a standard way to compute it while ensuring divisibility at each step.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Readability</strong>: The comments clearly explain the mathematical reasoning, which is excellent. The variable names (<code>N</code>, <code>K</code>, <code>res</code>) are standard for combinatorial problems.</li>
<li><strong>Alternative Algorithms</strong>:<ul>
<li><strong>Dynamic Programming (DP)</strong>: A common approach for this problem. You can build a <code>m x n</code> grid where <code>dp[i][j]</code> represents the number of unique paths to <code>(i,j)</code>. The recurrence relation is <code>dp[i][j] = dp[i-1][j] + dp[i][j-1]</code>.<ul>
<li>Time: <code>O(m*n)</code></li>
<li>Space: <code>O(m*n)</code> (or <code>O(min(m, n))</code> if optimized to use only two rows/columns).</li>
<li>This DP approach is generally less efficient than the combinatorial solution for this specific problem if <code>min(m,n)</code> is small compared to <code>m*n</code>.</li>
</ul>
</li>
<li><strong>Recursive (with Memoization)</strong>: Similar to DP but top-down. <code>paths(i, j) = paths(i-1, j) + paths(i, j-1)</code>. Requires a cache to avoid recomputing subproblems.<ul>
<li>Time: <code>O(m*n)</code></li>
<li>Space: <code>O(m*n)</code> for recursion stack and memoization table.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Input Validation</strong>: For a production environment, one might add checks for <code>m</code> and <code>n</code> being positive integers, though for competitive programming platforms like LeetCode, inputs typically conform to problem constraints.</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Performance</strong>: The combinatorial approach is highly efficient for this problem, especially when <code>m</code> and <code>n</code> can be large. Its <code>O(min(m, n))</code> time complexity outperforms the <code>O(m*n)</code> of DP solutions.</li>
<li><strong>Arbitrary Precision Integers</strong>: Python's integers automatically handle arbitrary size. This means there's no risk of integer overflow, even if <code>m</code> and <code>n</code> are very large and <code>res</code> becomes enormous (e.g., <code>m=100, n=100</code>). This is a significant advantage over languages like Java or C++ where <code>long long</code> might still overflow for very large inputs, requiring <code>BigInteger</code> libraries. However, handling extremely large numbers does incur a slight performance overhead compared to fixed-size integers.</li>
</ul>


### Code:
```python
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        # The robot needs to make a total of (m-1) down moves and (n-1) right moves.
        # The total number of steps is (m-1) + (n-1) = m + n - 2.
        # This is a combinatorial problem: choose (m-1) positions for down moves
        # (or (n-1) positions for right moves) out of (m+n-2) total steps.
        # C(N, K) = N! / (K! * (N-K)!)
        # Here, N = m + n - 2 (total steps)
        # K = m - 1 (number of down moves)

        N = m + n - 2
        K = m - 1

        # To optimize calculation, choose the smaller K for C(N, K)
        # C(N, K) = C(N, N-K)
        # N-K = (m+n-2) - (m-1) = n-1
        # So, we can use K = min(m-1, n-1)
        if K > N - K: # Equivalent to K > N // 2
            K = N - K

        res = 1
        for i in range(K):
            res = res * (N - i) // (i + 1)
        
        return res
```
