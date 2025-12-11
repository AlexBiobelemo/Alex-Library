## Regular Expression Matching
**Language:** python
**Tags:** python,dynamic programming,recursion,memoization,string matching

### Description:
<p>This code implements a solution for the classic regular expression matching problem, where <code>.</code> matches any single character and <code>*</code> matches zero or more of the preceding element. It uses a top-down dynamic programming approach with memoization.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code aims to determine if a given string <code>s</code> matches a given pattern <code>p</code>, where the pattern can include two special characters:</p>
<ul>
<li><code>.</code> (dot): Matches any single character.</li>
<li><code>*</code> (asterisk): Matches zero or more occurrences of the preceding element.</li>
</ul>
<p>The function <code>isMatch(s, p)</code> returns <code>True</code> if <code>s</code> matches <code>p</code>, and <code>False</code> otherwise. It's a recursive solution enhanced with memoization to avoid redundant computations.</p>
<h3>2. How It Works</h3>
<p>The core logic is encapsulated in the nested <code>dp(i, j)</code> function, which checks if the substring <code>s[i:]</code> matches the pattern <code>p[j:]</code>.</p>
<ul>
<li><strong>Memoization</strong>: A dictionary <code>memo</code> stores the results of <code>dp(i, j)</code> calls to prevent recomputing the same subproblems.</li>
<li><strong>Base Case</strong>:<ul>
<li>If <code>j</code> (pattern index) reaches <code>len(p)</code>, it means the entire pattern has been processed. The match is successful only if <code>i</code> (string index) also reached <code>len(s)</code>, implying the entire string has been matched.</li>
</ul>
</li>
<li><strong><code>first_match</code> Check</strong>:<ul>
<li>Determines if the current character <code>s[i]</code> (if <code>i</code> is within bounds) matches the current pattern character <code>p[j]</code>. This is true if <code>p[j]</code> is <code>s[i]</code> or <code>p[j]</code> is a <code>.</code> (wildcard).</li>
</ul>
</li>
<li><strong>Handling <code>*</code> (Asterisk)</strong>:<ul>
<li>If <code>p[j+1]</code> is <code>*</code>, it means <code>p[j]</code> followed by <code>*</code> forms a special repeating element. There are two possibilities:<ol>
<li><strong><code>*</code> matches zero occurrences</strong>: We effectively skip <code>p[j]</code> and <code>p[j+1]</code> (the character and the asterisk) and try to match <code>s[i:]</code> with <code>p[j+2:]</code>. This is <code>dp(i, j + 2)</code>.</li>
<li><strong><code>*</code> matches one or more occurrences</strong>: This is only possible if <code>first_match</code> is <code>True</code> (meaning <code>s[i]</code> matches <code>p[j]</code>). If so, we consider <code>s[i]</code> matched by <code>p[j]</code>, and then try to match <code>s[i+1:]</code> with the <em>same</em> pattern <code>p[j:]</code> (because <code>*</code> can match multiple times). This is <code>first_match and dp(i + 1, j)</code>.</li>
</ol>
</li>
<li>The result is <code>match_zero OR match_one_or_more</code>.</li>
</ul>
</li>
<li><strong>No <code>*</code> or <code>*</code> is not next</strong>:<ul>
<li>If <code>p[j+1]</code> is not <code>*</code>, then <code>p[j]</code> must match <code>s[i]</code> directly. This requires <code>first_match</code> to be <code>True</code>, and then we advance both string and pattern indices: <code>first_match AND dp(i + 1, j + 1)</code>.</li>
</ul>
</li>
<li><strong>Storing Result</strong>: The computed <code>result</code> for <code>(i, j)</code> is stored in <code>memo</code> before returning.</li>
<li><strong>Initial Call</strong>: The matching process begins by calling <code>dp(0, 0)</code>.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (Top-Down with Memoization)</strong>: The problem exhibits optimal substructure (the solution to the larger problem depends on solutions to smaller subproblems) and overlapping subproblems (the same subproblems are encountered multiple times). Memoization efficiently stores and reuses results for <code>(i, j)</code> pairs.</li>
<li><strong>Recursive Structure</strong>: The problem is naturally broken down into smaller recursive calls based on the current characters of <code>s</code> and <code>p</code>.</li>
<li><strong>State Definition <code>dp(i, j)</code></strong>: This clear state definition allows representing "does <code>s[i:]</code> match <code>p[j:]</code>?" which simplifies the recursive transitions.</li>
<li><strong>Handling <code>*</code> Separately</strong>: The asterisk introduces ambiguity (zero or more matches), necessitating a branching logic (<code>OR</code> condition) to explore both possibilities. This is a critical part of the algorithm's correctness.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: <code>O(len(s) * len(p))</code><ul>
<li>There are <code>len(s)</code> possible values for <code>i</code> and <code>len(p)</code> possible values for <code>j</code>.</li>
<li>This means there are <code>len(s) * len(p)</code> unique states <code>(i, j)</code>.</li>
<li>Each state is computed at most once (due to memoization).</li>
<li>The work done for each state (checking <code>first_match</code>, conditional branches, recursive calls, dictionary lookup/store) is constant time, <code>O(1)</code>.</li>
<li>Therefore, the total time complexity is <code>O(len(s) * len(p))</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(len(s) * len(p))</code><ul>
<li>The <code>memo</code> dictionary stores results for up to <code>len(s) * len(p)</code> states, requiring <code>O(len(s) * len(p))</code> space.</li>
<li>The recursion call stack depth can go up to <code>O(len(s) + len(p))</code> in the worst case (e.g., <code>s = "aaaaa", p = "a*a*a*a*a*"</code>).</li>
<li>Combining these, the dominant space complexity is <code>O(len(s) * len(p))</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm correctly handles several edge cases:</p>
<ul>
<li><strong>Empty Strings/Patterns</strong>:<ul>
<li><code>isMatch("", "")</code> -&gt; <code>dp(0, 0)</code> -&gt; <code>j == len(p)</code> is <code>True</code>, <code>i == len(s)</code> is <code>True</code>. Returns <code>True</code>. (Correct)</li>
<li><code>isMatch("a", "")</code> -&gt; <code>dp(0, 0)</code> -&gt; <code>j == len(p)</code> is <code>True</code>, <code>i == len(s)</code> is <code>False</code>. Returns <code>False</code>. (Correct)</li>
<li><code>isMatch("", "a*")</code> -&gt; <code>dp(0, 0)</code> -&gt; <code>p[1]</code> is <code>*</code>. <code>match_zero</code> = <code>dp(0, 2)</code> (matches <code>""</code> with <code>""</code>), which returns <code>True</code>. <code>match_one_or_more</code> is <code>False</code>. Returns <code>True</code>. (Correct)</li>
</ul>
</li>
<li><strong>All <code>.</code> or <code>*</code> patterns</strong>: <code>isMatch("abc", ".*")</code> -&gt; Correctly returns <code>True</code>.</li>
<li><strong>Long sequence matches</strong>: <code>isMatch("aaaaa", "a*")</code> -&gt; Correctly returns <code>True</code>.</li>
<li><strong>Mismatch after <code>*</code></strong>: <code>isMatch("aab", "c*a*b")</code> -&gt; Correctly returns <code>True</code> (c* matches zero 'c's).</li>
<li><strong>Index Out-of-Bounds</strong>:<ul>
<li><code>i &lt; len(s)</code> in <code>first_match</code> prevents accessing <code>s[i]</code> when <code>i</code> is out of bounds.</li>
<li><code>j + 1 &lt; len(p)</code> before checking <code>p[j+1]</code> prevents accessing <code>p[j+1]</code> when <code>j</code> is near the end of the pattern.</li>
</ul>
</li>
<li><strong>Correct Operator Precedence</strong>: The logic for <code>*</code> correctly considers matching zero occurrences <em>or</em> one-or-more, and the <code>AND</code> for non-<code>*</code> cases ensures both the character match and the recursive call must be true.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Bottom-Up Dynamic Programming</strong>: The current top-down (recursive with memoization) approach can be converted to a bottom-up (iterative) DP solution using a 2D array. This can sometimes offer minor performance benefits by avoiding recursion overhead and might be clearer for some to reason about iteration order.</li>
<li><strong>Clarity and Comments</strong>: While fairly well-structured, adding more specific comments within the <code>*</code> logic branches explaining <em>why</em> each branch is taken could enhance readability.</li>
<li><strong>Error Handling</strong>: For a production-grade library, one might consider input validation (e.g., ensuring <code>p</code> doesn't have an <code>*</code> as its first character, although the current solution handles this implicitly by matching zero).</li>
<li><strong>Optimized Regex Engines</strong>: For more complex or higher-performance regular expression matching (beyond just <code>.</code> and <code>*</code>), real-world regex engines use more sophisticated algorithms like converting patterns to Non-deterministic Finite Automata (NFA) or Deterministic Finite Automata (DFA), which can offer better average-case performance.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(len(s) * len(p))</code> complexity is polynomial and generally efficient enough for typical string lengths (e.g., up to a few hundred characters). For extremely long strings (thousands or more), this algorithm might become too slow, and alternatives like those used in production regex engines would be necessary.</li>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this algorithm itself, as it's purely a string comparison logic. It doesn't execute arbitrary code or interact with external systems. It's a self-contained matching function.</li>
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
        memo = {}

        def dp(i, j):
            if (i, j) in memo:
                return memo[(i, j)]

            # Base case: if pattern is exhausted
            if j == len(p):
                return i == len(s)

            # Check if current characters match
            # first_match is true if s[i] exists and matches p[j] (or p[j] is '.')
            first_match = (i < len(s) and (p[j] == s[i] or p[j] == '.'))

            # Case 1: If there's a '*' after the current pattern character
            if j + 1 < len(p) and p[j+1] == '*':
                # Option A: '*' matches zero occurrences of the preceding element (p[j])
                # So, we skip p[j] and p[j+1] and try to match s[i:] with p[j+2:]
                match_zero = dp(i, j + 2)

                # Option B: '*' matches one or more occurrences of the preceding element (p[j])
                # This is only possible if first_match is true.
                # If so, p[j] matches s[i], and we try to match s[i+1:] with the same pattern p[j:]
                # (because '*' can match multiple times).
                match_one_or_more = first_match and dp(i + 1, j)

                result = match_zero or match_one_or_more
            else:
                # Case 2: No '*' after the current pattern character, or pattern is exhausted after p[j]
                # p[j] must match s[i], and then we advance both string and pattern.
                result = first_match and dp(i + 1, j + 1)

            memo[(i, j)] = result
            return result

        return dp(0, 0)
```
