## String to Integer (atoi)
**Language:** python
**Tags:** string parsing,atoi,integer limits,algorithm

### Description:
<p>This code implements the <code>myAtoi</code> function, which converts a string to a 32-bit signed integer according to specific rules, similar to the <code>atoi</code> function in C/C++.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of <code>myAtoi</code> is to parse an input string and extract a numerical value, then convert it to an integer. It must adhere to a set of rules:</p>
<ul>
<li>Ignore leading whitespace.</li>
<li>Identify an optional sign (<code>+</code> or <code>-</code>).</li>
<li>Read digits until a non-digit character or the end of the string is reached.</li>
<li>If no digits are read after the sign, the result is 0.</li>
<li>Clamp the final integer to the range of a 32-bit signed integer <code>[-2^31, 2^31 - 1]</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The function processes the input string <code>s</code> in a sequential, step-by-step manner:</p>
<ul>
<li><strong>1. Whitespace Removal</strong>: It iterates past any leading space characters (<code>' '</code>).</li>
<li><strong>2. Sign Determination</strong>: After whitespace, it checks for an optional <code>'-'</code> or <code>'+'</code> character to determine the sign of the number. If neither is present, the sign defaults to positive. The index <code>i</code> is advanced past the sign character if found.</li>
<li><strong>3. Digit Conversion</strong>: It then iterates through consecutive digit characters. Each digit is converted to an integer and accumulated into the <code>result</code> variable using <code>result = result * 10 + digit</code>. This process continues until a non-digit character is encountered or the end of the string is reached.</li>
<li><strong>4. Sign Application &amp; Clamping</strong>: Once all digits are read, the determined <code>sign</code> (either 1 or -1) is applied to the <code>result</code>. Finally, the <code>result</code> is compared against <code>INT_MAX</code> (2^31 - 1) and <code>INT_MIN</code> (-2^31) and clamped to stay within this 32-bit signed integer range.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Manual Iteration</strong>: The code uses manual <code>while</code> loops and an index <code>i</code> to parse the string character by character. This gives precise control over each rule (whitespace, sign, digits).</li>
<li><strong>Arbitrary Precision Integer</strong>: Python's <code>int</code> type handles arbitrarily large integers. This simplifies the conversion step (step 3) because intermediate <code>result</code> values won't overflow during accumulation, even if they exceed <code>INT_MAX</code> or <code>INT_MIN</code>. The clamping is then applied <em>once</em> at the very end.</li>
<li><strong>Early Exit for No Digits</strong>: The <code>result</code> is initialized to 0, which correctly handles cases where no digits are found after whitespace and an optional sign (e.g., <code>"+"</code>, <code> "   -  "</code>).</li>
<li><strong>Explicit Constants</strong>: <code>INT_MAX</code> and <code>INT_MIN</code> are explicitly defined, making the clamping logic clear and maintainable.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The code iterates through the input string <code>s</code> at most a constant number of times (once for whitespace, once for sign, once for digits). <code>N</code> is the length of the string <code>s</code>. Therefore, the time complexity is directly proportional to the length of the input string.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The function uses a fixed amount of extra space for variables like <code>i</code>, <code>n</code>, <code>sign</code>, <code>result</code>, <code>digit</code>, <code>INT_MAX</code>, and <code>INT_MIN</code>. This space does not grow with the input size <code>N</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code correctly handles several important edge cases:</p>
<ul>
<li><strong>Empty string (<code>""</code>)</strong>: <code>n</code> will be 0, loops won't run, <code>result</code> remains 0. Correct.</li>
<li><strong>String with only whitespace (<code>"   "</code>)</strong>: <code>i</code> advances past spaces, <code>while i &lt; n and s[i].isdigit()</code> is false, <code>result</code> remains 0. Correct.</li>
<li><strong>String with only sign (<code>"+"</code> or <code>"-"</code>)</strong>: <code>i</code> advances past sign, <code>while i &lt; n and s[i].isdigit()</code> is false, <code>result</code> remains 0. Correct.</li>
<li><strong>Leading zeros (<code>"00123"</code>)</strong>: <code>result = result * 10 + digit</code> naturally handles this, accumulating <code>123</code>. Correct.</li>
<li><strong>Numbers out of range (<code>"91283472332"</code>, <code>"-91283472332"</code>)</strong>: The final <code>result</code> is correctly clamped to <code>INT_MAX</code> or <code>INT_MIN</code>. Correct.</li>
<li><strong>Digits followed by non-digits (<code>"123abc"</code>)</strong>: The digit conversion loop stops at the first non-digit character (<code>'a'</code>), returning <code>123</code>. Correct.</li>
<li><strong>Mixed whitespace, sign, and digits (<code>"  -42"</code>)</strong>: All steps are applied sequentially and correctly, yielding <code>-42</code>. Correct.</li>
<li><strong>Invalid character immediately after sign (<code>"+-"</code>)</strong>: The sign is processed, but then <code>s[i]</code> is not a digit, so <code>result</code> remains 0. Correct.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability for Whitespace</strong>: While the current loop is perfectly clear, Python's <code>str.lstrip()</code> could be used to remove leading whitespace more concisely if desired, though it would still require manual indexing afterward for the sign.<pre><code class="language-python">s = s.lstrip()
# Then continue with sign and digit parsing, adjusting 'i' accordingly or re-initializing 's'
</code></pre>
</li>
<li><strong>Early Overflow Detection (for fixed-size integer languages)</strong>: In languages like C++ or Java where <code>int</code>s have fixed sizes, building the <code>result</code> as Python does can lead to overflow <em>during</em> the <code>result = result * 10 + digit</code> operation. A more robust approach in such languages involves checking for potential overflow <em>before</em> the multiplication and addition. For example:<pre><code class="language-python"># Example logic for fixed-size ints, NOT needed in Python directly
if result &gt; INT_MAX // 10 or (result == INT_MAX // 10 and digit &gt; 7):
    return INT_MAX if sign == 1 else INT_MIN
if result &lt; INT_MIN // 10 or (result == INT_MIN // 10 and digit &gt; 8): # for negative
    return INT_MIN
# ... then proceed with result = result * 10 + digit
</code></pre>
However, as noted, Python's arbitrary-precision integers make this specific check unnecessary in the intermediate steps, as the final clamping handles it. The current Python solution is clean and idiomatic for the language.</li>
<li><strong>Built-in <code>int()</code> Function (Partial Alternative)</strong>: Python's <code>int()</code> can convert strings to integers, e.g., <code>int("-42")</code> gives <code>-42</code>. However, it doesn't strictly follow all <code>atoi</code> rules (e.g., <code>int("123abc")</code> raises a <code>ValueError</code>, whereas <code>myAtoi</code> returns <code>123</code>). Therefore, it's not a direct drop-in replacement but a useful tool for basic conversions.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The O(N) time complexity is optimal because the entire relevant part of the string must be read. No significant performance bottlenecks are evident.</li>
<li><strong>Security</strong>: There are no apparent security vulnerabilities like buffer overflows or injection risks. Python's strong typing and memory management inherently mitigate many issues common in lower-level languages. The function only parses data; it doesn't execute or interpret it in a way that could lead to code injection.</li>
</ul>


### Code:
```python
class Solution(object):
    def myAtoi(self, s):
        """
        :type s: str
        :rtype: int
        """
        i = 0
        n = len(s)
        sign = 1
        result = 0

        # 1. Whitespace: Ignore any leading whitespace (" ").
        while i < n and s[i] == ' ':
            i += 1

        # 2. Signedness: Determine the sign by checking if the next character is '-' or '+',
        # assuming positivity if neither present.
        if i < n:
            if s[i] == '-':
                sign = -1
                i += 1
            elif s[i] == '+':
                # sign is already 1, no need to change, just increment i
                i += 1

        # Define min/max for 32-bit signed integer
        INT_MAX = 2**31 - 1
        INT_MIN = -2**31

        # 3. Conversion: Read the integer by skipping leading zeros until a non-digit character
        # is encountered or the end of the string is reached. If no digits were read, then the result is 0.
        # The result initialization to 0 handles the "no digits read" case.
        while i < n and s[i].isdigit():
            digit = int(s[i])
            
            # Python's integers handle arbitrary size, so we can build the full number
            # and then apply the clamping logic at the end.
            result = result * 10 + digit
            i += 1

        # Apply the determined sign
        result *= sign

        # 4. Rounding: If the integer is out of the 32-bit signed integer range [-2^31, 2^31 - 1],
        # then round the integer to remain in the range. Specifically, integers less than -2^31
        # should be rounded to -2^31, and integers greater than 2^31 - 1 should be rounded to 2^31 - 1.
        if result > INT_MAX:
            return INT_MAX
        elif result < INT_MIN:
            return INT_MIN
        else:
            return result
```
