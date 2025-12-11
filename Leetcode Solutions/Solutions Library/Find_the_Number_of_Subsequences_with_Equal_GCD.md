## Find the Number of Subsequences with Equal GCD
**Language:** python
**Tags:** python,dynamic programming,subsequences,hashmap,number theory

### Description:
<p>This Python code implements a dynamic programming solution to count pairs of disjoint non-empty subsequences <code>(s1, s2)</code> from a given list <code>nums</code> such that their greatest common divisors (GCDs) are equal: <code>gcd(s1) == gcd(s2)</code>.</p>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Count pairs of disjoint subsequences <code>(s1, s2)</code> from <code>nums</code> where <code>s1</code> and <code>s2</code> are both non-empty, and the GCD of elements in <code>s1</code> equals the GCD of elements in <code>s2</code>.</li>
<li><strong>Approach:</strong> Uses dynamic programming to build up counts of <code>(gcd(s1), gcd(s2))</code> pairs as it iterates through <code>nums</code>.</li>
<li><strong>Disjointness:</strong> Ensured by deciding for each number <code>x</code> whether to add it to <code>s1</code>, <code>s2</code>, or neither.</li>
</ul>
<h3>2. How It Works</h3>
<ol>
<li><p><strong>Initialization:</strong></p>
<ul>
<li><code>MOD = 1_000_000_007</code>: For handling large results.</li>
<li><code>dp = Counter()</code>: A dictionary (specifically a <code>collections.Counter</code>) to store the DP states.</li>
<li><code>dp[(g1, g2)]</code> stores the count of ways to form two disjoint subsequences <code>s1</code> and <code>s2</code> using numbers processed so far, such that <code>gcd(s1) = g1</code> and <code>gcd(s2) = g2</code>.</li>
<li><code>g1=0</code> or <code>g2=0</code> represents an empty subsequence.</li>
<li><code>dp[(0, 0)] = 1</code>: There's one way to have two empty subsequences initially.</li>
<li><code>max_val</code>: Stores the maximum value in <code>nums</code>, used for GCD calculations (upper bound).</li>
</ul>
</li>
<li><p><strong>Iterating through <code>nums</code>:</strong></p>
<ul>
<li>For each number <code>x</code> in <code>nums</code>:<ul>
<li><code>new_dp = dp.copy()</code>: A temporary DP table is created. This copy implicitly handles the case where <code>x</code> is <em>not</em> used in any subsequence.</li>
<li><strong>Iterate over existing states:</strong> For each <code>((g1, g2), count)</code> pair in the current <code>dp</code>:<ul>
<li><strong>Option 1: Add <code>x</code> to <code>s1</code>:</strong><ul>
<li>Calculate <code>new_g1 = gcd(g1, x)</code>. If <code>g1</code> was <code>0</code> (empty <code>s1</code>), <code>new_g1</code> becomes <code>x</code>.</li>
<li>Update <code>new_dp[(new_g1, g2)] = (new_dp[(new_g1, g2)] + count) % MOD</code>.</li>
</ul>
</li>
<li><strong>Option 2: Add <code>x</code> to <code>s2</code>:</strong><ul>
<li>Calculate <code>new_g2 = gcd(g2, x)</code>. If <code>g2</code> was <code>0</code> (empty <code>s2</code>), <code>new_g2</code> becomes <code>x</code>.</li>
<li>Update <code>new_dp[(g1, new_g2)] = (new_dp[(g1, new_g2)] + count) % MOD</code>.</li>
</ul>
</li>
</ul>
</li>
<li><code>dp = new_dp</code>: The main <code>dp</code> table is updated with the results from processing <code>x</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Final Calculation:</strong></p>
<ul>
<li><code>total_ans = 0</code>: Accumulates the final count.</li>
<li>Iterate through all <code>((g1, g2), count)</code> pairs in the final <code>dp</code> table.</li>
<li>If <code>g1 &gt; 0</code> (s1 is non-empty), <code>g2 &gt; 0</code> (s2 is non-empty), and <code>g1 == g2</code> (their GCDs are equal):<ul>
<li>Add <code>count</code> to <code>total_ans</code>, applying modulo.</li>
</ul>
</li>
<li>Return <code>total_ans</code>.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming:</strong> The problem has optimal substructure and overlapping subproblems. The <code>dp</code> table effectively memoizes results for <code>(gcd(s1), gcd(s2))</code> pairs.</li>
<li><strong>State Definition <code>dp[(g1, g2)]</code>:</strong> This concisely captures the necessary information (the GCDs of the two subsequences) to extend the subsequences with new numbers.</li>
<li><strong><code>g=0</code> for Empty Subsequence:</strong> This is a clever way to handle the initial state of empty subsequences and correctly calculate the GCD when the first element is added (<code>gcd(0, x) = x</code>).</li>
<li><strong><code>collections.Counter</code>:</strong> Provides a convenient way to store the <code>dp</code> table, automatically initializing counts to <code>0</code> for new keys, simplifying additions.</li>
<li><strong>Modulo Arithmetic:</strong> Essential for preventing integer overflow, as the number of ways can grow very large.</li>
<li><strong>Disjointness:</strong> Handled by the three choices for each <code>x</code>: add to <code>s1</code>, add to <code>s2</code>, or add to neither (handled by <code>new_dp = dp.copy()</code>). An element cannot be added to both <code>s1</code> and <code>s2</code> for a given <code>dp</code> state transition.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be <code>len(nums)</code> and <code>M</code> be <code>max(nums)</code>.</p>
<ul>
<li><p><strong>Time Complexity:</strong></p>
<ul>
<li>The outer loop runs <code>N</code> times (once for each number in <code>nums</code>).</li>
<li>Inside the outer loop, <code>dp.copy()</code> takes <code>O(|dp|)</code> time.</li>
<li>The inner loop iterates <code>|dp|</code> times.</li>
<li>Inside the inner loop, <code>gcd</code> operation takes <code>O(log M)</code> time.</li>
<li>The values <code>g1</code> and <code>g2</code> can range from <code>0</code> to <code>M</code>. Therefore, the theoretical maximum number of distinct <code>(g1, g2)</code> pairs (states in <code>dp</code>) is <code>O((M+1)^2)</code>.</li>
<li>Thus, the worst-case time complexity is <code>O(N * (M+1)^2 * log M)</code>.</li>
<li><strong>Practical Note:</strong> For typical competitive programming constraints where <code>N</code> can be <code>~2000</code> and <code>M</code> can be <code>~10^5</code>, this theoretical complexity <code>(2000 * (10^5)^2 * log(10^5) = 2 * 10^{10} * 17)</code> is too high and would result in a Time Limit Exceeded (TLE). This suggests that either <code>M</code> is expected to be much smaller in practice (e.g., <code>M &lt;= 200-500</code>), or the number of <em>reachable</em> distinct <code>(g1, g2)</code> states in the <code>Counter</code> is significantly smaller than the theoretical maximum. If <code>M</code> is small, e.g., <code>M=200</code>, <code>N=2000</code>, it's <code>2000 * 200^2 * log(200) ~ 6.4 * 10^8</code>, which is borderline.</li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong></p>
<ul>
<li>The <code>dp</code> <code>Counter</code> stores pairs <code>(g1, g2)</code>. In the worst case, <code>g1</code> and <code>g2</code> can range from <code>0</code> to <code>M</code>.</li>
<li>Thus, the space complexity is <code>O((M+1)^2)</code>.</li>
<li>Similar to time complexity, for <code>M=10^5</code>, this would require <code>(10^5)^2 = 10^{10}</code> entries, which is too much memory. This further implies <code>M</code> must be small or the number of active states is practically limited.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>nums</code> is empty:</strong> <code>max_val</code> becomes <code>0</code>. The loop for <code>x</code> doesn't run. <code>dp</code> remains <code>{(0,0): 1}</code>. The final loop's conditions (<code>g1 &gt; 0</code> and <code>g2 &gt; 0</code>) are not met. <code>total_ans</code> returns <code>0</code>, which is correct as no non-empty subsequences can be formed.</li>
<li><strong><code>nums</code> has one element <code>[x]</code>:</strong> <code>dp</code> becomes <code>{(0,0):1, (x,0):1, (0,x):1}</code>. The final loop correctly returns <code>0</code> as two disjoint non-empty subsequences cannot be formed from a single element.</li>
<li><strong>Duplicate elements in <code>nums</code>:</strong> The algorithm processes each number <code>x</code> sequentially, regardless of duplicates. This correctly counts combinations where duplicates are chosen for different subsequences (e.g., if <code>nums = [2, 2]</code>, it can form <code>s1=[2_first], s2=[2_second]</code> and <code>s1=[2_second], s2=[2_first]</code>).</li>
<li><strong><code>g1=0</code> or <code>g2=0</code> handling:</strong> The logic <code>gcd(g1, x) if g1 != 0 else x</code> correctly handles the case where an empty subsequence gets its first element, setting its GCD to that element.</li>
<li><strong>Modulo arithmetic:</strong> Applied at each addition to prevent overflow, ensuring correctness of large counts.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (Reducing State Space):</strong> The primary area for improvement is the potentially very large state space <code>O(M^2)</code>.<ul>
<li><strong>Limited <code>g</code> values:</strong> <code>g1</code> and <code>g2</code> can only be divisors of numbers present in <code>nums</code>. While <code>max_val</code> might be large, the number of <em>distinct divisors</em> is typically much smaller (<code>d(k)</code> for numbers up to <code>10^5</code> is at most 128). If the set of all possible GCDs (union of divisors of all numbers in <code>nums</code>) is smaller than <code>M</code>, the actual <code>|dp|</code> might be smaller.</li>
<li><strong>Iterating GCDs downwards:</strong> A common optimization for GCD-related DPs is to iterate through possible GCD values <code>g</code> from <code>max_val</code> down to <code>1</code>. For each <code>g</code>, one might consider how many subsequences have <code>g</code> as their GCD, but this specific DP structure (two independent GCDs) makes it less straightforward.</li>
<li><strong>Filtering <code>dp</code>:</strong> One could prune <code>dp</code> entries that are no longer reachable or relevant, but <code>Counter</code> doesn't do this automatically.</li>
</ul>
</li>
<li><strong>Readability:</strong> The code is quite readable and well-commented for its complexity.</li>
<li><strong>Standard Library <code>math.gcd</code>:</strong> Using <code>math.gcd</code> is good for correctness and performance over a custom implementation.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck:</strong> As highlighted in the complexity analysis, the solution's performance is highly dependent on <code>max(nums)</code>. For large <code>max(nums)</code> values (e.g., <code>10^5</code> or more), the <code>O(M^2)</code> space and time factor makes it impractical.</li>
<li><strong>No Security Vulnerabilities:</strong> The code deals with numerical computations and does not involve external input, file operations, or network communication, so there are no apparent security concerns.</li>
</ul>


### Code:
```python
from collections import Counter
import math

class Solution:
    def subsequencePairCount(self, nums: List[int]) -> int:
        MOD = 1_000_000_007
        
        # dp[g1][g2] = count of disjoint (s1, s2) with gcd(s1)=g1, gcd(s2)=g2
        # g1=0 or g2=0 will represent an empty subsequence
        dp = Counter()
        dp[0, 0] = 1  # One way to have two empty subsequences
        
        max_val = max(nums) if nums else 0

        def gcd(a, b):
            return math.gcd(a, b)

        for x in nums:
            new_dp = dp.copy()
            
            # Iterate over all existing states
            for (g1, g2), count in dp.items():
                
                # Option 1: Add x to s1
                # new_g1 is gcd(current_g1, x)
                # If s1 was empty (g1=0), new_g1 is x
                new_g1 = gcd(g1, x) if g1 != 0 else x
                new_dp[new_g1, g2] = (new_dp[new_g1, g2] + count) % MOD
                
                # Option 2: Add x to s2
                # new_g2 is gcd(current_g2, x)
                # If s2 was empty (g2=0), new_g2 is x
                new_g2 = gcd(g2, x) if g2 != 0 else x
                new_dp[g1, new_g2] = (new_dp[g1, new_g2] + count) % MOD
            
            # Option 3 (Don't use x) is implicitly handled
            # because new_dp starts as a copy of dp.
            # The (g1, g2) state from dp is preserved in new_dp.
            dp = new_dp

        total_ans = 0
        for (g1, g2), count in dp.items():
            # We need pairs of NON-EMPTY subsequences
            # g1 > 0 and g2 > 0
            # And their GCDs must be equal
            if g1 > 0 and g1 == g2:
                total_ans = (total_ans + count) % MOD
                
        return total_ans
```
