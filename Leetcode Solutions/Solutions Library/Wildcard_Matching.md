## Wildcard Matching
**Language:** python
**Tags:** dynamic programming,wildcard matching,string matching,python

### Description:
<p>This code implements a solution for wildcard pattern matching using dynamic programming. It determines if a given input string <code>s</code> matches a pattern <code>p</code> where <code>?</code> matches any single character and <code>*</code> matches any sequence of characters (including the empty sequence).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: To check if a given string <code>s</code> matches a wildcard pattern <code>p</code>.</li>
<li><strong>Wildcards</strong>:<ul>
<li><code>?</code>: Matches any single character.</li>
<li><code>*</code>: Matches any sequence of characters, including an empty sequence.</li>
</ul>
</li>
<li><strong>Approach</strong>: The problem is solved using dynamic programming to efficiently handle overlapping subproblems and complex <code>*</code> behavior.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to build a 2D boolean table <code>dp</code> where <code>dp[i][j]</code> represents whether the first <code>i</code> characters of string <code>s</code> (i.e., <code>s[0...i-1]</code>) match the first <code>j</code> characters of pattern <code>p</code> (i.e., <code>p[0...j-1]</code>).</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li><code>dp[0][0] = True</code>: An empty string <code>s</code> matches an empty pattern <code>p</code>.</li>
<li>**First Row (<code>s</code> is empty)<code>**: For </code>j<code>from 1 to</code>n` (pattern length):<ul>
<li><code>dp[0][j]</code> is <code>True</code> only if <code>p[j-1]</code> is <code>'*'</code> and the preceding pattern <code>p[0...j-2]</code> also matched an empty string. This allows a sequence of <code>*</code>s at the beginning of <code>p</code> to match an empty <code>s</code>. All other <code>p[j-1]</code> characters cannot match an empty string.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Filling the DP Table</strong>: Iterate through <code>s</code> (from <code>i=1</code> to <code>m</code>) and <code>p</code> (from <code>j=1</code> to <code>n</code>):</p>
<ul>
<li><strong>Case 1: Direct Match or <code>?</code></strong>:<ul>
<li>If <code>p[j-1]</code> is equal to <code>s[i-1]</code> OR <code>p[j-1]</code> is <code>'?'</code>:</li>
<li>Then <code>dp[i][j]</code> is <code>True</code> if <code>dp[i-1][j-1]</code> was <code>True</code> (meaning the preceding parts matched).</li>
</ul>
</li>
<li><strong>Case 2: <code>*</code> Wildcard</strong>:<ul>
<li>If <code>p[j-1]</code> is <code>'*'</code>:</li>
<li><code>*</code> has two interpretations:<ul>
<li>It matches an <strong>empty sequence</strong>: In this case, <code>dp[i][j]</code> depends on <code>dp[i][j-1]</code> (treating <code>*</code> as if it wasn't there).</li>
<li>It matches <strong>one or more characters</strong>: In this case, <code>dp[i][j]</code> depends on <code>dp[i-1][j]</code> (the <code>*</code> matched <code>s[i-1]</code>, and it <em>could</em> potentially match more characters from <code>s</code> if <code>dp[i-1][j]</code> was <code>True</code>).</li>
</ul>
</li>
<li>So, <code>dp[i][j]</code> is <code>True</code> if <code>dp[i][j-1]</code> OR <code>dp[i-1][j]</code> is <code>True</code>.</li>
</ul>
</li>
<li><strong>Case 3: No Match</strong>:<ul>
<li>If none of the above conditions are met (i.e., characters don't match and <code>p[j-1]</code> is not <code>?</code> or <code>*</code>), then <code>dp[i][j]</code> remains <code>False</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Result</strong>: The final answer is <code>dp[m][n]</code>, which indicates whether the entire string <code>s</code> matches the entire pattern <code>p</code>.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (DP)</strong>:<ul>
<li><strong>Rationale</strong>: The problem exhibits optimal substructure (solution depends on solutions to smaller subproblems) and overlapping subproblems (the same subproblems are encountered multiple times). DP avoids redundant computations.</li>
<li><strong>Trade-offs</strong>: Requires O(m*n) extra space for the DP table, but reduces time complexity from potentially exponential (naive recursion) to polynomial.</li>
</ul>
</li>
<li><strong>State Definition <code>dp[i][j]</code></strong>: Clearly defined as matching prefixes <code>s[0...i-1]</code> and <code>p[0...j-1]</code>. Using 1-based indexing for <code>i</code> and <code>j</code> in the <code>dp</code> table (while <code>s</code> and <code>p</code> are 0-indexed) simplifies base cases (e.g., <code>dp[0][0]</code> for empty prefixes).</li>
<li><strong>Handling <code>*</code></strong>: The <code>or</code> condition (<code>dp[i][j-1] or dp[i-1][j]</code>) is crucial for correctly modeling the <code>*</code> wildcard's ability to match zero or more characters.<ul>
<li><code>dp[i][j-1]</code>: <code>*</code> matches an empty sequence.</li>
<li><code>dp[i-1][j]</code>: <code>*</code> matches the current character <code>s[i-1]</code> and potentially more (it "consumes" <code>s[i-1]</code> and <code>*</code> remains active).</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(m * n)<ul>
<li><code>m</code> is the length of string <code>s</code>.</li>
<li><code>n</code> is the length of pattern <code>p</code>.</li>
<li>The algorithm iterates through all <code>(m+1) * (n+1)</code> cells of the DP table, with each cell calculation taking constant time.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(m * n)<ul>
<li>This is due to the <code>dp</code> table, which stores <code>(m+1) * (n+1)</code> boolean values.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution correctly handles several tricky edge cases:</p>
<ul>
<li><strong>Empty Strings/Patterns</strong>:<ul>
<li><code>s="", p=""</code>: <code>dp[0][0]</code> is <code>True</code>. Correct.</li>
<li><code>s="a", p=""</code>: <code>dp[1][0]</code> is <code>False</code>. Correct.</li>
<li><code>s="", p="*"</code>: <code>dp[0][1]</code> becomes <code>True</code> due to <code>p[0]=='*'</code> and <code>dp[0][0]</code>. Correct.</li>
<li><code>s="", p="*a"</code>: <code>dp[0][1]</code> is <code>True</code> for <code>*</code>, but <code>dp[0][2]</code> remains <code>False</code> because <code>a</code> cannot match an empty string. Correct.</li>
</ul>
</li>
<li><strong>Pattern with Multiple <code>*</code>s</strong>: <code>p="a**b"</code> behaves identically to <code>p="a*b"</code> because <code>dp[i][j-1]</code> (matching empty sequence) ensures that <code>*</code> can effectively "collapse" into previous <code>*</code>s or just ignore itself.</li>
<li><strong><code>?</code> Wildcard</strong>: Functions as expected, matching any single character.</li>
<li><strong>String/Pattern Lengths</strong>: Handles cases where <code>m=0</code> or <code>n=0</code> due to the <code>+1</code> padding in the DP table dimensions and careful base case handling.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Space Optimization (O(n) space)</strong>:<ul>
<li>Since <code>dp[i][j]</code> only depends on values from the <em>previous row</em> (<code>dp[i-1][...]</code>) and the <em>current row's previous column</em> (<code>dp[i][j-1]</code>), the <code>dp</code> table can be optimized to use only two rows (current and previous), or even just one row if updates are carefully managed. This would reduce space complexity to O(n).</li>
</ul>
</li>
<li><strong>Recursive with Memoization</strong>:<ul>
<li>This is an alternative way to implement dynamic programming. It mirrors the recursive definition of the problem and often leads to more concise code. However, it can incur function call overhead and risks stack overflow for very long strings/patterns.</li>
</ul>
</li>
<li><strong>Readability</strong>: The current code is quite readable for a DP solution, with good variable names and comments explaining the logic. No immediate improvements are necessary for readability, though adding docstrings for helper methods (if any) would be good practice.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The O(m*n) time complexity is efficient for typical string and pattern lengths. However, for extremely long inputs (e.g., <code>m</code> and <code>n</code> in the millions), the quadratic time and space complexity could become a bottleneck. The O(n) space optimization would be critical in such memory-constrained scenarios.</li>
<li><strong>Security</strong>: This specific algorithm for wildcard matching does not inherently introduce security vulnerabilities. It's a deterministic string matching utility. Unlike some more complex regular expression engines that can suffer from "catastrophic backtracking" with certain patterns, this DP approach provides a guaranteed polynomial time complexity, making it robust against denial-of-service attacks based on pattern complexity. If used in a security-sensitive context (e.g., validating user input against a pattern), the <em>pattern itself</em> must be trusted, but the matching engine is sound.</li>
</ul>


### Code:
```python
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        m = len(s)
        n = len(p)

        # dp[i][j] will be True if the first i characters of s match the first j characters of p
        dp = [[False] * (n + 1) for _ in range(m + 1)]

        # Base case: Empty string matches empty pattern
        dp[0][0] = True

        # Fill the first row (s is empty)
        # An empty string can only match a pattern consisting solely of '*' characters
        for j in range(1, n + 1):
            if p[j - 1] == '*':
                dp[0][j] = dp[0][j - 1]
            # else: dp[0][j] remains False (default initialization)

        # Fill the rest of the DP table
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                # Case 1: Characters match or pattern has '?'
                if p[j - 1] == s[i - 1] or p[j - 1] == '?':
                    dp[i][j] = dp[i - 1][j - 1]
                # Case 2: Pattern has '*'
                elif p[j - 1] == '*':
                    # '*' can match an empty sequence (dp[i][j-1])
                    # OR '*' can match one or more characters (dp[i-1][j])
                    dp[i][j] = dp[i][j - 1] or dp[i - 1][j]
                # Case 3: Characters do not match and pattern is not '?' or '*'
                # dp[i][j] remains False (default initialization)

        return dp[m][n]
```
