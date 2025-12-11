## Letter Combinations of a Phone Number
**Language:** python
**Tags:** backtracking,recursion,combinations,string

### Description:
<p>This code snippet implements a classic backtracking algorithm to generate all possible letter combinations that a given string of digits (from a phone keypad) can represent.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem</strong>: Given a string containing digits from <code>2-9</code> inclusive, return all possible letter combinations that the numbers could represent. The mapping of digits to letters is the same as on a phone keypad.</li>
<li><strong>Goal</strong>: Generate a list of strings, where each string is a unique combination of letters corresponding to the input digits.</li>
<li><strong>Example</strong>: Input <code>"23"</code> should produce <code>["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution uses a recursive backtracking approach:</p>
<ol>
<li><strong>Mapping</strong>: A dictionary <code>mapping</code> stores the standard phone keypad digit-to-letter correspondences (e.g., <code>'2': 'abc'</code>).</li>
<li><strong>Base Case</strong>: The <code>backtrack</code> function is called recursively. When the <code>index</code> (current digit being processed) reaches the total length of the <code>digits</code> string, it means a complete combination has been formed. This <code>current_combination</code> is then added to the <code>result</code> list.</li>
<li><strong>Recursive Step</strong>:<ul>
<li>For the current <code>digit</code> at <code>index</code>, retrieve its corresponding <code>letters</code> from the <code>mapping</code>.</li>
<li>Iterate through each <code>letter</code> in these <code>letters</code>.</li>
<li>For each <code>letter</code>, make a recursive call to <code>backtrack</code>, incrementing the <code>index</code> by 1 (to move to the next digit) and appending the current <code>letter</code> to the <code>current_combination</code>.</li>
</ul>
</li>
<li><strong>Initiation</strong>: The process starts with <code>backtrack(0, "")</code>, meaning we begin with the first digit and an empty initial combination.</li>
<li><strong>Empty Input</strong>: If <code>digits</code> is an empty string, an empty list <code>[]</code> is immediately returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure: <code>mapping</code> (Dictionary)</strong>:<ul>
<li><strong>Purpose</strong>: Provides efficient O(1) lookup for letters associated with a digit.</li>
<li><strong>Benefit</strong>: Clear and direct representation of the keypad mapping.</li>
</ul>
</li>
<li><strong>Algorithm: Backtracking (Recursion)</strong>:<ul>
<li><strong>Purpose</strong>: Systematically explores all possible paths (combinations) by building them step-by-step and "backtracking" when a path is complete or leads to a dead end.</li>
<li><strong>Benefit</strong>: Naturally fits problems involving permutations, combinations, and state-space search. It's often concise and readable for this type of problem.</li>
</ul>
</li>
<li><strong>String Concatenation (<code>current_combination + letter</code>)</strong>:<ul>
<li><strong>Decision</strong>: New string created at each step.</li>
<li><strong>Trade-off</strong>: While readable, in Python, string concatenation creates new string objects each time, which can have performance implications for very long combinations or deep recursion.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the number of digits in the input string.
Let <code>M</code> be the maximum number of letters a digit can map to (e.g., 4 for '7' or '9').</p>
<ul>
<li><strong>Time Complexity: O(M^N * N)</strong><ul>
<li>There are <code>M</code> choices for each of the <code>N</code> digits (in the worst case). This leads to <code>M^N</code> total combinations.</li>
<li>For each combination, building the string <code>current_combination + letter</code> (or appending to <code>result</code>) takes <code>O(N)</code> time because Python strings are immutable and new strings are created.</li>
<li>Therefore, the total time is approximately <code>(number of combinations) * (time to build each combination) = M^N * N</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(M^N * N)</strong><ul>
<li><strong>Result Storage</strong>: The <code>result</code> list stores <code>M^N</code> strings, each of length <code>N</code>. This dominates the space complexity.</li>
<li><strong>Recursion Stack</strong>: The maximum depth of the recursion is <code>N</code> (one call per digit). Each stack frame consumes some memory, making this <code>O(N)</code>.</li>
<li><strong>Mapping</strong>: The <code>mapping</code> dictionary is constant size, <code>O(1)</code>.</li>
<li>Total space: <code>O(M^N * N + N + 1) = O(M^N * N)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Input (<code>""</code>)</strong>:<ul>
<li><strong>Handling</strong>: Explicitly handled by <code>if not digits: return []</code>.</li>
<li><strong>Correctness</strong>: Returns an empty list, which is the expected behavior for no digits.</li>
</ul>
</li>
<li><strong>Single Digit Input (e.g., <code>"2"</code>)</strong>:<ul>
<li><strong>Handling</strong>: The backtracking correctly generates <code>['a', 'b', 'c']</code>.</li>
<li><strong>Correctness</strong>: The base case is hit when <code>index</code> becomes <code>1</code>, and each single letter is appended.</li>
</ul>
</li>
<li><strong>Digits Not in Mapping</strong>:<ul>
<li><strong>Handling</strong>: The problem constraints typically imply input will only contain digits '2'-'9'. If an invalid digit (e.g., '0', '1', or non-digit) were present, <code>mapping[digit]</code> would raise a <code>KeyError</code>.</li>
<li><strong>Correctness</strong>: Under standard problem constraints, it's correct. If constraints allowed other digits, explicit error handling or filtering would be needed.</li>
</ul>
</li>
<li><strong>General Correctness</strong>: The backtracking ensures that all possible combinations are explored due to its systematic depth-first search of the decision tree. Each combination is unique because paths are distinct.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (String Building)</strong>:<ul>
<li><strong>Current</strong>: <code>current_combination + letter</code> creates a new string in each recursive call.</li>
<li><strong>Improvement</strong>: Pass a <code>list</code> of characters for <code>current_combination</code> to the <code>backtrack</code> function. When the base case is hit, <code>"".join(current_combination_list)</code> to form the final string. This avoids repeated intermediate string allocations.<pre><code class="language-python"># Inside backtrack function
# ...
def backtrack(index, current_combination_list): # Pass list
    if index == len(digits):
        result.append("".join(current_combination_list)) # Join at end
        return
    # ...
    for letter in letters:
        current_combination_list.append(letter) # Append char
        backtrack(index + 1, current_combination_list)
        current_combination_list.pop() # Backtrack: remove last char
# Initial call: backtrack(0, [])
</code></pre>
</li>
</ul>
</li>
<li><strong>Iterative Approach (BFS or DFS)</strong>:<ul>
<li>Can be implemented using a <code>deque</code> (for BFS) or a <code>stack</code> (for DFS) instead of recursion. For example, BFS would start with <code>[""]</code> in a queue, then iteratively expand combinations.</li>
<li><strong>Benefit</strong>: Avoids recursion stack depth limits for very large <code>N</code> (though <code>N</code> is typically small for this problem due to exponential time complexity).</li>
</ul>
</li>
<li><strong>Readability</strong>: The current recursive solution is quite readable and idiomatic for backtracking problems, especially for those familiar with the pattern. The suggested string building improvement makes it slightly less direct but more efficient.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck</strong>: The inherent exponential time complexity <code>O(M^N)</code> means this algorithm is only practical for small values of <code>N</code> (number of digits). For <code>N &gt; ~10-12</code>, the computation time and memory usage for storing results would become prohibitive, regardless of minor optimizations. This is a property of the problem itself, not a flaw in the algorithm's design.</li>
<li><strong>Security</strong>: There are no direct security vulnerabilities in this code. It processes input digits and generates strings, without interacting with external systems, user input parsing beyond its explicit domain, or sensitive data.</li>
</ul>


### Code:
```python
class Solution(object):
    def letterCombinations(self, digits):
        """
        :type digits: str
        :rtype: List[str]
        """
        if not digits:
            return []

        mapping = {
            '2': 'abc',
            '3': 'def',
            '4': 'ghi',
            '5': 'jkl',
            '6': 'mno',
            '7': 'pqrs',
            '8': 'tuv',
            '9': 'wxyz'
        }

        result = []
        
        def backtrack(index, current_combination):
            # Base case: if the current combination is the same length as digits, it's complete
            if index == len(digits):
                result.append(current_combination)
                return

            # Get the letters corresponding to the current digit
            digit = digits[index]
            letters = mapping[digit]

            # Iterate through each letter and recurse
            for letter in letters:
                backtrack(index + 1, current_combination + letter)

        # Start the backtracking process from the first digit with an empty combination
        backtrack(0, "")
        return result
```
