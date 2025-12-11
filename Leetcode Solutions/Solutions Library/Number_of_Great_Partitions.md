## Number of Great Partitions
**Language:** python
**Tags:** python,dynamic programming,subset sum,array

### Description:
<p> This problem is a classic dynamic programming challenge combined with the principle of inclusion-exclusion.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>countPartitions</code> function aims to solve a subset sum problem variant. It takes a list of integers <code>nums</code> and an integer <code>k</code>. The goal is to determine the number of ways to partition <code>nums</code> into two disjoint groups (let's call them Group 1 and Group 2) such that the sum of elements in Group 1 is <em>at least</em> <code>k</code>, AND the sum of elements in Group 2 is <em>at least</em> <code>k</code>. The result should be returned modulo <code>10^9 + 7</code>.</p>
<h3>2. How It Works</h3>
<p>The strategy employed is to count all possible partitions and then subtract the "invalid" ones.</p>
<ol>
<li><strong>Initial Check</strong>: It first calculates <code>total_sum = sum(nums)</code>. If <code>total_sum &lt; 2 * k</code>, it's impossible for both groups to individually sum up to at least <code>k</code> (because their combined sum wouldn't even reach <code>2k</code>). In this case, it immediately returns <code>0</code>.</li>
<li><strong>Dynamic Programming (DP) for Subset Sums</strong>:<ul>
<li>A DP array <code>dp</code> of size <code>k</code> is initialized. <code>dp[j]</code> will store the number of distinct subsets of <code>nums</code> whose elements sum up to exactly <code>j</code>.</li>
<li><code>dp[0]</code> is set to <code>1</code> as there's one way to achieve a sum of <code>0</code> (by choosing the empty set).</li>
<li>The code then iterates through each number <code>x</code> in <code>nums</code>. For each <code>x</code>, it updates the <code>dp</code> array.</li>
<li>The inner loop <code>for j in range(k - 1, x - 1, -1)</code> iterates backward from <code>k-1</code> down to <code>x</code>. This ensures that each number <code>x</code> is used at most once when forming a sum (0/1 Knapsack style).</li>
<li>The transition <code>dp[j] = (dp[j] + dp[j - x]) % MOD</code> means that the number of ways to get sum <code>j</code> is the sum of:<ul>
<li>Ways to get <code>j</code> <em>without</em> including the current number <code>x</code> (<code>dp[j]</code>).</li>
<li>Ways to get <code>j</code> <em>by</em> including the current number <code>x</code> (which means finding ways to get <code>j - x</code> from previous numbers, <code>dp[j - x]</code>).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Count "Small" Subsets</strong>: After the DP table is populated, <code>num_small_subsets</code> is calculated by summing all values in <code>dp</code>. This represents the total count of subsets whose sum is <em>less than</em> <code>k</code> (sums from <code>0</code> to <code>k-1</code>).</li>
<li><strong>Total Partitions</strong>: The total number of ways to divide <code>N</code> elements into two distinct groups is <code>2^N</code> (each element can either go into Group 1 or Group 2). This is calculated as <code>total_partitions = pow(2, len(nums), MOD)</code>.</li>
<li><strong>Calculate Final Answer</strong>:<ul>
<li>The problem asks for partitions <code>(G1, G2)</code> where <code>sum(G1) &gt;= k</code> AND <code>sum(G2) &gt;= k</code>.</li>
<li>This is equivalent to <code>Total Partitions - (Partitions where sum(G1) &lt; k OR sum(G2) &lt; k)</code>.</li>
<li>Using the Principle of Inclusion-Exclusion, <code>P(A OR B) = P(A) + P(B) - P(A AND B)</code>.<ul>
<li><code>P(A)</code>: Partitions where <code>sum(G1) &lt; k</code>. The number of ways to form <code>G1</code> with <code>sum(G1) &lt; k</code> is <code>num_small_subsets</code>.</li>
<li><code>P(B)</code>: Partitions where <code>sum(G2) &lt; k</code>. The number of ways to form <code>G2</code> with <code>sum(G2) &lt; k</code> is also <code>num_small_subsets</code>.</li>
<li><code>P(A AND B)</code>: Partitions where <code>sum(G1) &lt; k</code> AND <code>sum(G2) &lt; k</code>. If <code>sum(G1) &lt; k</code> and <code>sum(G2) &lt; k</code>, then <code>sum(G1) + sum(G2) = total_sum &lt; 2k</code>. However, the initial check <code>if total_sum &lt; 2 * k: return 0</code> ensures that if we reach this point, <code>total_sum &gt;= 2k</code>. Therefore, it's impossible for both conditions (<code>sum(G1) &lt; k</code> and <code>sum(G2) &lt; k</code>) to be true simultaneously. So, <code>P(A AND B) = 0</code>.</li>
</ul>
</li>
<li>Thus, the number of invalid partitions is <code>num_small_subsets + num_small_subsets - 0 = 2 * num_small_subsets</code>.</li>
<li>The final answer is <code>(total_partitions - 2 * num_small_subsets) % MOD</code>.</li>
<li>The modulo operation is applied to handle potential negative results from subtraction (e.g., if <code>2 * num_small_subsets</code> is larger than <code>total_partitions</code> before modulo).</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (DP)</strong>: The core of the solution is a classic 0/1 knapsack-like DP to count subsets summing to specific values. This is efficient for finding counts of subset sums up to a given target.</li>
<li><strong>Space Optimization in DP</strong>: The <code>dp</code> array uses <code>O(k)</code> space, which is standard for this type of knapsack problem, as only the current and previous states are needed, not the full <code>N x K</code> table. Iterating backward <code>for j in range(k-1, x-1, -1)</code> is crucial for the 0/1 property (each item used at most once).</li>
<li><strong>Inclusion-Exclusion Principle</strong>: The strategy of <code>Total - Invalid</code> is a smart application of the Principle of Inclusion-Exclusion, simplified by the pre-check for <code>total_sum &lt; 2*k</code>.</li>
<li><strong>Modulus Arithmetic</strong>: Using <code>MOD = 10**9 + 7</code> throughout the calculations prevents integer overflow when dealing with potentially large counts of subsets and partitions.</li>
<li><strong>Pre-check for <code>total_sum &lt; 2*k</code></strong>: An important early exit condition that correctly handles impossible scenarios and simplifies the inclusion-exclusion logic by ensuring the "overlap" term is zero.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li>Calculating <code>total_sum</code>: <code>O(N)</code>, where <code>N</code> is <code>len(nums)</code>.</li>
<li>DP initialization: <code>O(k)</code>.</li>
<li>DP loops: The outer loop runs <code>N</code> times (for each <code>x</code> in <code>nums</code>). The inner loop runs up to <code>k</code> times. Thus, the DP part is <code>O(N * k)</code>.</li>
<li>Summing <code>dp</code> array: <code>O(k)</code>.</li>
<li>Modular exponentiation <code>pow(2, len(nums), MOD)</code>: <code>O(log N)</code>.</li>
<li>Overall: The dominant factor is the DP, so <code>O(N * k)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>dp</code> array: <code>O(k)</code>.</li>
<li>Other variables: <code>O(1)</code>.</li>
<li>Overall: <code>O(k)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>nums</code> is empty</strong>:<ul>
<li><code>total_sum = 0</code>. If <code>k &gt; 0</code>, <code>0 &lt; 2*k</code> is true, returns <code>0</code>. Correct, as an empty set cannot form two groups each summing to <code>&gt;=k</code>.</li>
<li>If <code>k &lt;= 0</code> (assuming <code>k</code> can be non-positive, see improvements below), <code>0 &lt; 2*k</code> is false. The code would error due to <code>dp = [0]*k</code> creating an empty list and <code>dp[0]=1</code> failing. This points to a potential issue with <code>k &lt;= 0</code>.</li>
</ul>
</li>
<li><strong><code>total_sum &lt; 2 * k</code></strong>: Correctly handled by returning <code>0</code>.</li>
<li><strong>All numbers in <code>nums</code> are larger than <code>k-1</code></strong>: In this case, no individual number can contribute to a sum <code>&lt; k</code> (other than through the empty set). <code>dp</code> will remain <code>[1, 0, 0, ...]</code> after the loops, so <code>num_small_subsets</code> will be <code>1</code> (for the empty set with sum 0). This is correct.</li>
<li><strong>Numbers equal to <code>0</code> in <code>nums</code></strong>: If <code>nums</code> contains <code>0</code>, <code>dp[j] = (dp[j] + dp[j - 0]) = 2 * dp[j]</code>. This doubles the count for all sums that could be formed. This is correct because including or excluding <code>0</code> does not change the sum, effectively doubling the ways to achieve a particular sum.</li>
<li><strong>Correctness of <code>total_partitions - 2 * num_small_subsets</code></strong>: As discussed in "How It Works", the initial <code>total_sum &lt; 2 * k</code> check ensures that the two sets of invalid partitions (<code>sum(G1) &lt; k</code> and <code>sum(G2) &lt; k</code>) are mutually exclusive, simplifying the Inclusion-Exclusion Principle. The interpretation of <code>2^N</code> for ordered partitions is standard for these types of problems.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ol>
<li><strong>Robustness for <code>k &lt;= 0</code></strong>:<ul>
<li>The current code implicitly assumes <code>k &gt;= 1</code> because <code>dp = [0] * k</code> creates an empty list if <code>k = 0</code>, leading to an <code>IndexError</code> at <code>dp[0] = 1</code>.</li>
<li>If <code>k &lt;= 0</code>, the conditions <code>sum(G1) &gt;= k</code> and <code>sum(G2) &gt;= k</code> are always true for non-negative numbers in <code>nums</code>. In this scenario, all <code>2^N</code> partitions are valid.</li>
<li><strong>Improvement</strong>: Add an explicit check at the beginning:<pre><code class="language-python">if k &lt;= 0:
    return pow(2, len(nums), MOD)
</code></pre>
</li>
<li>This makes the code robust for all <code>k</code> values and aligns with the problem logic.</li>
</ul>
</li>
<li><strong>Clarity for DP array size</strong>: While <code>dp = [0] * k</code> is technically correct for sums up to <code>k-1</code>, it can be slightly less intuitive than <code>dp = [0] * (k + 1)</code> which explicitly covers sums up to <code>k</code>. However, given the problem's requirement for sums <em>less than</em> <code>k</code>, the current size <code>k</code> is efficient.</li>
<li><strong>Alternative Approaches (less likely to be better here)</strong>:<ul>
<li><strong>Meet-in-the-Middle</strong>: If <code>N</code> were much larger and <code>k</code> relatively small (or we just needed to check existence, not count), splitting <code>nums</code> into two halves and generating subset sums could be faster. However, for counting <em>all</em> subset sums up to <code>k-1</code>, the <code>O(N*k)</code> DP is typically optimal.</li>
<li><strong>Generating Partitions (Brute Force)</strong>: Iterating through all <code>2^N</code> partitions would be too slow for <code>N &gt; 20-25</code>.</li>
</ul>
</li>
</ol>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(N*k)</code> complexity means the solution can become slow if both <code>N</code> and <code>k</code> are large (e.g., <code>N=100</code>, <code>k=100000</code> leads to <code>10^7</code> operations). This is an inherent limitation of subset sum problems.</li>
<li><strong>Security</strong>: No direct security vulnerabilities in this code. It performs mathematical calculations on input lists.</li>
<li><strong>Integer Overflow</strong>: The use of <code>% MOD</code> at each addition step in the DP loop and for the final answer calculation correctly prevents integer overflows, which is crucial when dealing with potentially large counts. Using <code>pow(base, exp, mod)</code> is also the correct way to handle modular exponentiation efficiently.</li>
</ul>


### Code:
```python
class Solution:
    def countPartitions(self, nums: List[int], k: int) -> int:
        MOD = 10**9 + 7
        total_sum = sum(nums)
        
        # If the total sum is less than 2*k, it's impossible to satisfy 
        # the condition for both groups simultaneously.
        if total_sum < 2 * k:
            return 0
            
        # dp[i] will store the number of subsets that sum up to exactly i.
        # We only care about sums up to k-1.
        dp = [0] * k
        dp[0] = 1  # Base case: one way to get sum 0 (empty subset)
        
        # 0/1 Knapsack DP logic
        for x in nums:
            # We iterate backwards to ensure each number is used only once per subset
            # We only update if the current sum j >= x
            for j in range(k - 1, x - 1, -1):
                dp[j] = (dp[j] + dp[j - x]) % MOD
                
        # Calculate the number of subsets with sum < k
        num_small_subsets = sum(dp) % MOD
        
        # Total partitions is 2^n
        total_partitions = pow(2, len(nums), MOD)
        
        # Subtract invalid partitions (Group 1 < k OR Group 2 < k)
        # We multiply by 2 because either group could be the one with sum < k
        ans = (total_partitions - 2 * num_small_subsets) % MOD
        
        return ans
```
