## Nth Magical Number
**Language:** python
**Tags:** python,binary search,number theory,inclusion-exclusion

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code aims to find the <em>Nth magical number</em>. A "magical number" is defined as any positive integer that is a multiple of either <code>a</code> or <code>b</code>. The final result should be returned modulo <code>10^9 + 7</code>.</p>
<p>For example, if <code>n=3, a=2, b=3</code>, the magical numbers are <code>2, 3, 4, 6, 8, 9, ...</code>. The 3rd magical number is <code>4</code>.</p>
<h3>2. How It Works</h3>
<p>The core idea is to use binary search on the <em>answer space</em>.</p>
<ol>
<li><p><strong>Helper Functions</strong>:</p>
<ul>
<li><code>_gcd(x, y)</code>: Implements the Euclidean algorithm to find the greatest common divisor (GCD) of <code>x</code> and <code>y</code>.</li>
<li><code>_lcm(x, y)</code>: Calculates the least common multiple (LCM) of <code>x</code> and <code>y</code> using the formula <code>(x * y) // gcd(x, y)</code>.</li>
</ul>
</li>
<li><p><strong>Counting Function (<code>count</code>)</strong>:</p>
<ul>
<li>The key insight is to have a function that can efficiently tell us <em>how many</em> magical numbers are less than or equal to a given number <code>X</code>.</li>
<li>This is achieved using the <strong>inclusion-exclusion principle</strong>:<ul>
<li>Multiples of <code>a</code> up to <code>X</code>: <code>X // a</code></li>
<li>Multiples of <code>b</code> up to <code>X</code>: <code>X // b</code></li>
<li>Multiples of both <code>a</code> and <code>b</code> (i.e., multiples of <code>lcm(a, b)</code>) up to <code>X</code>: <code>X // l</code> (where <code>l</code> is <code>_lcm(a, b)</code>)</li>
<li>The total count is <code>(X // a) + (X // b) - (X // l)</code>. We subtract <code>X // l</code> because numbers divisible by both <code>a</code> and <code>b</code> were counted twice.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Binary Search</strong>:</p>
<ul>
<li><strong>Search Space</strong>:<ul>
<li><code>low</code>: Initialized to <code>1</code>, as the smallest possible magical number is at least <code>min(a,b)</code>.</li>
<li><code>high</code>: Initialized to <code>n * min(a, b)</code>. This provides a sufficiently large upper bound because even in the worst case (where <code>a</code> and <code>b</code> are very different, and <code>n</code> is large), the Nth magical number will not exceed <code>n</code> times the smallest step (<code>min(a,b)</code>). For instance, if <code>a=1</code>, the Nth magical number is <code>n</code>.</li>
</ul>
</li>
<li><strong>Iteration</strong>:<ul>
<li>Calculate <code>mid = (low + high) // 2</code>.</li>
<li>Use the <code>count</code> function to find <code>count_mid</code>, the number of magical numbers up to <code>mid</code>.</li>
<li><strong>If <code>count_mid &gt;= n</code></strong>: This means <code>mid</code> is either our answer or potentially larger than our answer. We record <code>mid</code> as a potential <code>ans</code> and try to find a smaller <code>mid</code> in the range <code>[low, mid - 1]</code>.</li>
<li><strong>If <code>count_mid &lt; n</code></strong>: This means <code>mid</code> is too small; the Nth magical number must be greater than <code>mid</code>. We search in the range <code>[mid + 1, high]</code>.</li>
</ul>
</li>
<li>The loop continues until <code>low &gt; high</code>, at which point <code>ans</code> will hold the smallest number <code>X</code> such that there are at least <code>n</code> magical numbers less than or equal to <code>X</code>.</li>
</ul>
</li>
<li><p><strong>Modulo Operation</strong>: The final <code>ans</code> is returned modulo <code>10**9 + 7</code>.</p>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Binary Search on Answer</strong>: This is the most efficient approach for problems asking for the Nth element where a monotonic <code>count</code> function exists. The <code>count</code> of magical numbers up to <code>X</code> is always non-decreasing with <code>X</code>.</li>
<li><strong>Inclusion-Exclusion Principle</strong>: Essential for correctly counting multiples of <code>a</code> OR <code>b</code>. Without it, multiples of <code>lcm(a, b)</code> would be double-counted.</li>
<li><strong>GCD/LCM Helpers</strong>: Standard mathematical functions efficiently implemented. LCM is crucial for the inclusion-exclusion principle.</li>
<li><strong>Tight Upper Bound for Binary Search</strong>: <code>n * min(a, b)</code> is a good balance. While <code>n * max(a, b)</code> would also work, <code>n * min(a, b)</code> is tighter and reduces the number of binary search iterations.</li>
<li><strong>Modulo at the End</strong>: The problem statement requires the result to be modulo <code>10^9 + 7</code>. This is applied only to the final answer because intermediate calculations (especially the comparisons in binary search and the <code>count</code> values) must operate on the actual magnitudes.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>_gcd</code> and <code>_lcm</code> functions: <code>O(log(min(a, b)))</code> due to the Euclidean algorithm.</li>
<li>Binary search loop: The range <code>[low, high]</code> is <code>O(n * min(a, b))</code>. The number of iterations is <code>O(log(n * min(a, b)))</code>.</li>
<li>Inside the loop: <code>count</code> calculation is <code>O(1)</code> (integer divisions and subtractions).</li>
<li>Overall: <code>O(log(n * min(a, b)))</code>. Given <code>n, a, b</code> can be up to <code>10^9</code>, <code>n * min(a, b)</code> can be <code>10^18</code>. <code>log(10^18)</code> is approximately <code>60</code>, making this extremely fast.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>O(1)</code> auxiliary space, as only a few variables are used.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n=1</code></strong>: The binary search correctly finds <code>min(a, b)</code> as the first magical number.</li>
<li><strong><code>a</code> or <code>b</code> are <code>1</code></strong>: E.g., <code>a=1, b=5</code>. Magical numbers are <code>1, 2, 3, 4, 5, ...</code>. The <code>count</code> function handles this correctly: <code>mid//1 + mid//5 - mid//5 = mid</code>. The binary search finds <code>n</code>.</li>
<li><strong><code>a == b</code></strong>: <code>lcm(a, a)</code> is <code>a</code>. <code>count = (mid // a) + (mid // a) - (mid // a) = mid // a</code>. The logic remains sound.</li>
<li><strong>Large <code>n, a, b</code></strong>: Python's arbitrary-precision integers handle the large values of <code>mid</code>, <code>count</code>, and <code>high</code> (up to <code>10^18</code>) without overflow. The <code>MOD</code> is applied only at the very end.</li>
<li><strong><code>ans = mid; high = mid - 1</code></strong>: This pattern in binary search correctly finds the <em>smallest</em> <code>mid</code> that satisfies the condition <code>count &gt;= n</code>. If the condition is met, <code>mid</code> is a potential answer, but a smaller number might also satisfy it, so we try <code>mid-1</code>. If not met, we need a larger number, so <code>mid+1</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Standard Library Functions</strong>: For Python 3.9+, <code>math.gcd</code> and <code>math.lcm</code> could be used directly, potentially offering minor performance benefits (as they are implemented in C) and reducing boilerplate.<pre><code class="language-python">import math
# ...
# l = math.lcm(a, b)
</code></pre>
</li>
<li><strong>Lower Bound</strong>: The <code>low</code> initialization could be <code>min(a, b)</code> instead of <code>1</code>, as no magical number can be smaller than <code>min(a, b)</code>. This is a micro-optimization and doesn't change the Big-O complexity.</li>
<li><strong>Readability</strong>: The code is already quite readable. Helper functions are well-named. Comments explaining the core <code>count</code> formula could be added for pedagogical clarity, though it's a standard pattern.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The logarithmic time complexity makes this solution highly performant, capable of handling very large inputs within typical time limits. There are no obvious performance bottlenecks.</li>
<li><strong>Security</strong>: No direct security vulnerabilities are present in this algorithmic code. It doesn't interact with external systems, user input (beyond parameters), or sensitive data. The integer arithmetic is robust within Python's capabilities.</li>
</ul>


### Code:
```python
class Solution(object):
    def nthMagicalNumber(self, n, a, b):
        """
        :type n: int
        :type a: int
        :type b: int
        :rtype: int
        """
        MOD = 10**9 + 7

        def _gcd(x, y):
            while y:
                x, y = y, x % y
            return x

        def _lcm(x, y):
            return (x * y) // _gcd(x, y)

        l = _lcm(a, b)

        low = 1
        high = n * min(a, b) 

        ans = 0
        while low <= high:
            mid = (low + high) // 2
            
            count = (mid // a) + (mid // b) - (mid // l)
            
            if count >= n:
                ans = mid
                high = mid - 1
            else:
                low = mid + 1
        
        return ans % MOD
```
