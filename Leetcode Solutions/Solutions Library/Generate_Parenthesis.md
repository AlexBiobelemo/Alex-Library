## Generate Parenthesis
**Language:** python
**Tags:** python,recursion,backtracking,algorithm

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The code aims to solve a classic combinatorial problem: generating all combinations of well-formed parentheses given <code>n</code> pairs.</p>
<ul>
<li><strong>Input</strong>: An integer <code>n</code>, representing the number of pairs of parentheses.</li>
<li><strong>Output</strong>: A list of strings, where each string is a unique, well-formed combination of <code>n</code> open and <code>n</code> close parentheses.</li>
<li><strong>Example</strong>: For <code>n = 3</code>, the output would include <code>["((()))", "(()())", "(())()", "()(())", "()()()"]</code>.</li>
</ul>
<h3>2. How It Works</h3>
<p>The core of the solution is a <strong>backtracking algorithm</strong>. It systematically explores all possible sequences of parentheses, building them character by character, and prunes invalid paths early.</p>
<ul>
<li><strong><code>backtrack(current_string, open_count, close_count)</code> function</strong>:<ul>
<li>This is a recursive helper function that builds parenthesis combinations.</li>
<li><code>current_string</code>: The string built so far.</li>
<li><code>open_count</code>: The number of opening parentheses already used.</li>
<li><code>close_count</code>: The number of closing parentheses already used.</li>
</ul>
</li>
<li><strong>Base Case</strong>:<ul>
<li>If <code>len(current_string) == 2 * n</code>, it means a complete string of <code>n</code> open and <code>n</code> close parentheses has been formed. This string is then added to the <code>result</code> list, and the recursion branch terminates.</li>
</ul>
</li>
<li><strong>Recursive Steps</strong>:<ul>
<li><strong>Adding an opening parenthesis <code>(</code></strong>:<ul>
<li>Condition: <code>if open_count &lt; n</code> (we haven't used all available <code>n</code> opening parentheses yet).</li>
<li>Action: Recursively call <code>backtrack</code> with <code>current_string + "("</code>, incrementing <code>open_count</code>.</li>
</ul>
</li>
<li><strong>Adding a closing parenthesis <code>)</code></strong>:<ul>
<li>Condition: <code>if close_count &lt; open_count</code> (we can only add a closing parenthesis if there's an unmatched opening parenthesis to its left, ensuring well-formedness).</li>
<li>Action: Recursively call <code>backtrack</code> with <code>current_string + ")</code>", incrementing <code>close_count</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Initiation</strong>:<ul>
<li>The process starts with <code>backtrack("", 0, 0)</code>, an empty string and zero counts for both types of parentheses.</li>
</ul>
</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm Choice: Backtracking</strong>: This is an excellent choice for combinatorial problems like this where we need to generate all possible valid arrangements. It systematically explores the search space and prunes invalid branches early, ensuring only valid combinations are fully constructed.</li>
<li><strong>State Representation</strong>: The <code>open_count</code> and <code>close_count</code> parameters are crucial for maintaining the "well-formed" property. They effectively track the balance and total usage of parentheses.</li>
<li><strong>String Concatenation</strong>: The <code>current_string + "("</code> and <code>current_string + ")"</code> operations are used to build new strings. While simple and readable, this involves creating new string objects in Python for each recursive call.</li>
</ul>
<h3>4. Complexity</h3>
<p>The problem of generating well-formed parentheses is directly related to Catalan numbers, <code>C_n = 1/(n+1) * (2n choose n)</code>.</p>
<ul>
<li><strong>Time Complexity</strong>: <code>O(C_n * 2n)</code><ul>
<li>There are <code>C_n</code> unique well-formed parenthesis sequences of length <code>2n</code>.</li>
<li>For each sequence, generating it involves <code>2n</code> string concatenations (or character appends), which in the worst case for immutable strings could be <code>O(2n)</code> per string.</li>
<li>The Catalan numbers grow approximately as <code>4^n / (n^(3/2) * sqrt(pi))</code>, so the time complexity is roughly <code>O(4^n * n)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(C_n * 2n)</code><ul>
<li><strong><code>result</code> list</strong>: Stores <code>C_n</code> strings, each of length <code>2n</code>. This dominates the space complexity.</li>
<li><strong>Recursion Stack</strong>: The maximum depth of the recursion is <code>2n</code> (the length of the longest string). This contributes <code>O(2n)</code> space.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n = 0</code></strong>:<ul>
<li><strong>Expected Output</strong>: <code>[""]</code> (an empty string representing zero pairs).</li>
<li><strong>Code Behavior</strong>: <code>backtrack("", 0, 0)</code> is called. <code>len("")</code> is <code>0</code>, <code>2 * n</code> is <code>0</code>. The base case <code>if len(current_string) == 2 * n</code> is immediately met. <code>result.append("")</code> occurs, and <code>[""]</code> is returned. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong><code>n = 1</code></strong>:<ul>
<li><strong>Expected Output</strong>: <code>["()"]</code>.</li>
<li><strong>Code Behavior</strong>: Correctly generates <code>()</code>.</li>
</ul>
</li>
<li><strong>Correctness Logic</strong>:<ul>
<li>The <code>open_count &lt; n</code> condition ensures that we never exceed the total allowed number of open parentheses.</li>
<li>The <code>close_count &lt; open_count</code> condition is critical for well-formedness. It guarantees that a closing parenthesis is only added if there is a preceding unmatched open parenthesis, preventing invalid sequences like <code>)(</code> or <code>())</code>.</li>
<li>The base case <code>len(current_string) == 2 * n</code> ensures that only strings of the correct final length (i.e., containing exactly <code>n</code> open and <code>n</code> close parentheses) are added to the result.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (String Building)</strong>:<ul>
<li>For very large <code>n</code> (though practically constrained by the exponential nature), string concatenation <code>current_string + "("</code> can be inefficient as it creates a new string object in each step.</li>
<li>An alternative is to use a mutable list of characters (<code>char_list</code>) and then <code>"".join(char_list)</code> at the base case.<pre><code class="language-python"># Inside backtrack function:
# Instead of: backtrack(current_string + "(", ...)
# Use:
# char_list.append('(')
# backtrack(char_list, open_count + 1, close_count)
# char_list.pop() # Backtrack: remove the last character

# Base case:
# result.append("".join(char_list))

# Initial call: backtrack([], 0, 0)
</code></pre>
</li>
<li>For typical constraints of this problem (e.g., <code>n</code> up to ~8-14), Python's string optimizations make the original approach perfectly acceptable and often clearer.</li>
</ul>
</li>
<li><strong>Input Validation</strong>:<ul>
<li>Add a check at the beginning to ensure <code>n</code> is a non-negative integer. If <code>n</code> is negative or a non-integer, the behavior is undefined and could lead to errors.</li>
</ul>
</li>
<li><strong>Alternatives</strong>:<ul>
<li><strong>Iterative Approach</strong>: While less common for direct generation, one could simulate the recursion using an explicit stack. This is often more complex to implement correctly for this type of problem.</li>
<li><strong>Dynamic Programming</strong>: A DP approach typically focuses on calculating the <em>number</em> of well-formed parentheses. While it <em>can</em> be adapted to generate them, the backtracking approach is usually more intuitive and straightforward for actual generation.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: No direct security vulnerabilities are present in this algorithm. It's a purely computational task.</li>
<li><strong>Performance</strong>: As highlighted in the complexity analysis, the problem is inherently exponential. For <code>n</code> values beyond roughly 14-15, the execution time and memory consumption will become prohibitive due to the rapid growth of Catalan numbers. The current implementation is efficient for its class of problem; optimizations would typically only yield minor gains for specific <code>n</code> values or push the practical limit slightly higher.</li>
</ul>


### Code:
```python
class Solution(object):
    def generateParenthesis(self, n):
        """
        :type n: int
        :rtype: List[str]
        """
        result = []

        def backtrack(current_string, open_count, close_count):
            # Base case: if the current string has reached the desired length (2*n)
            if len(current_string) == 2 * n:
                result.append(current_string)
                return

            # Recursive step 1: Add an opening parenthesis if we haven't used all 'n' open parentheses
            if open_count < n:
                backtrack(current_string + "(", open_count + 1, close_count)

            # Recursive step 2: Add a closing parenthesis if the number of closing parentheses
            # is less than the number of opening parentheses (to maintain well-formedness)
            if close_count < open_count:
                backtrack(current_string + ")", open_count, close_count + 1)

        # Start the backtracking process with an empty string and zero counts
        backtrack("", 0, 0)
        return result
```
