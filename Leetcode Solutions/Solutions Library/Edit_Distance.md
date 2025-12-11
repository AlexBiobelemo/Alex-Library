## Edit Distance
**Language:** python
**Tags:** python,dynamic programming,edit distance,string algorithms

### Description:
<p>This code implements a classic dynamic programming solution to calculate the Levenshtein distance (also known as edit distance) between two words.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem</strong>: Given two strings, <code>word1</code> and <code>word2</code>, find the minimum number of operations required to transform <code>word1</code> into <code>word2</code>.</li>
<li><strong>Allowed Operations</strong>: Insert, Delete, and Replace a character. Each operation counts as 1.</li>
<li><strong>Goal</strong>: Return the minimum total count of these operations.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution uses a bottom-up dynamic programming approach:</p>
<ul>
<li><strong>DP Table Initialization</strong>: A 2D array <code>dp</code> of size <code>(m+1) x (n+1)</code> is created, where <code>m</code> is the length of <code>word1</code> and <code>n</code> is the length of <code>word2</code>.<ul>
<li><code>dp[i][j]</code> represents the minimum edit distance between the first <code>i</code> characters of <code>word1</code> (<code>word1[:i]</code>) and the first <code>j</code> characters of <code>word2</code> (<code>word2[:j]</code>).</li>
</ul>
</li>
<li><strong>Base Cases</strong>:<ul>
<li><code>dp[i][0] = i</code>: If <code>word2</code> is an empty string, we need <code>i</code> deletions to transform <code>word1[:i]</code> into <code>""</code>.</li>
<li><code>dp[0][j] = j</code>: If <code>word1</code> is an empty string, we need <code>j</code> insertions to transform <code>""</code> into <code>word2[:j]</code>.</li>
</ul>
</li>
<li><strong>Filling the DP Table</strong>: The table is filled iteratively for <code>i</code> from 1 to <code>m</code> and <code>j</code> from 1 to <code>n</code>.<ul>
<li><strong>Characters Match</strong>: If <code>word1[i-1]</code> (current character in <code>word1</code>) is equal to <code>word2[j-1]</code> (current character in <code>word2</code>), no operation is needed for this pair. The distance is simply the distance of the prefixes <code>dp[i-1][j-1]</code>.</li>
<li><strong>Characters Mismatch</strong>: If the characters do not match, we consider three possibilities, taking the minimum cost among them plus 1 (for the current operation):<ul>
<li><code>dp[i-1][j]</code> (Delete): Delete <code>word1[i-1]</code>. We pay 1 for deletion and then find the distance between <code>word1[:i-1]</code> and <code>word2[:j]</code>.</li>
<li><code>dp[i][j-1]</code> (Insert): Insert <code>word2[j-1]</code> into <code>word1</code>. We pay 1 for insertion and then find the distance between <code>word1[:i]</code> and <code>word2[:j-1]</code>.</li>
<li><code>dp[i-1][j-1]</code> (Replace): Replace <code>word1[i-1]</code> with <code>word2[j-1]</code>. We pay 1 for replacement and then find the distance between <code>word1[:i-1]</code> and <code>word2[:j-1]</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Result</strong>: The final answer is <code>dp[m][n]</code>, which represents the minimum edit distance between the entire <code>word1</code> and <code>word2</code>.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dynamic Programming (DP) Table</strong>: A 2D array is the core data structure. This allows storing and reusing solutions to subproblems, avoiding redundant computations.</li>
<li><strong>Bottom-up Approach</strong>: The solution builds up from the smallest subproblems (empty strings, single characters) to the full problem. This ensures that when calculating <code>dp[i][j]</code>, all its dependencies (<code>dp[i-1][j]</code>, <code>dp[i][j-1]</code>, <code>dp[i-1][j-1]</code>) have already been computed.</li>
<li><strong>State Definition</strong>: <code>dp[i][j]</code> clearly defines the subproblem being solved, making the transitions logical.</li>
<li><strong>Trade-offs</strong>:<ul>
<li><strong>Space for Time</strong>: The <code>dp</code> table uses <code>O(m*n)</code> space to achieve <code>O(m*n)</code> time complexity. This is generally preferred over a purely recursive solution without memoization, which would have exponential time complexity due to repeated calculations.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: <code>O(m*n)</code><ul>
<li>Two nested loops iterate <code>m+1</code> and <code>n+1</code> times respectively.</li>
<li>Each step inside the loop involves constant time operations (array access, comparison, min).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(m*n)</code><ul>
<li>The <code>dp</code> table requires <code>(m+1) * (n+1)</code> integer cells.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Strings</strong>:<ul>
<li><code>minDistance("", "")</code>: <code>dp[0][0]</code> is initialized to 0 (correct).</li>
<li><code>minDistance("abc", "")</code>: Handled by <code>dp[i][0] = i</code> initialization (correctly returns 3).</li>
<li><code>minDistance("", "xyz")</code>: Handled by <code>dp[0][j] = j</code> initialization (correctly returns 3).</li>
</ul>
</li>
<li><strong>Identical Strings</strong>: If <code>word1 == word2</code>, the <code>if word1[i-1] == word2[j-1]</code> condition will always be met, leading to <code>dp[i][j] = dp[i-1][j-1]</code>. The final result <code>dp[m][n]</code> will correctly be 0.</li>
<li><strong>Strings with no common characters</strong>: The algorithm correctly calculates the necessary inserts/deletes/replaces.</li>
<li><strong>Correctness Principle</strong>: The solution adheres to Bellman's principle of optimality. The optimal solution to transforming <code>word1[:i]</code> to <code>word2[:j]</code> is built upon optimal solutions to transforming smaller prefixes.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Space Optimization</strong>:<ul>
<li>Since <code>dp[i][j]</code> only depends on values from the current row (<code>dp[i][j-1]</code>) and the previous row (<code>dp[i-1][j]</code>, <code>dp[i-1][j-1]</code>), the <code>dp</code> table can be optimized to use only two rows (<code>O(min(m, n))</code> or <code>O(n)</code> space if we always keep <code>n</code> as the smaller dimension). This reduces space complexity from <code>O(m*n)</code> to <code>O(n)</code>.</li>
</ul>
</li>
<li><strong>Readability</strong>: The code is quite readable for a DP solution. Variable names <code>m</code>, <code>n</code>, <code>dp</code> are standard. Comments for base cases and operations are helpful.</li>
<li><strong>Alternative Implementation</strong>:<ul>
<li><strong>Recursive with Memoization</strong>: A top-down recursive solution with memoization would conceptually mirror this bottom-up approach, yielding the same time and space complexity, but might involve more overhead due to function calls.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(m*n)</code> time complexity is generally considered optimal for this problem using DP. For very long strings, the quadratic complexity can become a bottleneck, but there are no known significantly faster general-purpose algorithms.</li>
<li><strong>Memory</strong>: For extremely long strings (e.g., <code>m, n &gt; 10^4</code>), the <code>O(m*n)</code> space complexity could lead to memory exhaustion. The space optimization discussed above would be crucial in such scenarios.</li>
<li><strong>Security</strong>: This algorithm is purely computational and operates on string lengths and characters. It does not involve external inputs, file I/O, network operations, or user authentication, so there are no direct security vulnerabilities associated with the algorithm itself.</li>
</ul>


### Code:
```python
class Solution(object):
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        m = len(word1)
        n = len(word2)

        dp = [[0] * (n + 1) for _ in range(m + 1)]

        # Initialize base cases
        # If word2 is empty, we need to delete all characters from word1
        for i in range(m + 1):
            dp[i][0] = i
        
        # If word1 is empty, we need to insert all characters from word2
        for j in range(n + 1):
            dp[0][j] = j

        # Fill the DP table
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                # If characters match, no operation needed for this pair
                if word1[i - 1] == word2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                else:
                    dp[i][j] = 1 + min(dp[i - 1][j],      # Delete
                                       dp[i][j - 1],      # Insert
                                       dp[i - 1][j - 1])  # Replace
        
        return dp[m][n]
```
