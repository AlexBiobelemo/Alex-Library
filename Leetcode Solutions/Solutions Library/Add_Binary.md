## Add Binary
**Language:** python
**Tags:** binary addition,string manipulation,algorithm,two pointers

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a function <code>addBinary</code> that adds two binary numbers represented as strings.</p>
<ul>
<li><strong>Input</strong>: Two strings <code>a</code> and <code>b</code>, each representing a binary number (e.g., "101", "11").</li>
<li><strong>Output</strong>: A string representing the sum of the two binary numbers (e.g., "1000").</li>
<li><strong>Purpose</strong>: To simulate the manual process of binary addition, handling carries and varying input string lengths.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm simulates manual binary addition, processing bits from right to left (least significant to most significant):</p>
<ol>
<li><strong>Initialization</strong>:<ul>
<li>An empty list <code>result</code> is created to store the sum bits.</li>
<li>Pointers <code>i</code> and <code>j</code> are initialized to the last index of strings <code>a</code> and <code>b</code> respectively.</li>
<li>A <code>carry</code> variable is set to <code>0</code>.</li>
</ul>
</li>
<li><strong>Iteration</strong>: The core logic runs in a <code>while</code> loop as long as there are bits remaining in either string or a <code>carry</code> exists.<ul>
<li><code>sum_val</code> is initialized with the current <code>carry</code>.</li>
<li>If <code>i</code> is within bounds, the integer value of <code>a[i]</code> is added to <code>sum_val</code>, and <code>i</code> is decremented.</li>
<li>If <code>j</code> is within bounds, the integer value of <code>b[j]</code> is added to <code>sum_val</code>, and <code>j</code> is decremented.</li>
<li>The current bit of the sum (<code>sum_val % 2</code>) is converted to a string and appended to <code>result</code>.</li>
<li>The new <code>carry</code> (<code>sum_val // 2</code>) is calculated.</li>
</ul>
</li>
<li><strong>Finalization</strong>: After the loop finishes, the <code>result</code> list contains the sum bits in reverse order. It is reversed (<code>[::-1]</code>) and joined into a single string to form the final binary sum.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>String Representation</strong>: Inputs and output are strings, which is standard for this problem, especially to handle potentially very large binary numbers that might exceed standard integer type limits.</li>
<li><strong>Right-to-Left Traversal</strong>: Processing from the least significant bit (rightmost) is fundamental to how addition works, allowing carries to propagate correctly.</li>
<li><strong><code>carry</code> Variable</strong>: Essential for handling overflow from one bit position to the next, mirroring manual addition.</li>
<li><strong>List for <code>result</code></strong>: Appending to a list and then <code>"".join()</code> is an efficient way to build strings incrementally in Python (<code>O(N)</code> total time), rather than repeated string concatenation (<code>O(N^2)</code> total time).</li>
<li><strong>Appending in Reverse</strong>: Building the <code>result</code> list by appending each sum bit means it's constructed from least significant to most significant. A final reversal is needed to present it correctly.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N = len(a)</code> and <code>M = len(b)</code>.</p>
<ul>
<li><strong>Time Complexity</strong>: <code>O(max(N, M))</code><ul>
<li>The <code>while</code> loop iterates at most <code>max(N, M) + 1</code> times (the <code>+1</code> is for a potential final carry).</li>
<li>Inside the loop, operations are constant time (arithmetic, list append).</li>
<li>The final <code>"".join(result[::-1])</code> takes <code>O(max(N, M))</code> time to reverse and join the list.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(max(N, M))</code><ul>
<li>The <code>result</code> list will store <code>max(N, M)</code> or <code>max(N, M) + 1</code> characters.</li>
<li>Other variables (<code>i</code>, <code>j</code>, <code>carry</code>, <code>sum_val</code>) use constant space.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code handles several edge cases gracefully:</p>
<ul>
<li><strong>Strings of Different Lengths</strong>: The <code>if i &gt;= 0</code> and <code>if j &gt;= 0</code> checks correctly handle when one string runs out of bits before the other, effectively treating missing bits as '0'.</li>
<li><strong>Empty Strings</strong>: While typically constraints specify non-empty strings, if empty strings were allowed, <code>len(a)-1</code> would be -1, and the loops would immediately correctly handle them as effectively adding 0.</li>
<li><strong>All Zeroes/Ones</strong>: Handles "0" + "0" correctly giving "0", and "1" + "1" giving "10", or "11" + "1" giving "100".</li>
<li><strong>Final Carry</strong>: The <code>or carry</code> condition in the <code>while</code> loop ensures that if there's a carry-out from the most significant bit, it's correctly appended to the <code>result</code> (e.g., "1" + "1" = "10").</li>
<li><strong>Correct Reversal</strong>: The <code>result[::-1]</code> ensures the bits, which were added from right-to-left, are put in the correct left-to-right order for the final string.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability (Minor)</strong>:<ul>
<li>Could use more descriptive variable names, e.g., <code>bit_a</code>, <code>bit_b</code> instead of just adding to <code>sum_val</code>.</li>
<li>Using <code>divmod(sum_val, 2)</code> can combine the modulo and floor division operations, slightly improving conciseness.</li>
</ul>
</li>
<li><strong>Alternative Implementation (Integer Conversion)</strong>:<ul>
<li>Python has arbitrary-precision integers, so a common alternative is to convert the binary strings to integers, add them, and convert the sum back to a binary string:<pre><code class="language-python">int_a = int(a, 2)
int_b = int(b, 2)
sum_int = int_a + int_b
return bin(sum_int)[2:] # [2:] to strip the "0b" prefix
</code></pre>
</li>
<li>This is often more concise and readable in Python but might be less performant for <em>extremely</em> long strings than the bit-by-bit approach due to the overhead of string parsing and arbitrary-precision arithmetic for very large numbers. For typical competitive programming constraints, it's often faster due to highly optimized built-in functions.</li>
</ul>
</li>
<li><strong>Alternative (Deque for Result)</strong>:<ul>
<li>Instead of a list and then reversing, one could use <code>collections.deque</code> and <code>appendleft</code> to build the result in the correct order directly, avoiding the final <code>[::-1]</code> operation:<pre><code class="language-python">from collections import deque
result_dq = deque()
# ... inside loop ...
result_dq.appendleft(str(sum_val % 2))
# ...
return "".join(result_dq)
</code></pre>
For typical string lengths, the list-append-and-reverse approach is often slightly faster in Python due to lower overhead for <code>list</code> compared to <code>deque</code>.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The current implementation is efficient (linear time and space complexity). For very large inputs, the direct bit-by-bit simulation is generally robust across languages, whereas the <code>int(str, base)</code> approach's performance depends heavily on the language's integer handling (e.g., Python's arbitrary-precision vs. fixed-size integers in C++/Java). In Python, for very, very long strings, the direct simulation might edge out the <code>int()</code> conversion due to the underlying C implementations.</li>
<li><strong>No Security Concerns</strong>: This specific algorithm involves only arithmetic and string manipulation, posing no inherent security vulnerabilities. It doesn't process external data in a way that could lead to injection or other common security flaws.</li>
</ul>


### Code:
```python
class Solution(object):
    def addBinary(self, a, b):
        """
        :type a: str
        :type b: str
        :rtype: str
        """
        result = []
        i, j = len(a) - 1, len(b) - 1
        carry = 0

        while i >= 0 or j >= 0 or carry:
            sum_val = carry

            if i >= 0:
                sum_val += int(a[i])
                i -= 1
            if j >= 0:
                sum_val += int(b[j])
                j -= 1

            result.append(str(sum_val % 2))
            carry = sum_val // 2

        return "".join(result[::-1])
```
