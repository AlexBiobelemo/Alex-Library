## Valid Number
**Language:** python
**Tags:** string parsing,validation,number parsing,finite state machine

### Description:
This is a classic string parsing problem often found in technical interviews and real-world data validation scenarios. The solution implements a state-machine-like approach using flags to track the valid components of a number.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Goal</strong>: Determine if a given string <code>s</code> represents a valid numeric value according to common interpretations (integers, decimals, and numbers in scientific notation).</li>
<li><strong>Purpose</strong>: This function is crucial for robust input validation, data parsing, and type conversion where flexible numeric string formats are allowed.</li>
<li><strong>Context</strong>: The problem often aims to test understanding of string manipulation, state tracking, and handling various edge cases related to numeric formats.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The code processes the input string character by character, maintaining a set of flags to track the presence and validity of different number components.</p>
<ol>
<li><strong>Preprocessing</strong>:<ul>
<li>Trims leading/trailing whitespace (<code>s.strip()</code>).</li>
<li>Handles an empty string after stripping (returns <code>False</code>).</li>
</ul>
</li>
<li><strong>State Flags</strong>: Initializes <code>seen_digit</code>, <code>seen_dot</code>, and <code>seen_e</code> to <code>False</code> to track if these components have appeared.</li>
<li><strong><code>is_integer</code> Helper</strong>: Defines a nested helper function <code>is_integer(sub_s)</code> to check if a substring represents a valid integer (optionally with a leading sign). This is primarily used for the exponent part.</li>
<li><strong>Main Loop</strong>: Iterates through the (stripped) string:<ul>
<li><strong>Digits (<code>0</code>-<code>9</code>)</strong>: Sets <code>seen_digit = True</code>.</li>
<li><strong>Signs (<code>+</code>, <code>-</code>)</strong>: Valid only if it's the <em>first</em> character or immediately follows an <code>'e'</code> or <code>'E'</code>. Otherwise, <code>False</code>.</li>
<li><strong>Decimal Point (<code>.</code>)</strong>: Valid only if neither a dot nor an exponent <code>e</code>/<code>E</code> has been seen yet. Otherwise, <code>False</code>.</li>
<li><strong>Exponent (<code>e</code>, <code>E</code>)</strong>: Valid only if an <code>e</code>/<code>E</code> hasn't been seen <em>and</em> at least one digit has been seen <em>before</em> it. If valid, <code>seen_e</code> is set to <code>True</code>. Crucially, it then extracts the <em>remainder</em> of the string (<code>s[i+1:]</code>) and immediately returns the result of <code>is_integer()</code> on this substring, as the exponent part <em>must</em> be a valid integer.</li>
<li><strong>Other Characters</strong>: Any character not covered by the above rules makes the string invalid (returns <code>False</code>).</li>
</ul>
</li>
<li><strong>Final Check</strong>: After the loop finishes (meaning no invalid characters were found and no exponent part prematurely returned), it returns <code>seen_digit</code>. This ensures that strings like <code>"."</code>, <code>"+"</code> or <code>""</code> (after stripping) are correctly identified as invalid, as they don't contain any digits.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>State Machine using Flags</strong>: The core design relies on boolean flags (<code>seen_digit</code>, <code>seen_dot</code>, <code>seen_e</code>) to manage the valid sequence and uniqueness of number components. This is a common and effective pattern for string parsing problems with defined rules.</li>
<li><strong>Helper Function for Reusability</strong>: The <code>is_integer</code> helper function abstracts away the logic for validating the integer part of the exponent, making the main loop cleaner and avoiding code duplication.</li>
<li><strong>Early Exits</strong>: The code frequently uses <code>return False</code> as soon as an invalid condition is met. This improves efficiency by not processing the rest of the string unnecessarily.</li>
<li><strong>Immediate Exponent Validation</strong>: When an <code>'e'</code> or <code>'E'</code> is encountered, the rest of the string is immediately passed to <code>is_integer</code>. This simplifies the main loop's logic by offloading the exponent's specific rules.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>s.strip()</code>: O(L), where L is the length of the input string.</li>
<li>Main loop: Iterates up to L times, performing constant-time operations per character.</li>
<li><code>exponent_part = s[i+1:]</code>: String slicing, can take O(L) time in the worst case (if 'e' is near the start).</li>
<li><code>is_integer(sub_s)</code>: Calls <code>sub_s.isdigit()</code>, which iterates over <code>sub_s</code>. In the worst case, <code>sub_s</code> can be O(L).</li>
<li><strong>Overall</strong>: O(L) because even with string slicing and helper calls, each character is effectively processed a constant number of times across all operations.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>s.strip()</code>: May create a new string, O(L) in worst case.</li>
<li>Flags: O(1).</li>
<li><code>exponent_part = s[i+1:]</code>: Creates a new substring, O(L) in worst case.</li>
<li><strong>Overall</strong>: O(L) due to string copying for <code>strip()</code> and the exponent part.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code handles a wide range of edge cases correctly:</p>
<ul>
<li><strong>Empty/Whitespace only</strong>: <code>""</code>, <code>"   "</code> -&gt; <code>False</code> (handled by <code>strip</code> and <code>if not s</code>).</li>
<li><strong>Basic Integers</strong>: <code>"123"</code>, <code>"+1"</code>, <code>"-5"</code> -&gt; <code>True</code>.</li>
<li><strong>Basic Decimals</strong>: <code>"0.1"</code>, <code>".1"</code>, <code>"1."</code>, <code>"-0.1"</code>, <code>"+.1"</code> -&gt; <code>True</code>.<ul>
<li>Correctly handles implicit digits like <code>".1"</code> and <code>"1."</code> due to the final <code>return seen_digit</code>.</li>
</ul>
</li>
<li><strong>Basic Scientific Notation</strong>: <code>"1e1"</code>, <code>"1.e+1"</code>, <code>".1E-1"</code> -&gt; <code>True</code>.</li>
<li><strong>Invalid Characters</strong>: <code>"abc"</code>, <code>"1a"</code>, <code>"1e1a"</code>, <code>"--1"</code> -&gt; <code>False</code> (caught by <code>else</code> clause or sign/exponent rules).</li>
<li><strong>Missing Parts</strong>:<ul>
<li><code>"e1"</code>: <code>False</code> (no digit before <code>e</code>).</li>
<li><code>"1e"</code>: <code>False</code> (exponent part empty, <code>is_integer("")</code> returns <code>False</code>).</li>
<li><code>"."</code>, <code>"+"</code>: <code>False</code> (no digits seen, final <code>seen_digit</code> check).</li>
</ul>
</li>
<li><strong>Multiple Decimals/Exponents</strong>: <code>"1.2.3"</code>, <code>"1e2e3"</code> -&gt; <code>False</code> (caught by <code>seen_dot</code> or <code>seen_e</code> flags).</li>
<li><strong>Incorrect Sign Placement</strong>: <code>"1+2"</code>, <code>"1e+-"</code> -&gt; <code>False</code> (caught by sign rules).</li>
</ul>
<p>The final <code>return seen_digit</code> is crucial for distinguishing between valid numbers like <code>".1"</code> (where <code>seen_digit</code> becomes true after processing '1') and invalid strings like <code>"."</code> (where <code>seen_digit</code> remains false).</p>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability with Explicit States</strong>: While functional, the flag-based approach can become complex to reason about for more intricate parsing. An explicit <strong>Finite State Automaton (FSA)</strong> or state machine implementation (e.g., using an <code>enum</code> for states like <code>START</code>, <code>SIGN</code>, <code>INTEGER</code>, <code>DECIMAL_POINT</code>, <code>DECIMAL_DIGITS</code>, <code>EXPONENT_START</code>, <code>EXPONENT_SIGN</code>, <code>EXPONENT_DIGITS</code>) might make the logic more structured and easier to extend or debug.</li>
<li><strong>Reduced String Slicing</strong>: For extreme performance-critical applications with very long strings, repeatedly slicing <code>s[i+1:]</code> for the exponent part creates temporary strings. One could pass indices to <code>is_integer</code> (e.g., <code>is_integer(s, start_idx, end_idx)</code>) to avoid creating new string objects. However, for typical string lengths, the current approach is fine.</li>
<li><strong>Alternative Approach: Regular Expressions</strong>: For many, a concise regular expression might be preferred for its brevity.<ul>
<li>Example: <code>r"^[+-]?(\d+\.?\d*|\.\d+)([eE][+-]?\d+)?$"</code></li>
<li><strong>Pros</strong>: Very compact.</li>
<li><strong>Cons</strong>: Can be harder to debug, explain the exact rules, or extend with very specific, non-regex-friendly logic. The current manual parser offers more granular control and explicit error conditions.</li>
</ul>
</li>
<li><strong>Python's Built-in Conversion</strong>: While not an exact solution for <code>isNumber</code> (as <code>float()</code> might raise exceptions or handle slightly different rules), one could attempt <code>float(s)</code> within a <code>try-except ValueError</code> block. However, <code>float()</code> can be more lenient (e.g., <code>float("inf")</code>, <code>float("NaN")</code>) or stricter in other ways than the problem's typical definition of "number".</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>No Direct Security Vulnerabilities</strong>: The code operates purely on string input and does not interact with external systems, files, or sensitive data in a way that would introduce common vulnerabilities like injection attacks or path traversals.</li>
<li><strong>Performance Scaling</strong>: The O(L) time complexity means it scales linearly with input size, which is generally efficient. However, for extremely long strings where many temporary string objects are created (due to <code>strip()</code> and <code>s[i+1:]</code>), there could be minor memory overhead or cache misses. This is usually not a significant concern unless dealing with strings in the megabyte range or higher in a very tight loop.</li>
<li><strong>ReDoS (Regular Expression Denial of Service)</strong>: Since this is a manual parser and not using regular expressions, it is inherently immune to ReDoS attacks, which can be a concern with complex regex patterns applied to untrusted input.</li>
</ul>


### Code:
```python
class Solution(object):
    def isNumber(self, s):
        """
        :type s: str
        :rtype: bool
        """
        # 1. Strip leading/trailing whitespace
        s = s.strip()
        if not s:
            return False

        # Flags to track the presence of components
        seen_digit = False
        seen_dot = False
        seen_e = False

        # The part of the string after 'e' or 'E' must be a valid integer
        # We use a helper function for this
        def is_integer(sub_s):
            if not sub_s:
                return False
            
            # Optional sign at the start
            if sub_s[0] in ('+', '-'):
                sub_s = sub_s[1:]
            
            # Must have at least one digit
            return sub_s.isdigit()

        # Iterate through the string character by character
        for i, char in enumerate(s):
            
            # A. Check for Digits
            if '0' <= char <= '9':
                seen_digit = True
            
            # B. Check for Signs ('+' or '-')
            elif char == '+' or char == '-':
                # A sign is only valid at the very start OR immediately after 'e'/'E'
                if i > 0 and s[i-1] not in ('e', 'E'):
                    # Invalid placement: Sign must be first or after exponent symbol
                    return False
                # If it's after 'e' or 'E', the part before 'e'/'E' must exist and be valid, 
                # but our main loop structure handles this implicitly.

            # C. Check for Decimal Point ('.')
            elif char == '.':
                # A dot is only valid if we haven't seen one yet AND haven't seen an 'e'/'E'
                if seen_dot or seen_e:
                    return False
                seen_dot = True
            
            # D. Check for Exponent ('e' or 'E')
            elif char == 'e' or char == 'E':
                # 'e'/'E' is only valid if:
                # 1. We haven't seen one yet.
                # 2. We have seen at least one digit *before* it.
                if seen_e or not seen_digit:
                    return False
                seen_e = True
                
                # After 'e'/'E', the rest of the string *must* be a valid integer
                # We check the remaining part of the string using the helper
                exponent_part = s[i+1:]
                return is_integer(exponent_part) # This immediately determines the final result
            
            # E. Check for any other invalid character
            else:
                return False

        # Final check after the loop: 
        # The number part must have at least one digit to be valid.
        # This handles cases like: "", "+", "-", ".", "+.", ".-"
        return seen_digit
```
