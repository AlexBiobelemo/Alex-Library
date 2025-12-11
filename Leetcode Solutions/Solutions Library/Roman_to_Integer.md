## Roman to Integer
**Language:** python
**Tags:** python,algorithm,roman numerals,string processing,dictionary

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code implements a function <code>romanToInt</code> that takes a string representing a Roman numeral and converts it into its corresponding integer value. Roman numerals follow specific rules where symbols (I, V, X, L, C, D, M) represent certain values, and their positions determine whether they are added or subtracted from the total.</p>
<h3>2. How It Works</h3>
<p>The algorithm processes the Roman numeral string from left to right:</p>
<ul>
<li><strong>Symbol Mapping</strong>: It first defines a dictionary <code>roman_map</code> to store the integer value for each Roman numeral symbol (e.g., 'I': 1, 'V': 5, etc.).</li>
<li><strong>Initialization</strong>: A <code>total</code> variable is initialized to 0, and the length of the input string <code>n</code> is obtained.</li>
<li><strong>Iterative Processing</strong>: It iterates through the string from the first character up to the second-to-last character (<code>n - 1</code>).<ul>
<li>In each iteration, it retrieves the integer values for the <code>current_val</code> (at index <code>i</code>) and the <code>next_val</code> (at index <code>i+1</code>).</li>
<li><strong>Subtractive Rule</strong>: If <code>current_val</code> is less than <code>next_val</code> (e.g., 'I' before 'V' in "IV"), it means the <code>current_val</code> should be subtracted from the <code>total</code> (as per Roman numeral rules where a smaller value preceding a larger one means subtraction).</li>
<li><strong>Additive Rule</strong>: Otherwise (if <code>current_val</code> is greater than or equal to <code>next_val</code>), it means the <code>current_val</code> should be added to the <code>total</code>.</li>
</ul>
</li>
<li><strong>Last Character</strong>: After the loop finishes, the last character (<code>s[n-1]</code>) has not yet been processed. Its value is always added to the <code>total</code> because it has no character to its right to form a subtractive pair.</li>
<li><strong>Return Value</strong>: The final <code>total</code> integer is returned.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dictionary for Mapping</strong>: Using a hash map (Python dictionary) for <code>roman_map</code> allows for O(1) average-case time complexity for looking up the integer value of each Roman symbol. This is efficient given the small, fixed set of Roman symbols.</li>
<li><strong>Left-to-Right Scan with Lookahead</strong>: The choice to iterate from left to right and look at the <em>next</em> character is a common and intuitive way to handle the subtractive rule (e.g., 'IV' where 'I' comes before 'V').</li>
<li><strong>Separate Handling for Last Character</strong>: The design explicitly handles the last character outside the main loop. This is necessary because the loop's <code>i+1</code> access would go out of bounds if <code>i</code> reached <code>n-1</code>.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(N)<ul>
<li>The <code>roman_map</code> initialization is constant time as it's a fixed size.</li>
<li>The main <code>for</code> loop iterates <code>n-1</code> times, where <code>n</code> is the length of the input string <code>s</code>.</li>
<li>Inside the loop, dictionary lookups (<code>roman_map[s[i]]</code>) are O(1) on average.</li>
<li>The final addition of the last character is O(1).</li>
<li>Therefore, the dominant factor is the loop, making the overall time complexity linear with respect to the input string length.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(1)<ul>
<li>The <code>roman_map</code> stores a fixed number of key-value pairs (7 symbols), so its space usage is constant.</li>
<li>Variables like <code>total</code>, <code>n</code>, <code>current_val</code>, <code>next_val</code> consume constant space.</li>
<li>The space complexity does not grow with the input string length.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Single Character String</strong>: For <code>s = "M"</code>, <code>n=1</code>. The loop <code>range(n-1)</code> (i.e., <code>range(0)</code>) won't execute. <code>total</code> remains 0. Then <code>total += roman_map[s[0]]</code> makes <code>total = 1000</code>. Correct.</li>
<li><strong>Two Character Strings (Additive)</strong>: For <code>s = "VI"</code>, <code>n=2</code>. Loop <code>i=0</code>. <code>current_val = 5</code> ('V'), <code>next_val = 1</code> ('I'). <code>current_val</code> is NOT less than <code>next_val</code>. <code>total += 5</code>. Loop ends. <code>total += roman_map[s[1]]</code> (i.e., 1). <code>total = 5 + 1 = 6</code>. Correct.</li>
<li><strong>Two Character Strings (Subtractive)</strong>: For <code>s = "IV"</code>, <code>n=2</code>. Loop <code>i=0</code>. <code>current_val = 1</code> ('I'), <code>next_val = 5</code> ('V'). <code>current_val &lt; next_val</code>. <code>total -= 1</code>. Loop ends. <code>total += roman_map[s[1]]</code> (i.e., 5). <code>total = -1 + 5 = 4</code>. Correct.</li>
<li><strong>Longer Strings</strong>: The logic correctly applies the additive and subtractive rules sequentially. For example, "MCMXCIV":<ul>
<li>M (1000) -&gt; total=1000</li>
<li>CM (C&lt;M) -&gt; total -= 100 (total=900)</li>
<li>XM (X&lt;M) -&gt; total -= 10 (total=890) -&gt; wait, this is not X M but M C M X C I V. The loop is <code>s[i]</code> and <code>s[i+1]</code>.</li>
<li>M (1000), C (100) -&gt; 1000 + 100 = 1100</li>
<li>M (1000), C (100) -&gt; total += 1000</li>
<li>C (100), M (1000) -&gt; total -= 100</li>
<li>M (1000), X (10) -&gt; total += 1000</li>
<li>X (10), C (100) -&gt; total -= 10</li>
<li>C (100), I (1) -&gt; total += 100</li>
<li>I (1), V (5) -&gt; total -= 1</li>
<li>Last char V (5) -&gt; total += 5</li>
<li>Let's trace "MCMXCIV":<ul>
<li>Initial total = 0</li>
<li>i=0: s[0]='M'(1000), s[1]='C'(100). 1000 not &lt; 100. total += 1000. total = 1000.</li>
<li>i=1: s[1]='C'(100), s[2]='M'(1000). 100 &lt; 1000. total -= 100. total = 900.</li>
<li>i=2: s[2]='M'(1000), s[3]='X'(10). 1000 not &lt; 10. total += 1000. total = 1900.</li>
<li>i=3: s[3]='X'(10), s[4]='C'(100). 10 &lt; 100. total -= 10. total = 1890.</li>
<li>i=4: s[4]='C'(100), s[5]='I'(1). 100 not &lt; 1. total += 100. total = 1990.</li>
<li>i=5: s[5]='I'(1), s[6]='V'(5). 1 &lt; 5. total -= 1. total = 1989.</li>
<li>Loop ends.</li>
<li>Add last char: s[6]='V'(5). total += 5. total = 1994. Correct.</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>The logic correctly handles all standard Roman numeral structures, assuming the input <code>s</code> is a valid Roman numeral string.</p>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Right-to-Left Iteration (Simplified Logic)</strong>: An alternative approach can simplify the logic by iterating from right to left. This often avoids the special handling of the last character and the <code>if/else</code> condition for current vs. next:<pre><code class="language-python">class Solution(object):
    def romanToInt(self, s):
        roman_map = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000}
        total = 0
        prev_val = 0 # To store the value of the character to the right

        # Iterate from right to left
        for i in range(len(s) - 1, -1, -1):
            current_val = roman_map[s[i]]
            
            if current_val &lt; prev_val: # If current is smaller than what's to its right (e.g., I in IV)
                total -= current_val
            else: # Otherwise (e.g., V in VI, or I in III)
                total += current_val
            
            prev_val = current_val # Update prev_val for the next iteration
        
        return total
</code></pre>
This right-to-left approach is often considered slightly cleaner as it eliminates the <code>n-1</code> loop boundary and the explicit addition of the last character outside the loop. Both achieve O(N) time and O(1) space.</li>
<li><strong>Input Validation</strong>: If this function were exposed to untrusted user input, adding validation to ensure <code>s</code> only contains valid Roman numeral characters and follows their construction rules would be crucial to prevent <code>KeyError</code> or incorrect results. For typical LeetCode-style problems, input is guaranteed valid.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The current implementation is highly efficient. Its O(N) time complexity is optimal because every character in the input string must be examined at least once. The O(1) space complexity is also optimal as it uses a fixed amount of auxiliary space.</li>
<li><strong>Security</strong>: For this specific problem, there are no direct security vulnerabilities. However, in a broader application context, if the <code>s</code> input string came from an untrusted source, the lack of input validation could lead to:<ul>
<li><code>KeyError</code>: If <code>s</code> contains characters not in <code>roman_map</code>.</li>
<li>Denial of Service (DoS): If an attacker provides an extremely long string of valid Roman numerals, it could consume CPU cycles, though the linear complexity limits this risk for practical string lengths.
It's important to assume valid input for competitive programming, but always consider validation in real-world systems.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def romanToInt(self, s):
        """
        :type s: str
        :rtype: int
        """
        roman_map = {
            'I': 1, 'V': 5, 'X': 10, 'L': 50,
            'C': 100, 'D': 500, 'M': 1000
        }
        
        total = 0
        n = len(s)
        
        # Iterate through the string up to the second to last character
        for i in range(n - 1):
            current_val = roman_map[s[i]]
            next_val = roman_map[s[i+1]]
            
            if current_val < next_val:
                total -= current_val
            else:
                total += current_val
                
        # Add the value of the last character
        total += roman_map[s[n-1]]
        
        return total
```
