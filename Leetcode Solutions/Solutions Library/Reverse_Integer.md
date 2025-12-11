## Reverse Integer
**Language:** python
**Tags:** python,integer_reversal,overflow_handling,algorithm

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> The code implements a function to reverse the digits of a given 32-bit signed integer.</li>
<li><strong>Core Challenge:</strong> The primary challenge is handling potential integer overflow. If the reversed integer exceeds the maximum or falls below the minimum value for a 32-bit signed integer, the function must return 0.</li>
<li><strong>Context:</strong> This is a very common interview question or LeetCode problem (e.g., "Reverse Integer").</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm proceeds iteratively, extracting digits and reconstructing the reversed number while performing continuous overflow checks:</p>
<ol>
<li><strong>Initialize Limits:</strong> Defines <code>MAX_INT</code> and <code>MIN_INT</code> for a 32-bit signed integer.</li>
<li><strong>Handle Sign:</strong> Determines if the input <code>x</code> is negative and stores this in <code>is_negative</code>. The processing then uses the absolute value of <code>x</code> (<code>temp_x</code>).</li>
<li><strong>Iterative Reversal:</strong> Enters a <code>while</code> loop that continues as long as <code>temp_x</code> is not zero.<ul>
<li><strong>Extract Digit:</strong> Gets the last digit of <code>temp_x</code> using the modulo operator (<code>% 10</code>).</li>
<li><strong>Remove Last Digit:</strong> Removes the last digit from <code>temp_x</code> using integer division (<code>//= 10</code>).</li>
<li><strong>Overflow Check:</strong> <em>Before</em> updating <code>reversed_x</code>, it performs a critical check:<ul>
<li>It compares the current <code>reversed_x</code> against <code>MAX_INT // 10</code> (or <code>abs(MIN_INT) // 10</code> for negative numbers). If <code>reversed_x</code> is already greater than this threshold, it means the next multiplication by 10 will cause an overflow.</li>
<li>If <code>reversed_x</code> is <em>equal</em> to the threshold, it then checks if the <code>digit</code> being added would push it over the limit (<code>MAX_INT % 10</code> or <code>abs(MIN_INT) % 10</code>).</li>
<li>If an overflow is detected, <code>0</code> is returned immediately.</li>
</ul>
</li>
<li><strong>Accumulate Reversed Number:</strong> Updates <code>reversed_x</code> by multiplying it by 10 and adding the extracted <code>digit</code>.</li>
</ul>
</li>
<li><strong>Apply Sign:</strong> After the loop finishes, if the original number was negative, the final <code>reversed_x</code> is negated before being returned. Otherwise, it's returned as is.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Iterative Digit Manipulation:</strong> The core approach is to extract digits mathematically (<code>% 10</code> and <code>// 10</code>) rather than converting the integer to a string.<ul>
<li><strong>Trade-off:</strong> This is generally more performant than string conversion for large numbers (due to string creation and parsing overhead) but requires more manual handling of digits and, critically, overflow.</li>
</ul>
</li>
<li><strong>Pre-emptive Overflow Checks:</strong> The overflow logic is executed <em>before</em> <code>reversed_x</code> is multiplied by 10 and the new digit is added. This is essential for correctness to prevent Python's arbitrary-precision integers from silently growing beyond the 32-bit limit before an explicit check.</li>
<li><strong>Separate Sign Handling:</strong> Processing the absolute value and reapplying the sign at the end simplifies the main loop's logic, avoiding separate positive/negative logic within each iteration besides the overflow checks.</li>
<li><strong>Python's Integer Behavior:</strong> The code correctly acknowledges Python's arbitrary-precision integers by explicitly defining <code>MAX_INT</code> and <code>MIN_INT</code> and implementing the overflow checks, because the <em>problem constraints</em> are typically fixed-width (e.g., 32-bit signed integer), even if Python itself doesn't overflow.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log |x|)</strong><ul>
<li>The number of iterations in the <code>while</code> loop is proportional to the number of digits in <code>x</code>. The number of digits in an integer <code>x</code> is <code>log10(|x|)</code>.</li>
<li>Each operation inside the loop is constant time.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The function uses a fixed number of variables (<code>MAX_INT</code>, <code>MIN_INT</code>, <code>is_negative</code>, <code>temp_x</code>, <code>reversed_x</code>, <code>digit</code>) regardless of the input integer's magnitude.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>x = 0</code>:</strong><ul>
<li><code>temp_x</code> starts as 0. The <code>while temp_x != 0</code> loop condition is immediately false.</li>
<li><code>reversed_x</code> remains 0, and 0 is correctly returned.</li>
</ul>
</li>
<li><strong>Single-Digit Numbers (e.g., <code>x = 5</code>, <code>x = -7</code>):</strong><ul>
<li>The loop runs once. <code>reversed_x</code> becomes the digit (or its absolute value).</li>
<li>The final sign application correctly returns the original single digit.</li>
</ul>
</li>
<li><strong>Numbers Resulting in Overflow (e.g., <code>x = 2147483647</code> (MAX_INT) -&gt; <code>7463847412</code>):</strong><ul>
<li>The pre-emptive overflow checks correctly identify that multiplying <code>reversed_x</code> by 10 and adding the next digit would exceed <code>MAX_INT</code> (or <code>MIN_INT</code> for negative numbers).</li>
<li>The function correctly returns <code>0</code> in these cases.</li>
</ul>
</li>
<li><strong><code>x = -2**31</code> (MIN_INT):</strong><ul>
<li><code>abs(x)</code> is <code>2**31</code>. This is handled correctly because the overflow checks compare against <code>abs(MIN_INT) // 10</code> and <code>abs(MIN_INT) % 10</code>, which are derived from <code>2**31</code>. The reversed version of <code>2147483648</code> is <code>8463847412</code>, which is beyond <code>2**31 - 1</code>, so the overflow condition is triggered for the negative counterpart as well, returning 0.</li>
</ul>
</li>
<li><strong>Trailing Zeros (e.g., <code>x = 120</code> -&gt; <code>21</code>):</strong><ul>
<li>The logic correctly extracts and appends digits. Trailing zeros in the original number become leading zeros in the reversed number, which are naturally dropped by integer representation (e.g., <code>012</code> becomes <code>12</code>).</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Readability Refinement for Overflow Checks:</strong></p>
<ul>
<li>The overflow conditions, while correct, are dense and duplicated for positive and negative cases.</li>
<li><strong>Idea:</strong> Extract the threshold calculation and comparison into a helper method or variables to reduce repetition and improve clarity.</li>
</ul>
<pre><code class="language-python"># Example Refactoring idea
limit_div_10 = MAX_INT // 10 if not is_negative else abs(MIN_INT) // 10
limit_mod_10 = MAX_INT % 10 if not is_negative else abs(MIN_INT) % 10

if reversed_x &gt; limit_div_10:
    return 0
if reversed_x == limit_div_10 and digit &gt; limit_mod_10:
    return 0
</code></pre>
</li>
<li><p><strong>Alternative: String Conversion (Less Recommended for Performance but Simpler to Write)</strong></p>
<ul>
<li><strong>Approach:</strong> Convert the integer to a string, reverse the string, convert back to an integer. Handle the sign explicitly.</li>
</ul>
<pre><code class="language-python">s = str(abs(x))
reversed_s = s[::-1]
result = int(reversed_s)
if x &lt; 0:
    result = -result

# Still requires overflow checks AFTER conversion
if not (MIN_INT &lt;= result &lt;= MAX_INT):
    return 0
return result
</code></pre>
<ul>
<li><strong>Trade-offs:</strong> Easier to write and less prone to off-by-one errors in overflow logic, but typically slower due to string operations and still requires explicit overflow checks against the <code>MAX_INT</code>/<code>MIN_INT</code> constraints.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code has no direct security vulnerabilities. The explicit handling of integer limits is a correctness concern for the problem specification, not a security flaw in Python itself, which handles arbitrary-precision integers by default, preventing traditional integer overflow exploits found in fixed-width languages like C/C++.</li>
<li><strong>Performance:</strong> The current iterative, mathematical approach is highly performant and generally considered the optimal solution for this problem in terms of time and space complexity. The performance is dominated by the number of digits, making it very efficient for typical integer ranges.</li>
</ul>


### Code:
```python
class Solution(object):
    def reverse(self, x):
        """
        :type x: int
        :rtype: int
        """
        MAX_INT = 2**31 - 1
        MIN_INT = -2**31

        is_negative = x < 0
        temp_x = abs(x)
        reversed_x = 0

        while temp_x != 0:
            digit = temp_x % 10
            temp_x //= 10

            if is_negative:
                if reversed_x > abs(MIN_INT) // 10:
                    return 0
                if reversed_x == abs(MIN_INT) // 10 and digit > abs(MIN_INT) % 10:
                    return 0
            else:
                if reversed_x > MAX_INT // 10:
                    return 0
                if reversed_x == MAX_INT // 10 and digit > MAX_INT % 10:
                    return 0
            
            reversed_x = reversed_x * 10 + digit
        
        if is_negative:
            return -reversed_x
        else:
            return reversed_x
```
