## Palindrome Number
**Language:** python
**Tags:** python,palindrome,integer-manipulation,algorithm

### Description:
<p>This code snippet provides an efficient and clever way to determine if an integer is a palindrome without converting it to a string. It leverages mathematical operations to reverse only half of the number.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: The <code>isPalindrome</code> method checks if a given integer <code>x</code> reads the same forwards and backward.</li>
<li><strong>Definition of Palindrome</strong>: For this problem, a number is considered a palindrome if its digits are symmetrical (e.g., 121, 5, 0). Negative numbers and numbers ending in zero (except 0 itself) are explicitly excluded as palindromes.</li>
<li><strong>Core Constraint</strong>: The implicit challenge often associated with this problem is to solve it <em>without</em> converting the integer to a string. This solution adheres to that constraint.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm proceeds in three main steps:</p>
<ol>
<li><p><strong>Handle Special Cases</strong>:</p>
<ul>
<li>Negative numbers are immediately identified as non-palindromes.</li>
<li>Numbers that end in <code>0</code> (e.g., <code>10</code>, <code>120</code>) are also non-palindromes, unless the number itself is <code>0</code>. This is because a number starting with a non-zero digit cannot end in <code>0</code> and be a palindrome (e.g., <code>021</code> is not <code>120</code>).</li>
</ul>
</li>
<li><p><strong>Reverse Half the Number</strong>:</p>
<ul>
<li>It initializes <code>reverted_number</code> to <code>0</code>.</li>
<li>It enters a <code>while</code> loop that continues as long as <code>x</code> (the original number, which is being progressively shortened from the right) is greater than <code>reverted_number</code> (the reversed half, which is being progressively built).</li>
<li>Inside the loop:<ul>
<li><code>reverted_number = reverted_number * 10 + x % 10</code>: The last digit of <code>x</code> (<code>x % 10</code>) is extracted and appended to <code>reverted_number</code>.</li>
<li><code>x //= 10</code>: The last digit is removed from <code>x</code>.</li>
</ul>
</li>
<li>This loop effectively builds the reversed second half of the number in <code>reverted_number</code> while <code>x</code> retains the first half (or the first half plus the middle digit if the total length is odd). The loop stops when <code>x</code> becomes less than or equal to <code>reverted_number</code>, meaning we've processed roughly half the digits.</li>
</ul>
</li>
<li><p><strong>Final Comparison</strong>:</p>
<ul>
<li><strong>Even Length Palindrome</strong>: If the original number had an even number of digits (e.g., <code>1221</code>), at the end of the loop, <code>x</code> will equal <code>reverted_number</code> (e.g., <code>x</code> becomes <code>12</code>, <code>reverted_number</code> becomes <code>12</code>).</li>
<li><strong>Odd Length Palindrome</strong>: If the original number had an odd number of digits (e.g., <code>12321</code>), at the end of the loop, <code>reverted_number</code> will contain the middle digit, making it slightly larger than <code>x</code> (e.g., <code>x</code> becomes <code>12</code>, <code>reverted_number</code> becomes <code>123</code>). In this case, the middle digit doesn't affect palindromic status, so we compare <code>x</code> with <code>reverted_number // 10</code> (effectively removing the middle digit from <code>reverted_number</code>).</li>
<li>The <code>return x == reverted_number or x == reverted_number // 10</code> statement cleverly handles both scenarios.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Integer Arithmetic Only</strong>: The primary design choice is to avoid string conversion, relying solely on mathematical operations (<code>%</code>, <code>//</code>, <code>*</code>). This often aligns with implicit problem constraints for performance or concept understanding.</li>
<li><strong>Half Reversal Optimization</strong>: Instead of fully reversing the entire number, the algorithm only reverses approximately half of it. This significantly reduces computation and avoids potential integer overflow issues for the reversed number in languages with fixed-size integers.</li>
<li><strong>Early Exit Conditions</strong>: The initial checks for negative numbers and numbers ending in zero (except 0) prune invalid cases efficiently before the main loop, improving average-case performance.</li>
<li><strong>Combined Comparison</strong>: The final <code>or</code> condition for comparison elegantly handles both even and odd-length numbers.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log N)</strong><ul>
<li>Let N be the value of the integer <code>x</code>. The number of digits in <code>x</code> is <code>log10(N)</code>.</li>
<li>The <code>while</code> loop iterates roughly <code>log10(N) / 2</code> times, processing half the digits.</li>
<li>Each operation inside the loop (<code>%</code>, <code>//</code>, <code>*</code>, <code>+</code>) is constant time.</li>
<li>Therefore, the time complexity is proportional to the number of digits, which is <code>O(log N)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>x</code>, <code>reverted_number</code>) regardless of the input integer's magnitude.</li>
<li>This makes its space complexity constant.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>x = 0</code></strong>: The initial check <code>(x % 10 == 0 and x != 0)</code> correctly evaluates to <code>False</code>. The loop <code>while 0 &gt; 0</code> is <code>False</code>. The final return <code>0 == 0 or 0 == 0 // 10</code> evaluates to <code>True</code>. <strong>Correct.</strong></li>
<li><strong>Negative Numbers (e.g., <code>-121</code>)</strong>: The <code>if x &lt; 0</code> condition immediately returns <code>False</code>. <strong>Correct</strong> (as per common problem definition).</li>
<li><strong>Numbers ending in 0 (e.g., <code>10</code>, <code>120</code>)</strong>: The condition <code>(x % 10 == 0 and x != 0)</code> immediately returns <code>False</code>. <strong>Correct</strong> (as a non-zero number starting with non-zero digit cannot end in 0 and be a palindrome).</li>
<li><strong>Single-Digit Numbers (e.g., <code>7</code>)</strong>:<ul>
<li>Initial checks pass.</li>
<li>Loop <code>while 7 &gt; 0</code>: <code>reverted_number</code> becomes <code>7</code>, <code>x</code> becomes <code>0</code>.</li>
<li>Loop terminates <code>while 0 &gt; 7</code> is <code>False</code>.</li>
<li>Final return <code>0 == 7 or 0 == 7 // 10</code> (i.e., <code>0 == 0</code>) returns <code>True</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong>Even Length Palindromes (e.g., <code>1221</code>)</strong>: <code>x</code> becomes <code>12</code>, <code>reverted_number</code> becomes <code>12</code>. <code>x == reverted_number</code> is <code>True</code>. <strong>Correct.</strong></li>
<li><strong>Odd Length Palindromes (e.g., <code>12321</code>)</strong>: <code>x</code> becomes <code>12</code>, <code>reverted_number</code> becomes <code>123</code>. <code>x == reverted_number // 10</code> (i.e., <code>12 == 12</code>) is <code>True</code>. <strong>Correct.</strong></li>
<li><strong>Large Numbers</strong>: Python's integers have arbitrary precision, so <code>x</code> and <code>reverted_number</code> will not overflow. The logic holds.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is quite readable, especially with the clear comments explaining the logic for special cases and the final comparison. No major readability improvements are immediately apparent.</li>
<li><strong>Alternative 1: String Conversion (If Allowed)</strong>:<pre><code class="language-python">def isPalindrome_str(self, x):
    return str(x) == str(x)[::-1]
</code></pre>
This is often the most concise and readable approach if string conversion is permitted. However, it might be slightly less performant for very large numbers due to string conversion overhead, and it fundamentally changes the problem's implicit challenge.</li>
<li><strong>Alternative 2: Full Number Reversal (Generally Less Optimal)</strong>: Fully reverse <code>x</code> into a new number <code>y</code>, then compare <code>x == y</code>. This approach is less optimal than the current one because:<ul>
<li>It performs more operations (reverses <em>all</em> digits, not just half).</li>
<li>In languages with fixed-width integers, reversing a very large number might lead to integer overflow if the reversed number exceeds the maximum integer value. The current solution gracefully avoids this by only reversing half.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The provided solution is highly performant with <code>O(log N)</code> time complexity and <code>O(1)</code> space complexity. It's an optimal integer-based solution for this problem.</li>
<li><strong>Security</strong>: There are no specific security concerns as the code operates purely on an integer input without external dependencies, I/O, or system interactions. It's a self-contained mathematical calculation.</li>
</ul>


### Code:
```python
class Solution(object):
    def isPalindrome(self, x):
        """
        :type x: int
        :rtype: bool
        """
        # Special cases:
        # As discussed, negative numbers are not palindromes.
        # If x is positive and ends with 0, it cannot be a palindrome
        # (e.g., 10, 200). The only exception is 0 itself.
        if x < 0 or (x % 10 == 0 and x != 0):
            return False

        reverted_number = 0
        while x > reverted_number:
            reverted_number = reverted_number * 10 + x % 10
            x //= 10

        # When the length is an odd number, we can get rid of the middle digit by reverted_number // 10
        # For example, when x = 12321, at the end of the loop we have x = 12, and reverted_number = 123
        # Since the middle digit doesn't matter in a palindrome (it's always equal to itself), we can simply
        # remove it from reverted_number.
        return x == reverted_number or x == reverted_number // 10
```
