## Pow (x, n)
**Language:** python
**Tags:** python,math,algorithm,exponentiation,optimization

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements the <code>myPow(x, n)</code> function, which calculates <code>x</code> raised to the power <code>n</code> (i.e., <code>x^n</code>). The primary intent is to provide an efficient way to compute powers, typically in a context where the standard library's <code>pow()</code> or <code>**</code> operator might be disallowed or the underlying algorithm needs to be demonstrated.</p>
<h3>2. How It Works</h3>
<p>The function utilizes the <strong>Exponentiation by Squaring</strong> (also known as Binary Exponentiation) algorithm, adapted to handle various <code>n</code> values:</p>
<ul>
<li><strong>Base Case (n = 0):</strong> If <code>n</code> is 0, <code>x^0</code> is always <code>1.0</code>.</li>
<li><strong>Handle Negative Exponents (n &lt; 0):</strong> If <code>n</code> is negative, it's converted to a positive exponent by changing <code>x</code> to <code>1/x</code> and <code>n</code> to <code>-n</code>. For example, <code>x^-2</code> becomes <code>(1/x)^2</code>.</li>
<li><strong>Iterative Exponentiation:</strong><ul>
<li>It initializes <code>ans</code> (the final result) to <code>1.0</code> and <code>current_x</code> to <code>x</code>.</li>
<li>It then enters a <code>while</code> loop that continues as long as <code>n</code> is greater than 0. In each iteration:<ul>
<li><strong>Check <code>n</code>'s least significant bit:</strong> If <code>n</code> is odd (<code>n % 2 == 1</code>), it means the current power of <code>x</code> (represented by <code>current_x</code>) contributes to the final result, so <code>ans</code> is multiplied by <code>current_x</code>.</li>
<li><strong>Square <code>current_x</code>:</strong> <code>current_x</code> is squared (<code>current_x *= current_x</code>). This effectively prepares <code>current_x</code> to represent <code>x^(2k)</code> if it previously represented <code>x^k</code>.</li>
<li><strong>Halve <code>n</code>:</strong> <code>n</code> is integer-divided by 2 (<code>n //= 2</code>). This moves to the next bit of <code>n</code> (in binary representation).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Return Result:</strong> After the loop completes (when <code>n</code> becomes 0), <code>ans</code> holds the computed <code>x^n</code>.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Exponentiation by Squaring (Binary Exponentiation):</strong><ul>
<li><strong>Pro:</strong> This is the most significant design choice. It dramatically reduces the number of multiplications required compared to a naive O(n) approach. It processes the exponent <code>n</code> bit by bit.</li>
<li><strong>Con:</strong> Can be slightly less intuitive to grasp initially compared to a simple loop for positive <code>n</code>.</li>
</ul>
</li>
<li><strong>Iterative Approach:</strong><ul>
<li><strong>Pro:</strong> Avoids potential recursion depth limits that a recursive solution might encounter for extremely large <code>n</code> values (though Python's recursion limit is quite high for typical competitive programming constraints). It also tends to have slightly better memory performance as it doesn't build up a call stack.</li>
<li><strong>Con:</strong> A recursive implementation might be considered more elegant by some.</li>
</ul>
</li>
<li><strong>In-place Modification for Negative <code>n</code>:</strong><ul>
<li><code>x = 1 / x</code> and <code>n = -n</code> directly modify the input variables.</li>
<li><strong>Pro:</strong> Simple and avoids creating new variables, saving minor memory.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(log |n|)</strong><ul>
<li>The <code>while</code> loop runs approximately <code>log₂|n|</code> times because <code>n</code> is repeatedly halved in each iteration.</li>
<li>Each operation inside the loop (multiplication, modulo, division) is constant time.</li>
<li>Therefore, the overall time complexity is logarithmic with respect to the absolute value of <code>n</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>ans</code>, <code>current_x</code>) regardless of the input size <code>n</code>.</li>
<li>No auxiliary data structures that scale with <code>n</code> are used.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n = 0</code>:</strong> Correctly returns <code>1.0</code>. (<code>x^0 = 1</code>).</li>
<li><strong><code>n = 1</code>:</strong> The loop runs once, <code>ans</code> becomes <code>x</code>, <code>current_x</code> becomes <code>x*x</code>, <code>n</code> becomes <code>0</code>. Returns <code>x</code>. Correct.</li>
<li><strong><code>n &lt; 0</code>:</strong> Handled by converting <code>x</code> to <code>1/x</code> and <code>n</code> to <code>-n</code>, then applying the positive exponent logic. This is mathematically sound.</li>
<li><strong><code>x = 0</code>:</strong><ul>
<li>If <code>n &gt; 0</code>: <code>0^n = 0</code>. The loop will correctly produce <code>0.0</code> as <code>current_x</code> will become <code>0</code> and <code>ans</code> will eventually be multiplied by <code>0</code> (if <code>n</code> is odd at some point) or <code>current_x</code> will already be <code>0</code> before multiplication with <code>ans</code>.</li>
<li>If <code>n &lt; 0</code>: <code>0^-n</code> is <code>1/(0^n)</code>, which involves division by zero. The code correctly results in a <code>ZeroDivisionError</code> when <code>x = 1/x</code> is executed for <code>x=0</code>. This is the expected mathematical behavior for <code>0</code> raised to a negative power.</li>
<li>If <code>n = 0</code>: <code>0^0</code> returns <code>1.0</code>. This is a common mathematical convention, though it can be undefined in some contexts. The code follows this convention.</li>
</ul>
</li>
<li><strong><code>x = 1</code>:</strong> Correctly returns <code>1.0</code> for any <code>n</code> (as <code>1^n = 1</code>).</li>
<li><strong><code>x = -1</code>:</strong> Correctly calculates <code>(-1)^n</code>, alternating between <code>1</code> and <code>-1</code> depending on <code>n</code>'s parity.</li>
<li><strong>Floating-point precision:</strong> While the algorithm is correct, inherent limitations of floating-point arithmetic mean that very large or very small results, or repeated operations, might introduce minor precision errors. This is not an algorithmic flaw but a characteristic of <code>float</code> types.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> For an educational context, adding comments to explain the binary exponentiation logic within the <code>while</code> loop would be beneficial.</li>
<li><strong>Recursive Implementation:</strong> An alternative is a recursive version, which some find more concise:<pre><code class="language-python">class Solution(object):
    def myPowRecursive(self, x, n):
        if n == 0:
            return 1.0
        if n &lt; 0:
            return 1.0 / self.myPowRecursive(x, -n)

        half_pow = self.myPowRecursive(x, n // 2)
        if n % 2 == 0:
            return half_pow * half_pow
        else:
            return half_pow * half_pow * x
</code></pre>
</li>
<li><strong>Built-in Functions:</strong> For production code in Python, using the built-in <code>pow(x, n)</code> function or the <code>x**n</code> operator is generally preferred for clarity, robustness, and often C-optimized performance. This implementation is primarily for understanding the algorithm.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The O(log |n|) time complexity makes this solution highly efficient, especially for large exponents, significantly outperforming a naive O(|n|) multiplication loop.</li>
<li><strong>Floating-point Accuracy:</strong> As mentioned, repeated multiplications with floats can lead to accumulated precision errors, especially for numbers very close to zero or very large. For applications requiring extreme precision, fixed-point arithmetic or arbitrary-precision libraries (e.g., Python's <code>decimal</code> module) would be necessary, but that's beyond the scope of this general float implementation.</li>
<li><strong>Potential for Overflow/Underflow:</strong> For extremely large positive <code>x</code> and <code>n</code>, the result might exceed the maximum representable float value (overflow to <code>inf</code>). For <code>x</code> close to zero and large positive <code>n</code>, the result might underflow to <code>0.0</code>. These are natural behaviors of floating-point numbers.</li>
</ul>


### Code:
```python
class Solution(object):
    def myPow(self, x, n):
        """
        :type x: float
        :type n: int
        :rtype: float
        """
        if n == 0:
            return 1.0

        if n < 0:
            x = 1 / x
            n = -n
        
        ans = 1.0
        current_x = x 

        while n > 0:
            if n % 2 == 1:
                ans *= current_x
            current_x *= current_x
            n //= 2

        return ans
```
