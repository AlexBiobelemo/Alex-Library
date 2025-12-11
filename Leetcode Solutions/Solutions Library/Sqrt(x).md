## Sqrt(x)
**Language:** python
**Tags:** python,binary search,algorithm,math

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: The <code>mySqrt</code> function calculates the integer square root of a non-negative integer <code>x</code>.</li>
<li><strong>Definition</strong>: The integer square root <code>k</code> of <code>x</code> is the largest integer <code>k</code> such that <code>k * k &lt;= x</code>. This is equivalent to <code>floor(sqrt(x))</code>.</li>
<li><strong>Context</strong>: This is a common algorithmic problem, often used to test understanding of search algorithms.</li>
</ul>
<h3>2. How It Works</h3>
<p>The function employs a binary search algorithm to find the integer square root:</p>
<ul>
<li><strong>Base Cases</strong>:<ul>
<li>If <code>x</code> is <code>0</code> or <code>1</code>, its square root is <code>x</code> itself, so it returns <code>x</code> immediately.</li>
</ul>
</li>
<li><strong>Search Space Initialization</strong>:<ul>
<li><code>left</code> is initialized to <code>1</code>, as <code>0</code> is handled and <code>1</code> is the smallest possible positive integer square root.</li>
<li><code>right</code> is initialized to <code>x // 2</code>. This is an effective upper bound because for any <code>x &gt; 1</code>, <code>sqrt(x)</code> will always be less than or equal to <code>x / 2</code>. For example, <code>sqrt(4) = 2</code>, <code>4/2 = 2</code>; <code>sqrt(9) = 3</code>, <code>9/2 = 4.5</code>. A tighter bound helps reduce initial iterations.</li>
<li><code>ans</code> is initialized to <code>0</code> and will store the most recent <code>mid</code> value whose square was less than or equal to <code>x</code>.</li>
</ul>
</li>
<li><strong>Binary Search Loop</strong>:<ul>
<li>The loop continues as long as <code>left</code> is less than or equal to <code>right</code>.</li>
<li><strong>Midpoint Calculation</strong>: <code>mid = left + (right - left) // 2</code> is used to prevent potential integer overflow if <code>left + right</code> were to exceed the maximum integer value (though less critical in Python).</li>
<li><strong>Square Calculation</strong>: <code>square = mid * mid</code> computes the square of the current <code>mid</code> candidate.</li>
<li><strong>Comparison and Adjustment</strong>:<ul>
<li>If <code>square == x</code>: An exact integer square root is found, so <code>mid</code> is returned.</li>
<li>If <code>square &lt; x</code>: <code>mid</code> is a potential candidate (since <code>mid * mid &lt;= x</code>), but we might find a larger one. So, <code>mid</code> is stored in <code>ans</code>, and the search continues in the right half (<code>left = mid + 1</code>).</li>
<li>If <code>square &gt; x</code>: <code>mid</code> is too large. The search continues in the left half (<code>right = mid - 1</code>).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Final Result</strong>: After the loop terminates (when <code>left &gt; right</code>), <code>ans</code> holds the largest <code>mid</code> value whose square was less than or equal to <code>x</code>, which is precisely the integer square root.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm Choice</strong>: Binary Search.<ul>
<li><strong>Justification</strong>: The function <code>f(k) = k * k</code> is monotonically increasing for <code>k &gt;= 0</code>. This property makes binary search an ideal choice for efficiently finding the <code>k</code> that satisfies <code>k * k &lt;= x</code>.</li>
</ul>
</li>
<li><strong>Search Space Bounds</strong>: <code>[1, x // 2]</code>.<ul>
<li><strong>Justification</strong>: This provides a tight and accurate range for the binary search, minimizing the number of iterations required.</li>
</ul>
</li>
<li><strong>Storing Potential Answer (<code>ans</code>)</strong>:<ul>
<li><strong>Justification</strong>: This is crucial for correctly handling cases where <code>x</code> is not a perfect square. The <code>ans</code> variable ensures that we always return the floor of the square root, i.e., the largest integer <code>k</code> such that <code>k^2 &lt;= x</code>.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: <code>O(log x)</code><ul>
<li>The binary search algorithm repeatedly halves the search space. The initial search space size is approximately <code>x / 2</code>. Therefore, the number of operations is logarithmic with respect to <code>x</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(1)</code><ul>
<li>The algorithm uses a fixed number of variables (<code>left</code>, <code>right</code>, <code>mid</code>, <code>ans</code>, <code>square</code>) regardless of the input <code>x</code>. No auxiliary data structures are used that grow with <code>x</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The implementation correctly handles key edge cases:</p>
<ul>
<li><strong><code>x = 0</code></strong>: Returns <code>0</code>. Correct. (<code>0 * 0 = 0</code>).</li>
<li><strong><code>x = 1</code></strong>: Returns <code>1</code>. Correct. (<code>1 * 1 = 1</code>).</li>
<li><strong><code>x = 2</code></strong>:<ul>
<li><code>left=1, right=1</code>.</li>
<li><code>mid=1, square=1</code>. <code>square &lt; x</code>, so <code>ans=1, left=2</code>.</li>
<li>Loop terminates (<code>left &gt; right</code>). Returns <code>ans=1</code>. Correct (<code>floor(sqrt(2)) = 1</code>).</li>
</ul>
</li>
<li><strong><code>x = 3</code></strong>:<ul>
<li><code>left=1, right=1</code>.</li>
<li><code>mid=1, square=1</code>. <code>square &lt; x</code>, so <code>ans=1, left=2</code>.</li>
<li>Loop terminates. Returns <code>ans=1</code>. Correct (<code>floor(sqrt(3)) = 1</code>).</li>
</ul>
</li>
<li><strong><code>x = 4</code> (Perfect Square)</strong>:<ul>
<li><code>left=1, right=2</code>.</li>
<li><code>mid=1, square=1</code>. <code>square &lt; x</code>, so <code>ans=1, left=2</code>.</li>
<li><code>left=2, right=2</code>. <code>mid=2, square=4</code>. <code>square == x</code>, returns <code>2</code>. Correct.</li>
</ul>
</li>
<li><strong>Large <code>x</code></strong>: Python's arbitrary-precision integers prevent overflow issues for <code>mid * mid</code> that might occur in languages with fixed-size integers (e.g., C++, Java) when <code>x</code> is very large (e.g., <code>x</code> close to <code>2^63 - 1</code> and <code>mid</code> close to <code>2^31 - 1</code>). The <code>O(log x)</code> complexity ensures efficient handling of large inputs.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already highly readable, with clear variable names and comments. No significant readability improvements are needed.</li>
<li><strong>Minor Optimization (Theoretical)</strong>: For <code>x=0</code> or <code>x=1</code>, the base case <code>if x &lt; 2: return x</code> is correct. If the upper bound for <code>right</code> was <code>x</code> instead of <code>x // 2</code>, the base case could be <code>if x == 0: return 0</code> and the loop would correctly handle <code>x=1</code>. However, <code>x // 2</code> is a tighter bound for <code>x &gt;= 2</code>.</li>
<li><strong>Alternative Algorithm: Newton's Method</strong>:<ul>
<li><strong>Concept</strong>: An iterative method for finding roots of functions that converges quadratically (very fast). For <code>sqrt(x)</code>, it approximates the root of <code>f(k) = k^2 - x = 0</code>.</li>
<li><strong>Formula</strong>: <code>k_next = (k_current + x / k_current) / 2</code>.</li>
<li><strong>Pros</strong>: Often faster convergence than binary search for floating-point roots.</li>
<li><strong>Cons</strong>: Requires careful handling of initial guess and termination condition for integer square roots, can be slightly more complex to implement correctly for integers.</li>
</ul>
</li>
<li><strong>Python's Built-in Functions</strong>:<ul>
<li>For practical applications, one would use <code>int(math.sqrt(x))</code> or, in Python 3.8+, <code>math.isqrt(x)</code> which is specifically designed for integer square roots and is highly optimized.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(log x)</code> time complexity is highly efficient for calculating square roots. No performance bottlenecks are apparent in this implementation. The use of integer arithmetic is generally faster than floating-point arithmetic.</li>
<li><strong>Security</strong>: This function has no direct security implications. It takes a single integer input and performs a purely mathematical calculation. There are no external dependencies, I/O operations, or interactions with sensitive data that could introduce vulnerabilities.</li>
</ul>


### Code:
```python
class Solution(object):
    def mySqrt(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x < 2:
            return x

        left, right = 1, x // 2
        ans = 0

        while left <= right:
            mid = left + (right - left) // 2
            square = mid * mid

            if square == x:
                return mid
            elif square < x:
                ans = mid  # mid is a potential answer, try larger
                left = mid + 1
            else: # square > x
                right = mid - 1 # mid is too large, try smaller
        
        return ans
```
