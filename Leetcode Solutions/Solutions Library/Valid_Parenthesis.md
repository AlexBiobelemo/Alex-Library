## Valid Parenthesis
**Language:** python
**Tags:** stack,string,algorithm,parentheses validation

### Description:
<p>This code implements a classic algorithm to determine if a string containing only '(', ')', '{', '}', '[', ']' is a valid sequence of parentheses.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code defines a method <code>isValid</code> within a <code>Solution</code> class, designed to check if a given string <code>s</code> of brackets is "valid". A valid string adheres to two rules:</p>
<ul>
<li>Open brackets must be closed by the same type of brackets.</li>
<li>Open brackets must be closed in the correct order.</li>
</ul>
<p>This is a common problem in computer science, often used to illustrate the utility of stack data structures.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm processes the input string <code>s</code> character by character using a stack:</p>
<ul>
<li><strong>Initialization:</strong> An empty list <code>stack</code> is created to store opening brackets. A <code>mapping</code> dictionary defines the corresponding opening bracket for each closing bracket (e.g., <code>")": "("</code>).</li>
<li><strong>Iteration:</strong><ul>
<li>If the current <code>char</code> is a <em>closing</em> bracket (i.e., it's a key in the <code>mapping</code> dictionary):<ul>
<li>It attempts to pop the top element from the <code>stack</code>. If the <code>stack</code> is empty, a placeholder <code>'#'</code> is used instead.</li>
<li>It then checks if the popped <code>top_element</code> matches the <em>expected</em> opening bracket for the current <code>char</code> (retrieved from <code>mapping</code>). If they don't match, the string is invalid, and <code>False</code> is returned.</li>
</ul>
</li>
<li>If the current <code>char</code> is an <em>opening</em> bracket:<ul>
<li>It is pushed onto the <code>stack</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Final Check:</strong> After iterating through the entire string, if the <code>stack</code> is empty, it means all opening brackets have found their corresponding closing brackets in the correct order, and the string is valid (<code>True</code> is returned). Otherwise, if the <code>stack</code> is not empty, it means there are unmatched opening brackets, and the string is invalid (<code>False</code> is returned).</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Stack Data Structure:</strong> The core design decision is using a stack (implemented with a Python list). This is crucial because parenthesis matching requires a Last-In, First-Out (LIFO) order. The most recently opened bracket must be the first one to be closed.</li>
<li><strong>Mapping Dictionary:</strong> The <code>mapping</code> dictionary provides an efficient O(1) lookup to determine the correct opening bracket type for any given closing bracket.</li>
<li><strong>Placeholder for Empty Stack:</strong> Using <code>'#'</code> as a <code>top_element</code> when the stack is empty (e.g., <code>")"</code> as the first character) is a clever way to immediately trigger a mismatch and return <code>False</code>. Any character guaranteed not to be a valid opening bracket would work.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The algorithm iterates through the input string <code>s</code> exactly once, where <code>N</code> is the length of the string.</li>
<li>Each character processing involves a dictionary lookup and a stack operation (push or pop), both of which are O(1) on average.</li>
<li>Therefore, the total time complexity is linear with the length of the string.</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li>In the worst-case scenario (e.g., <code>(((((</code>), the stack could store all opening brackets. This means the stack could potentially hold up to <code>N/2</code> elements.</li>
<li>Thus, the space complexity is also linear with the length of the string.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code handles various edge cases correctly:</p>
<ul>
<li><strong>Empty string <code>""</code>:</strong><ul>
<li>The <code>for</code> loop won't execute. <code>stack</code> remains empty. <code>return not stack</code> evaluates to <code>True</code>. Correct.</li>
</ul>
</li>
<li><strong>String with only opening brackets <code>(([</code>:</strong><ul>
<li>All brackets are pushed onto the <code>stack</code>. <code>stack</code> will not be empty at the end. <code>return not stack</code> evaluates to <code>False</code>. Correct.</li>
</ul>
</li>
<li><strong>String with only closing brackets <code>)))</code>:</strong><ul>
<li>The first closing bracket triggers the <code>if char in mapping</code> block. <code>stack</code> is empty, so <code>top_element</code> becomes <code>'#'</code>. <code>mapping[char]</code> (e.g., <code>'('</code>) will not equal <code>'#'</code>. <code>False</code> is returned immediately. Correct.</li>
</ul>
</li>
<li><strong>Mismatched bracket types <code>([)]</code>:</strong><ul>
<li><code>(</code> pushed, <code>[</code> pushed. Stack: <code>['(', '[']</code>.</li>
<li><code>)</code> comes. Pop <code>[</code>. <code>mapping[')']</code> is <code>'('</code>. <code>'(' != '['</code> is true. Returns <code>False</code>. Correct.</li>
</ul>
</li>
<li><strong>Correctly nested <code>"{[]}"</code>:</strong><ul>
<li><code>{</code> push. <code>[</code> push. Stack: <code>['{', '[']</code>.</li>
<li><code>]</code> comes. Pop <code>[</code>. Match <code>mapping[']']</code> (<code>'['</code>). Stack: <code>['{']</code>.</li>
<li><code>}</code> comes. Pop <code>{</code>. Match <code>mapping['}']</code> (<code>'{'</code>). Stack: <code>[]</code>.</li>
<li><code>return not stack</code> evaluates to <code>True</code>. Correct.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability (Minor):</strong> The line <code>top_element = stack.pop() if stack else '#'</code> is Pythonic and efficient. For someone less familiar with this construct, it could be split for explicit clarity, though this is purely stylistic:<pre><code class="language-python">if not stack:
    top_element = '#'
else:
    top_element = stack.pop()
</code></pre>
However, the current one-liner is generally preferred for its conciseness by experienced Python developers.</li>
<li><strong>No significant algorithmic alternatives exist that are more efficient</strong> for this problem. The stack-based approach is standard and optimal in terms of time complexity. One could potentially use a fixed-size array and a pointer instead of a Python list for the stack, but Python's list is highly optimized for append/pop from the end, making it practically O(1) for this use case.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> The code doesn't interact with external systems, files, or user inputs in a way that would introduce common security vulnerabilities (e.g., injection, arbitrary code execution). It's a purely computational algorithm on a given string.</li>
<li><strong>Performance:</strong> The current implementation is highly performant. Its linear time and space complexity means it scales efficiently with the input size. No major performance bottlenecks are present.</li>
</ul>


### Code:
```python
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        stack = []
        mapping = {")": "(", "}": "{", "]": "["}

        for char in s:
            if char in mapping:  # It's a closing bracket
                top_element = stack.pop() if stack else '#' # Use '#' as a placeholder if stack is empty
                if mapping[char] != top_element:
                    return False
            else:  # It's an opening bracket
                stack.append(char)

        return not stack # True if stack is empty, False otherwise
```
