## Longest Valid Parentheses
**Language:** python
**Tags:** python,stack,string,parentheses

### Description:
<p>This code snippet implements a classic algorithm to find the length of the longest valid (well-formed) parentheses substring within a given string.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to determine the maximum length of a substring within the input string <code>s</code> that consists of well-formed parentheses. A well-formed parentheses string means that every opening parenthesis has a matching closing parenthesis, and they are properly nested (e.g., "()", "(())", "()()").</p>
<h3>2. How It Works</h3>
<p>The algorithm uses a single pass through the string with the help of a stack:</p>
<ul>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li><code>max_len</code> is initialized to 0 to store the maximum length found so far.</li>
<li><code>stack</code> is initialized with <code>[-1]</code>. This sentinel value <code>-1</code> acts as a base index, simplifying length calculations and handling cases where a valid sequence starts from the very beginning of the string or after an invalid segment.</li>
</ul>
</li>
<li><p><strong>Iteration</strong>: The code iterates through the input string <code>s</code> character by character, keeping track of both the character <code>char</code> and its index <code>i</code>.</p>
<ul>
<li><strong>If <code>char</code> is <code>'('</code></strong>: The current index <code>i</code> is pushed onto the stack. This records the position of an opening parenthesis that is awaiting a match.</li>
<li><strong>If <code>char</code> is <code>')'</code></strong>:<ol>
<li>The top element is popped from the stack. This represents either matching the most recent opening parenthesis or consuming the base index if no opening parenthesis is available.</li>
<li><strong>If the stack becomes empty after popping</strong>: This signifies that the <code>)</code> either matched the initial <code>-1</code> (meaning no prior <code>(</code> was available for it to match), or it completed a sequence that was delimited by the previous base. In either case, this <code>)</code> cannot extend any previously valid sequence, so its own index <code>i</code> is pushed onto the stack. This <code>i</code> now acts as a new "base" or "wall" for future valid sequence calculations.</li>
<li><strong>If the stack is not empty after popping</strong>: This means the <code>)</code> successfully matched an <code>(</code> whose index was just popped. The element now at the top of the stack (<code>stack[-1]</code>) is either the index of the next unmatched <code>(</code> or the original base <code>-1</code>. The length of the current valid sequence is <code>i - stack[-1]</code>. <code>max_len</code> is updated if this new length is greater.</li>
</ol>
</li>
</ul>
</li>
<li><p><strong>Result</strong>: After processing all characters, <code>max_len</code> holds the length of the longest valid parentheses substring.</p>
</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Stack Data Structure</strong>: A stack is the ideal choice here because it naturally handles the LIFO (Last-In, First-Out) nature of matching parentheses. When an opening parenthesis is encountered, it's pushed; when a closing parenthesis is encountered, it expects to match the <em>most recently</em> opened, unmatched parenthesis.</li>
<li><strong>Sentinel Value (<code>-1</code>) in Stack</strong>:<ul>
<li><strong>Base for Length Calculation</strong>: It provides a fixed reference point. When a valid sequence <code>()</code> is found at indices <code>0, 1</code>, if the stack was <code>[-1, 0]</code>, then <code>1 - stack[-1]</code> (i.e., <code>1 - (-1) = 2</code>) correctly gives the length. Without it, handling the first valid pair starting at index 0 would be more complex.</li>
<li><strong>Delimiting Invalid Sequences</strong>: When an unmatched closing parenthesis <code>)</code> is encountered (i.e., it pops an <code>(</code> and then the stack becomes <code>[-1]</code>, or it directly pops <code>-1</code> when no <code>(</code> is available), it signifies the end of a potential valid sequence or an invalid character. Pushing the current <code>i</code> as the new base effectively "resets" the calculation for future valid sequences, ignoring anything before this <code>i</code>.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The algorithm makes a single pass through the input string <code>s</code> of length N.</li>
<li>Each character is processed once.</li>
<li>Stack operations (push, pop, peek) take O(1) time.</li>
<li>Therefore, the total time complexity is linear with respect to the length of the string.</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li>In the worst-case scenario (e.g., <code>s = "((((("</code>), the stack might store indices for all N opening parentheses.</li>
<li>Thus, the space complexity is proportional to the length of the string.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm correctly handles various edge cases:</p>
<ul>
<li><strong>Empty string (<code>""</code>)</strong>: <code>max_len</code> remains 0. Correct.</li>
<li><strong>String with no valid parentheses (<code>"((("</code>, <code>")))"</code>, <code>"()("</code>)</strong>:<ul>
<li>For <code>(((</code>: Stack grows, <code>max_len</code> stays 0. Correct.</li>
<li>For <code>)))</code>: The stack <code>[-1]</code> gets repeatedly cleared and repopulated with the current index <code>i</code>. <code>max_len</code> stays 0. Correct.</li>
<li>For <code>()(</code>: <code>max_len</code> correctly becomes 2 for "()", and the trailing <code>(</code> doesn't affect <code>max_len</code>. Correct.</li>
</ul>
</li>
<li><strong>Entire string is valid (<code>"(()())"</code>)</strong>: The stack logic correctly calculates <code>max_len</code> as 6.</li>
<li><strong>Valid substrings separated by invalid parts (<code>"()(()())"</code>):</strong> The <code>[-1]</code> sentinel helps delimit and calculate separate valid lengths, correctly identifying the longest one.</li>
<li><strong>Valid substrings concatenated (<code>"()()"</code>):</strong> The algorithm correctly calculates the length. For <code>()()</code>, after the first <code>()</code>, <code>max_len</code> is 2. Then the stack effectively "resets" to <code>[-1]</code> for length calculations related to the subsequent valid sequence, and after the second <code>()</code>, <code>max_len</code> correctly updates to <code>4</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Alternative Approach: Dynamic Programming (DP)</strong><ul>
<li>A DP approach could define <code>dp[i]</code> as the length of the longest valid parentheses substring ending at index <code>i</code>.</li>
<li>This would also be O(N) time and O(N) space. The state transitions can be a bit tricky, often requiring looking back at <code>dp[i-1]</code> and <code>s[i-dp[i-1]-1]</code>.</li>
</ul>
</li>
<li><strong>Alternative Approach: Two Pointers (O(1) Space)</strong><ul>
<li>This approach involves two passes.</li>
<li><strong>First Pass (Left-to-Right)</strong>: Iterate, maintaining <code>open</code> and <code>close</code> counts. If <code>open == close</code>, it's a valid segment, update <code>max_len</code>. If <code>close &gt; open</code>, reset both counts (invalid sequence).</li>
<li><strong>Second Pass (Right-to-Left)</strong>: Similar logic but iterate from right to left to catch valid sequences that might be missed in the first pass (e.g., <code>(()</code>).</li>
<li>This method achieves O(N) time complexity with O(1) space complexity, making it more space-efficient.</li>
</ul>
</li>
</ul>
<p>The current stack-based solution is generally preferred for its clarity and relatively straightforward logic, even if it uses O(N) space.</p>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this code. It processes string input without external dependencies, file I/O, network requests, or privileged operations.</li>
<li><strong>Performance</strong>: The algorithm is highly performant with O(N) time complexity, which is optimal as every character must be examined at least once. The constant factors for stack operations are very small.</li>
</ul>


### Code:
```python
class Solution(object):
    def longestValidParentheses(self, s):
        """
        :type s: str
        :rtype: int
        """
        max_len = 0
        stack = [-1]  # Initialize stack with -1 to handle edge cases and simplify length calculation

        for i, char in enumerate(s):
            if char == '(':
                stack.append(i)
            else:  # char == ')'
                stack.pop()  # Pop the last open parenthesis or the base index
                if not stack:
                    # If stack is empty, it means we've encountered a ')' without a matching '('
                    # or we just popped the initial -1.
                    # This ')' cannot be part of a valid sequence starting before it.
                    # So, we push its index to act as a new base for future calculations.
                    stack.append(i)
                else:
                    # If stack is not empty, the top of the stack is the index of the
                    # most recent unmatched '(' or the base index.
                    # The length of the current valid sequence is i - stack[-1].
                    max_len = max(max_len, i - stack[-1])
        
        return max_len
```
