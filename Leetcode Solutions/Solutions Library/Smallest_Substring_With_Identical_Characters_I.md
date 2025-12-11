## Smallest Substring With Identical Characters I
**Language:** python
**Tags:** python,dynamic programming,binary search,monotonic queue,prefix sum

### Description:
<p>This code solves a problem where you need to find the minimum possible maximum run length in a binary string after performing at most <code>numOps</code> character flips. This is a classic "minimum maximum" problem, typically tackled with binary search on the answer.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given a binary string <code>s</code> and a maximum number of allowed flips <code>numOps</code>, find the smallest possible value <code>k</code> such that you can transform <code>s</code> (by flipping at most <code>numOps</code> characters) into a new string where no contiguous run of identical characters (e.g., "000" or "111") has a length greater than <code>k</code>.</li>
<li><strong>Approach:</strong> The solution uses binary search on the possible values of <code>k</code> (the maximum run length). For a chosen <code>k</code>, a helper function <code>check(k)</code> determines if it's possible to achieve this <code>k</code> with <code>numOps</code> or fewer flips.</li>
<li><strong>Goal:</strong> Minimize the maximum run length while respecting the <code>numOps</code> constraint.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><p><strong>Prefix Sums:</strong></p>
<ul>
<li><code>pref0</code> and <code>pref1</code> arrays are precomputed.</li>
<li><code>pref0[i]</code> stores the count of '0's in the prefix <code>s[0...i-1]</code>.</li>
<li><code>pref1[i]</code> stores the count of '1's in the prefix <code>s[0...i-1]</code>.</li>
<li>These allow calculating the number of '0's or '1's in any substring <code>s[j...i-1]</code> in <code>O(1)</code> time (e.g., <code>pref1[i] - pref1[j]</code> gives the count of '1's in <code>s[j...i-1]</code>).</li>
</ul>
</li>
<li><p><strong><code>check(k)</code> Function (Dynamic Programming with Monotonic Queue):</strong></p>
<ul>
<li>This function determines if it's possible to transform <code>s</code> such that no run exceeds length <code>k</code>, using at most <code>numOps</code> flips.</li>
<li><strong>DP State:</strong><ul>
<li><code>dp[i][0]</code>: Minimum flips needed for the prefix <code>s[0...i-1]</code> such that <code>s[i-1]</code> (the last character) is '0', and all runs within <code>s[0...i-1]</code> have a length <code>&lt;= k</code>.</li>
<li><code>dp[i][1]</code>: Similar, but <code>s[i-1]</code> is '1'.</li>
</ul>
</li>
<li><strong>Transitions:</strong><ul>
<li>To compute <code>dp[i][0]</code>: The substring <code>s[j...i-1]</code> must consist entirely of '0's, and its length <code>(i-j)</code> must be <code>&lt;= k</code>. This implies that <code>s[j-1]</code> (if <code>j &gt; 0</code>) must have been '1'.</li>
<li>So, <code>dp[i][0]</code> is the minimum of <code>dp[j][1]</code> (cost to make <code>s[0...j-1]</code> end in '1') plus <code>cost_to_0(j, i)</code> (flips to make <code>s[j...i-1]</code> all '0's) for all valid <code>j</code> in the range <code>[max(0, i-k), i-1]</code>.</li>
<li>The formula becomes: <code>dp[i][0] = min_{j \in [i-k, i-1]} (dp[j][1] + (pref1[i] - pref1[j]))</code>.</li>
<li>This can be rewritten as: <code>dp[i][0] = (min_{j \in [i-k, i-1]} (dp[j][1] - pref1[j])) + pref1[i]</code>.</li>
<li>A similar logic applies for <code>dp[i][1]</code> using <code>dp[j][0]</code> and <code>cost_to_1</code>.</li>
</ul>
</li>
<li><strong>Monotonic Queue Optimization:</strong> To efficiently find the minimum <code>(dp[j][char] - pref[j])</code> in a sliding window <code>[i-k, i-1]</code>, two deques (<code>dq0</code> and <code>dq1</code>) are used. These deques store indices <code>j</code> in increasing order, but the values <code>dp[j][char] - pref[j]</code> are stored in increasing order. This allows finding the minimum in <code>O(1)</code> at the front of the deque and updating the deque in amortized <code>O(1)</code>.</li>
<li>The function returns <code>True</code> if <code>min(dp[n][0], dp[n][1]) &lt;= numOps</code>, meaning the entire string can be transformed within the allowed flips.</li>
</ul>
</li>
<li><p><strong>Main Binary Search Loop:</strong></p>
<ul>
<li>The search space for <code>k</code> is <code>[1, n]</code> (minimum run length 1, maximum <code>n</code>).</li>
<li>If <code>check(k)</code> is <code>True</code> (meaning <code>k</code> is achievable), we try for a smaller <code>k</code> (<code>ans = k</code>, <code>high = k - 1</code>).</li>
<li>If <code>check(k)</code> is <code>False</code> (meaning <code>k</code> is not achievable), we must allow larger runs (<code>low = k + 1</code>).</li>
<li>The loop continues until <code>low &gt; high</code>, and <code>ans</code> holds the smallest <code>k</code> found.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Binary Search on Answer:</strong> The problem structure ("minimum maximum") and the monotonic property of <code>check(k)</code> (if <code>k</code> is achievable, any <code>k' &gt; k</code> is also achievable) make binary search an ideal fit.</li>
<li><strong>Dynamic Programming:</strong> The <code>check(k)</code> subproblem naturally lends itself to DP, as optimal solutions for prefixes can be built upon optimal solutions for smaller prefixes.</li>
<li><strong>Prefix Sums:</strong> This preprocessing step is crucial for efficient (O(1)) calculation of flip costs for arbitrary substrings, avoiding repeated iterations.</li>
<li><strong>Monotonic Queue (Deque) Optimization:</strong> This is the most critical optimization, transforming the <code>O(N*K)</code> DP transitions into <code>O(N)</code> for each <code>check(k)</code> call. Without it, the overall complexity would be <code>O(N*K*log N)</code>, which would be too slow for large <code>N</code>.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity:</strong></p>
<ul>
<li><strong>Prefix Sums:</strong> <code>O(N)</code> to build <code>pref0</code> and <code>pref1</code>.</li>
<li><strong><code>check(k)</code> function:</strong> The loop runs <code>N</code> times. Inside the loop, deque operations (popleft, pop, append) take amortized <code>O(1)</code> time each because each element is added and removed at most once. Thus, <code>check(k)</code> runs in <code>O(N)</code>.</li>
<li><strong>Binary Search:</strong> The binary search performs <code>log N</code> iterations (since <code>k</code> ranges from <code>1</code> to <code>N</code>).</li>
<li><strong>Total Time Complexity:</strong> <code>O(N log N)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong></p>
<ul>
<li><strong>Prefix Sums:</strong> <code>O(N)</code> for <code>pref0</code> and <code>pref1</code>.</li>
<li><strong>DP Table:</strong> <code>O(N)</code> for <code>dp</code> array.</li>
<li><strong>Deques:</strong> <code>O(N)</code> in the worst case (e.g., when <code>k</code> is large, the deques can hold up to <code>N</code> elements).</li>
<li><strong>Total Space Complexity:</strong> <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty String (<code>n=0</code>):</strong> The code would handle this gracefully. <code>pref0</code> and <code>pref1</code> would be <code>[0]</code>. <code>check(k)</code> would initialize <code>dp[0][0]=0, dp[0][1]=0</code>. The loop <code>for i in range(1, n + 1)</code> would not run. <code>min(dp[0][0], dp[0][1])</code> (which is <code>0</code>) would be compared to <code>numOps</code>. If <code>numOps &gt;= 0</code>, <code>check(k)</code> would return <code>True</code>. The binary search range <code>low=1, high=0</code> would immediately terminate, returning <code>ans=0</code> (initialized to <code>n=0</code>), which seems correct for an empty string.</li>
<li><strong>String of Length 1 (<code>n=1</code>):</strong> Binary search will try <code>k=1</code>. <code>check(1)</code> will run. <code>dp[1][0]</code> will be <code>pref1[1]</code> (cost to flip <code>s[0]</code> to '0'). <code>dp[1][1]</code> will be <code>pref0[1]</code> (cost to flip <code>s[0]</code> to '1'). If <code>min(pref1[1], pref0[1]) &lt;= numOps</code>, it returns <code>True</code>. This correctly finds <code>k=1</code> if possible, otherwise larger.</li>
<li><strong><code>numOps = 0</code>:</strong> The algorithm will find the minimum <code>k</code> that corresponds to the initial maximum run length in <code>s</code>.</li>
<li><strong><code>numOps</code> is very large (e.g., <code>numOps &gt;= n</code>):</strong> It should be possible to achieve <code>k=1</code> (e.g., "010101..."). The <code>check(1)</code> function would calculate the cost to make <code>s</code> an alternating string. If <code>numOps</code> is sufficient, <code>check(1)</code> returns <code>True</code>, and the binary search correctly finds <code>ans=1</code>.</li>
<li><strong>String with all identical characters (e.g., "00000"):</strong> The <code>pref0</code> and <code>pref1</code> arrays will reflect this. <code>cost_to_0</code> would be 0, <code>cost_to_1</code> would be the length of the segment. The DP will correctly compute the flips needed to break up these runs to satisfy <code>k</code>.</li>
<li>The logic for <code>j_best_0</code> and <code>j_best_1</code> using <code>dq0[0]</code> and <code>dq1[0]</code> (accessing the front of the deque) is correct, as the monotonic queue ensures the minimum value for the expression <code>dp[j][char] - pref[j]</code> is always at the front, within the sliding window.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability/Comments:</strong><ul>
<li>Add a brief comment to the deque creation explaining what <code>val_i_0</code> and <code>val_i_1</code> represent (the value <code>dp[i][0] - pref0[i]</code> that is being added to the deque to maintain monotonicity).</li>
<li>Explicitly mention that <code>dp[j_best_1][1] - pref1[j_best_1]</code> is the part of the cost from <code>s[0...j_best_1-1]</code> ending in '1' <em>without</em> accounting for the <code>pref1[j_best_1]</code> which is cancelled out in the <code>cost_to_0</code> calculation.</li>
</ul>
</li>
<li><strong>No major algorithmic alternatives are significantly better.</strong> This <code>O(N log N)</code> approach is optimal for this problem type. Other approaches (e.g., flow-based) would likely be much slower.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The code is highly optimized. <code>O(N log N)</code> with <code>N</code> up to typical constraints (e.g., 10^5) is efficient. Python's <code>collections.deque</code> provides performant operations for the monotonic queue.</li>
<li><strong>Security:</strong> This code operates solely on an input string and numerical values. It does not interact with external systems (file I/O, network), user inputs (other than the string itself), or sensitive data. There are no immediate security concerns related to input validation beyond what Python's string and integer types inherently handle.</li>
</ul>


### Code:
```python
import collections
from typing import List

class Solution:
    def minLength(self, s: str, numOps: int) -> int:
        n = len(s)
        
        # Create prefix sums for '0's and '1's.
        # pref0[i] = count of '0's in s[0...i-1]
        pref0 = [0] * (n + 1)
        pref1 = [0] * (n + 1)
        for i in range(n):
            pref0[i+1] = pref0[i] + (1 if s[i] == '0' else 0)
            pref1[i+1] = pref1[i] + (1 if s[i] == '1' else 0)

        def cost_to_0(j, i):
            """Cost to make s[j...i-1] all '0's."""
            return pref1[i] - pref1[j]

        def cost_to_1(j, i):
            """Cost to make s[j...i-1] all '1's."""
            return pref0[i] - pref0[j]

        def check(k: int) -> bool:
            """
            Checks if we can achieve max run length 'k' in O(N) ops.
            
            dp[i][0] = min flips for s[0...i-1] to end in '0' (run <= k)
            dp[i][1] = min flips for s[0...i-1] to end in '1' (run <= k)
            """
            # dp[i] corresponds to prefix s[0...i-1]
            dp = [[float('inf')] * 2 for _ in range(n + 1)]
            dp[0][0] = 0
            dp[0][1] = 0
            
            # Deques will store indices 'j'
            # dq0 stores j for dp[j][0] (ending in 0)
            # dq1 stores j for dp[j][1] (ending in 1)
            # We want to find min(dp[j][char] - cost_prefix[j])
            dq0 = collections.deque([0])
            dq1 = collections.deque([0])

            for i in range(1, n + 1):
                # 1. Calculate dp[i][0] (ending in '0')
                # This must come from a dp[j][1] (ending in '1')
                # where j is in the window [i-k, i-1]
                
                # Remove indices outside the window [i-k, i-1]
                while dq1 and dq1[0] < i - k:
                    dq1.popleft()
                
                # The best 'j' is at the front of the deque
                j_best_1 = dq1[0]
                dp[i][0] = (dp[j_best_1][1] - pref1[j_best_1]) + pref1[i]

                # 2. Calculate dp[i][1] (ending in '1')
                # This must come from a dp[j][0] (ending in '0')
                
                # Remove indices outside the window
                while dq0 and dq0[0] < i - k:
                    dq0.popleft()
                    
                j_best_0 = dq0[0]
                dp[i][1] = (dp[j_best_0][0] - pref0[j_best_0]) + pref0[i]

                # 3. Add 'i' to the deques for future calculations
                # Maintain monotonic property:
                # dp[j][0] - pref0[j] must be increasing
                val_i_0 = dp[i][0] - pref0[i]
                while dq0 and (dp[dq0[-1]][0] - pref0[dq0[-1]]) >= val_i_0:
                    dq0.pop()
                dq0.append(i)
                
                # dp[j][1] - pref1[j] must be increasing
                val_i_1 = dp[i][1] - pref1[i]
                while dq1 and (dp[dq1[-1]][1] - pref1[dq1[-1]]) >= val_i_1:
                    dq1.pop()
                dq1.append(i)

            # The final answer is the minimum flips needed for the
            # entire string, ending in either '0' or '1'.
            return min(dp[n][0], dp[n][1]) <= numOps

        # --- Main Binary Search ---
        low = 1
        high = n
        ans = n

        while low <= high:
            k = (low + high) // 2
            if check(k):
                # This 'k' is possible, try to find a smaller one
                ans = k
                high = k - 1
            else:
                # This 'k' is impossible, we must allow larger runs
                low = k + 1
        
        return ans
```
