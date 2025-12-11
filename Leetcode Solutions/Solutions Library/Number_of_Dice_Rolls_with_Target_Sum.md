## Number of Dice Rolls with Target Sum
**Language:** python
**Tags:** dynamic programming,sliding window,combinatorics,counting

### Description:
<p>This code solves the classic dynamic programming problem of finding the number of ways to achieve a target sum by rolling a given number of dice, each with a specified number of faces. It demonstrates an advanced space and time optimization technique for DP.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>numRollsToTarget</code> function calculates the number of distinct ways to roll <code>n</code> dice, each having <code>k</code> faces (numbered 1 to <code>k</code>), such that their sum equals <code>target</code>. The result is returned modulo <code>10^9 + 7</code> to handle potentially very large numbers.</p>
<p>The solution employs dynamic programming with significant optimizations:</p>
<ul>
<li><strong>Space Optimization:</strong> Reduces memory usage from <code>O(n * target)</code> to <code>O(target)</code>.</li>
<li><strong>Time Optimization:</strong> Achieves an <code>O(1)</code> transition for each state by using a sliding window (prefix sum) technique, leading to an overall <code>O(n * target)</code> time complexity.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to build up the solution iteratively. <code>dp[j]</code> represents the number of ways to achieve a sum <code>j</code> using the <em>previous</em> number of dice processed. <code>new_dp[j]</code> represents the ways to achieve sum <code>j</code> using the <em>current</em> number of dice.</p>
<ol>
<li><p><strong>Initialization:</strong></p>
<ul>
<li>A <code>MOD</code> constant <code>10^9 + 7</code> is defined for modulo arithmetic.</li>
<li>An early exit handles impossible targets (<code>target &lt; n</code> or <code>target &gt; n * k</code>).</li>
<li><code>dp</code> is initialized as an array of zeros of size <code>target + 1</code>. <code>dp[0]</code> is set to <code>1</code>, meaning there is one way to achieve a sum of 0 (by rolling zero dice).</li>
</ul>
</li>
<li><p><strong>Iterating Through Dice (<code>i</code> loop):</strong></p>
<ul>
<li>The outer loop runs <code>n</code> times, representing the addition of one die in each iteration (from 1st die up to <code>n</code>-th die).</li>
<li>Inside this loop, a <code>new_dp</code> array is created to store the results for the current number of dice <code>i</code>.</li>
</ul>
</li>
<li><p><strong>Iterating Through Target Sums (<code>j</code> loop) with Sliding Window:</strong></p>
<ul>
<li>The inner loop iterates <code>j</code> from <code>1</code> to <code>target</code>, calculating <code>new_dp[j]</code>.</li>
<li><code>sum_ways</code> acts as a sliding window sum. It stores the sum of <code>dp[j-1]</code>, <code>dp[j-2]</code>, ..., <code>dp[j-k]</code> from the <em>previous</em> iteration (i.e., using <code>i-1</code> dice).</li>
<li><strong>Add to Window:</strong> For <code>new_dp[j]</code>, we consider rolling a '1' on the <code>i</code>-th die. This means we need <code>j-1</code> from the previous <code>i-1</code> dice. So, <code>dp[j-1]</code> is added to <code>sum_ways</code>.</li>
<li><strong>Remove from Window:</strong> If <code>j - k - 1</code> is a valid index, it means we are now considering sums where rolling a value <em>greater than <code>k</code></em> (i.e., <code>k+1</code>) on the <code>i</code>-th die would have been necessary to get the sum <code>j</code>. This is impossible. <code>dp[j - k - 1]</code> corresponds to the ways to get <code>j - (k + 1)</code> using <code>i-1</code> dice, which is now outside our valid window of face values (1 to k). So, <code>dp[j - k - 1]</code> is subtracted from <code>sum_ways</code>.</li>
<li><code>new_dp[j]</code> is then simply set to the current <code>sum_ways</code>, representing the sum of ways to form <code>j</code> using <code>i</code> dice by combining sums from <code>i-1</code> dice with each possible face value (1 to k).</li>
</ul>
</li>
<li><p><strong>Update DP Array:</strong></p>
<ul>
<li>After computing <code>new_dp</code> for all <code>j</code> values for the current <code>i</code>, <code>dp</code> is updated to <code>new_dp</code>. This prepares <code>dp</code> for the next iteration (when <code>i</code> becomes <code>i+1</code>).</li>
</ul>
</li>
<li><p><strong>Result:</strong></p>
<ul>
<li>Finally, <code>dp[target]</code> (which holds the result for <code>n</code> dice) is returned.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming:</strong> The problem exhibits optimal substructure (solution for <code>i</code> dice builds on <code>i-1</code> dice) and overlapping subproblems (multiple paths can lead to the same sum). This makes DP a natural fit.</li>
<li><strong><code>dp[j]</code> state definition:</strong> Using <code>dp[j]</code> to mean "ways to get sum <code>j</code> with <em>current</em> number of dice" (implicitly) allows for space optimization. If <code>dp[i][j]</code> were used, it would be "ways to get sum <code>j</code> with <code>i</code> dice".</li>
<li><strong>Space Optimization (<code>O(target)</code>):</strong> By observing that <code>new_dp</code> only depends on the <em>previous</em> <code>dp</code> array (results for <code>i-1</code> dice), we only need to store two rows of the DP table at any time (<code>dp</code> and <code>new_dp</code>), reducing space from <code>O(n * target)</code> to <code>O(target)</code>.</li>
<li><strong>Sliding Window / Prefix Sum Optimization (<code>O(1)</code> transition):</strong><ul>
<li>A naive DP transition would involve an inner loop <code>for face in range(1, k+1): new_dp[j] = (new_dp[j] + dp[j - face]) % MOD</code>. This would make the total complexity <code>O(n * target * k)</code>.</li>
<li>The <code>sum_ways</code> variable efficiently calculates this sum. For <code>new_dp[j]</code>, we need <code>dp[j-1] + dp[j-2] + ... + dp[j-k]</code>.</li>
<li>For <code>new_dp[j-1]</code>, we needed <code>dp[j-2] + ... + dp[j-k] + dp[j-k-1]</code>.</li>
<li>Thus, <code>new_dp[j] = new_dp[j-1] + dp[j-1] - dp[j-k-1]</code> (after appropriate index checks and modulo arithmetic). This allows calculation in <code>O(1)</code> for each <code>j</code>.</li>
</ul>
</li>
<li><strong>Modulo Arithmetic:</strong> <code>(value + MOD) % MOD</code> is correctly used to handle potential negative results from subtraction before applying the modulo, ensuring the result always stays positive.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong> <code>O(n * target)</code><ul>
<li>The outer loop runs <code>n</code> times (for each die).</li>
<li>The inner loop runs <code>target</code> times (for each possible sum).</li>
<li>Inside the inner loop, all operations (add, subtract, modulo, array access) are constant time <code>O(1)</code>.</li>
<li>Total time complexity: <code>n * target * O(1) = O(n * target)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> <code>O(target)</code><ul>
<li>Two arrays, <code>dp</code> and <code>new_dp</code>, are used, each of size <code>target + 1</code>.</li>
<li>This is <code>O(target)</code> space.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Impossible Targets:</strong><ul>
<li><code>target &lt; n</code>: It's impossible to get a sum less than <code>n</code> if each of the <code>n</code> dice must roll at least '1'. Handled correctly by <code>return 0</code>.</li>
<li><code>target &gt; n * k</code>: It's impossible to get a sum greater than <code>n * k</code> if each of the <code>n</code> dice rolls at most 'k'. Handled correctly by <code>return 0</code>.</li>
</ul>
</li>
<li><strong>Base Case <code>dp[0]=1</code>:</strong> This is crucial. It represents one way to achieve a sum of 0 with 0 dice. When the first die is considered, <code>dp[0]</code> allows us to calculate <code>new_dp[1]</code> (by rolling a 1 on the first die), <code>new_dp[2]</code> (by rolling a 2), etc., correctly.</li>
<li><strong>Modulo Operations:</strong> The use of <code>(sum_ways - dp[j - k - 1] + MOD) % MOD</code> correctly handles potential negative values when subtracting, ensuring the result remains within the positive range before applying the modulo.</li>
<li><strong>Boundary Conditions for Sliding Window:</strong> <code>j - 1 &gt;= 0</code> and <code>j - k - 1 &gt;= 0</code> correctly ensure that array accesses are within bounds and that only valid previous states contribute to <code>sum_ways</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is quite readable for an optimized DP solution, with good comments explaining the logic, especially the sliding window. The docstring is also very clear.</li>
<li><strong>Minor Variable Naming:</strong> <code>sum_ways</code> could perhaps be named <code>current_window_sum</code> or <code>sum_of_ways_from_previous_dice_in_window</code> for absolute clarity, but <code>sum_ways</code> is already descriptive enough in context.</li>
<li><strong>Alternative DP state/loop order:</strong><ul>
<li>A less optimized but conceptually simpler DP approach would be <code>dp[i][j]</code> representing ways to get sum <code>j</code> using <code>i</code> dice. The transition would be <code>dp[i][j] = sum(dp[i-1][j-f] for f in 1..k)</code>. This would lead to <code>O(n * target * k)</code> time and <code>O(n * target)</code> space without optimizations.</li>
</ul>
</li>
<li><strong>Mathematical Approach (Generating Functions):</strong> For highly specialized scenarios, this problem can be modeled using generating functions (polynomial multiplication), but it's typically more complex to implement and generally slower for typical contest constraints than optimized DP.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no direct security vulnerabilities. The algorithm is purely mathematical.</li>
<li><strong>Performance:</strong> The solution is highly optimized, achieving <code>O(n * target)</code> time and <code>O(target)</code> space, which is typically the most efficient dynamic programming approach for this problem within typical contest constraints. The modulo operation prevents integer overflow, which is a common performance/correctness issue for combinatorial problems with large numbers.</li>
</ul>


### Code:
```python
class Solution(object):
    def numRollsToTarget(self, n, k, target):
        """
        Calculates the number of ways to roll 'n' dice, each with 'k' faces, 
        to achieve a 'target' sum, modulo 10^9 + 7.

        The solution uses Dynamic Programming with O(target) space optimization 
        and an O(1) transition via a sliding window/prefix sum technique, 
        resulting in an overall time complexity of O(n * target).

        :type n: int
        :type k: int
        :type target: int
        :rtype: int
        """
        MOD = 10**9 + 7
        
        # Edge case check: If the target is impossible to reach (too small or too large)
        if target < n or target > n * k:
            return 0
        
        # dp[j] represents the number of ways to get a sum of 'j' using the 
        # current number of dice being processed.
        # Initialize for 0 dice: sum 0 is possible in 1 way.
        dp = [0] * (target + 1)
        dp[0] = 1
        
        # Iterate over each dice (from 1 up to n)
        for i in range(1, n + 1):
            # new_dp will hold the results after rolling the i-th dice
            new_dp = [0] * (target + 1)
            
            # sum_ways accumulates the rolling sum: dp[j-1] + ... + dp[j-k].
            # This is the key optimization for the O(1) transition.
            sum_ways = 0 
            
            # Iterate over all possible target sums (j from 1 to target)
            for j in range(1, target + 1):
                
                # 1. ADD: Include the new maximum value in the sliding window.
                # The new maximum is the number of ways to get sum (j - 1) 
                # using (i-1) dice (dp[j-1]). This corresponds to rolling a '1' on the i-th die.
                if j - 1 >= 0:
                    sum_ways = (sum_ways + dp[j - 1]) % MOD
                
                # 2. SUBTRACT: Remove the oldest value from the sliding window.
                # The oldest value is the number of ways to get sum (j - k - 1) 
                # using (i-1) dice (dp[j - k - 1]). This corresponds to rolling an impossible face value k+1.
                if j - k - 1 >= 0:
                    # Use (value + MOD) % MOD to handle negative results from subtraction
                    sum_ways = (sum_ways - dp[j - k - 1] + MOD) % MOD
                
                # new_dp[j] is the sum of ways to form the remaining sum (j-f) using (i-1) dice, 
                # where 1 <= f <= k. This sum is exactly what 'sum_ways' holds.
                new_dp[j] = sum_ways
                
            # Update dp array for the next dice iteration
            dp = new_dp
            
        return dp[target]

```
