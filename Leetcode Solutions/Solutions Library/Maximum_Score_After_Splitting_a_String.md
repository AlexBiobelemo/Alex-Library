## Maximum Score After Splitting a String
**Language:** python
**Tags:** python,oop,string,greedy

### Description:
<p>This Python code defines a method <code>maxScore</code> within a <code>Solution</code> class, designed to solve a common string manipulation problem.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given a binary string <code>s</code> (containing only '0's and '1's), the goal is to split it into two non-empty substrings, a left substring and a right substring.</li>
<li><strong>Score Calculation:</strong> The "score" of a split is defined as the number of '0's in the left substring plus the number of '1's in the right substring.</li>
<li><strong>Objective:</strong> Find the maximum possible score among all valid splits.</li>
<li><strong>Example:</strong> For <code>s = "011101"</code>, a split after the first character "0" | "11101" would yield <code>(zeros in "0") + (ones in "11101") = 1 + 3 = 4</code>. The code aims to find the split that maximizes this sum.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a single-pass approach to efficiently calculate and update scores for all possible split points.</p>
<ol>
<li><p><strong>Initialization:</strong></p>
<ul>
<li><code>n</code>: Stores the length of the input string <code>s</code>.</li>
<li><code>right_ones</code>: Initially counts <em>all</em> '1's in the entire string <code>s</code>. This represents the '1's in the right part <em>before</em> any split has occurred (conceptually, <code>s</code> is the right part, and the left part is empty).</li>
<li><code>left_zeros</code>: Starts at <code>0</code>, as the left part is initially empty.</li>
<li><code>max_score</code>: Initialized to <code>0</code>, which is a safe lower bound since scores are non-negative.</li>
</ul>
</li>
<li><p><strong>Iterative Splitting:</strong></p>
<ul>
<li>The code iterates from <code>i = 0</code> to <code>n - 2</code>. Each <code>i</code> represents a potential split point <em>after</em> <code>s[i]</code>. This ensures both the left substring (<code>s[0...i]</code>) and the right substring (<code>s[i+1...n-1]</code>) are non-empty.</li>
<li>In each iteration, <code>s[i]</code> is considered as "moving" from the right part to the left part:<ul>
<li>If <code>s[i]</code> is '0', it means a '0' has moved to the left substring, so <code>left_zeros</code> is incremented.</li>
<li>If <code>s[i]</code> is '1', it means a '1' has moved out of the right substring, so <code>right_ones</code> is decremented.</li>
</ul>
</li>
<li><strong>Score Calculation:</strong> After updating <code>left_zeros</code> and <code>right_ones</code> for the current split (<code>s[0...i]</code> | <code>s[i+1...n-1]</code>), <code>current_score = left_zeros + right_ones</code> is calculated.</li>
<li><strong>Maximum Tracking:</strong> <code>max_score</code> is updated with <code>max(max_score, current_score)</code> to keep track of the highest score found so far.</li>
</ul>
</li>
<li><p><strong>Result:</strong> After iterating through all possible split points, <code>max_score</code> holds the maximum score, which is then returned.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm:</strong><ul>
<li><strong>Single Pass (Linear Scan):</strong> The core decision is to use an iterative approach that updates counts in O(1) time per split point, rather than re-calculating counts for each substring from scratch (which would involve slicing or nested loops).</li>
<li>This is an optimized dynamic programming-like approach where previous calculations inform current ones without storing large intermediate states.</li>
</ul>
</li>
<li><strong>Data Structures:</strong><ul>
<li><strong>Simple Integer Counters:</strong> <code>left_zeros</code>, <code>right_ones</code>, <code>max_score</code> are simple integers. This is highly memory-efficient.</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Efficiency vs. Initial Simplicity:</strong> While a naive approach of slicing the string and calling <code>count()</code> on each substring for every split would be easier to conceptualize, this iterative approach is significantly more performant.</li>
<li><strong>Initial Count:</strong> Using <code>s.count('1')</code> initially for <code>right_ones</code> is efficient in Python as <code>str.count()</code> is implemented in C and very fast. An alternative would be to iterate once to get the total '1's, then the main loop. The chosen method is clean and efficient.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li><code>s.count('1')</code>: Takes O(N) time, where N is the length of the string <code>s</code>.</li>
<li>The <code>for</code> loop iterates <code>N-1</code> times.</li>
<li>Inside the loop, all operations (comparison, increment/decrement, addition, <code>max</code>) are O(1).</li>
<li>Total time complexity is dominated by the initial count and the loop, resulting in O(N) + O(N) = O(N).</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>A fixed number of variables (<code>n</code>, <code>right_ones</code>, <code>left_zeros</code>, <code>max_score</code>, <code>current_score</code>) are used, regardless of the input string's length.</li>
<li>No auxiliary data structures that scale with N are created.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Minimum String Length:</strong> The problem statement typically implies <code>n &gt;= 2</code> because both left and right substrings must be non-empty.<ul>
<li>If <code>n = 2</code> (e.g., "01"): The loop <code>for i in range(2 - 1)</code> runs for <code>i = 0</code>. This is the only valid split ("0" | "1").</li>
<li>If <code>n &lt; 2</code> were allowed (it usually isn't for this problem), the code would return <code>0</code> because <code>range(n-1)</code> would be empty. This would be incorrect as no split is possible.</li>
</ul>
</li>
<li><strong>String of All '0's (e.g., "000"):</strong><ul>
<li><code>right_ones</code> starts at 0.</li>
<li><code>left_zeros</code> increments with each '0' encountered.</li>
<li><code>current_score</code> will be <code>left_zeros</code>. Correctly handles this case.</li>
</ul>
</li>
<li><strong>String of All '1's (e.g., "111"):</strong><ul>
<li><code>right_ones</code> starts at <code>n</code>.</li>
<li><code>left_zeros</code> remains 0.</li>
<li><code>right_ones</code> decrements with each '1' encountered.</li>
<li><code>current_score</code> will be <code>right_ones</code>. Correctly handles this case.</li>
</ul>
</li>
<li><strong>Initial <code>max_score = 0</code>:</strong> This is correct because the minimum possible score is 0 (e.g., for "10", split "1" | "0", score is <code>0 + 0 = 0</code>). Scores are always non-negative, so 0 is a safe initial value.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already quite readable with descriptive variable names and helpful comments. No significant improvements needed here.</li>
<li><strong>Performance:</strong> The current O(N) time and O(1) space complexity is optimal for this problem, as the entire string must be read at least once. No further algorithmic performance improvements are generally possible.</li>
<li><strong>Alternative Initial <code>right_ones</code>:</strong><pre><code class="language-python"># Alternative to s.count('1')
right_ones = sum(1 for char in s if char == '1')
</code></pre>
This is functionally equivalent but <code>s.count()</code> is typically faster for Python strings.</li>
<li><strong>Constraint Handling:</strong> If <code>n &lt; 2</code> <em>were</em> a possible input, adding an explicit check at the beginning would be necessary to handle it (e.g., <code>if n &lt; 2: raise ValueError("String length must be at least 2")</code> or return a specific error/value). However, typical LeetCode problem constraints ensure <code>n &gt;= 2</code>.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no direct security implications. The code operates purely on an input string and performs arithmetic calculations. It does not interact with external systems, files, or user inputs in a way that could introduce vulnerabilities.</li>
<li><strong>Performance:</strong> The solution is highly performant due to its optimal O(N) time complexity and minimal O(1) space usage. For very long strings, this efficiency is crucial. Python's built-in <code>str.count()</code> is optimized at a lower level (C), contributing to the overall speed.</li>
</ul>


### Code:
```python
class Solution:
    def maxScore(self, s: str) -> int:
        n = len(s)
        
        # Initialize right_ones with the total count of '1's in the entire string.
        # As we iterate, '1's moving to the left substring will decrement this count.
        right_ones = s.count('1')
        
        # Initialize left_zeros to 0.
        # As we iterate, '0's moving to the left substring will increment this count.
        left_zeros = 0
        
        # Initialize max_score to a value that will be easily surpassed.
        # Since the minimum possible score is 0 (e.g., "10" -> "1"|"0" -> 0+0=0), 0 is a safe initial value.
        max_score = 0
        
        # Iterate through all possible split points.
        # The split point is after index `i`.
        # The left substring will be s[0...i] and the right substring will be s[i+1...n-1].
        # `i` ranges from 0 to n-2 to ensure both substrings are non-empty.
        for i in range(n - 1):
            # Update counts based on the character s[i] moving from the conceptual right part to the left part.
            if s[i] == '0':
                left_zeros += 1
            else: # s[i] == '1'
                right_ones -= 1
            
            # Calculate the score for the current split.
            current_score = left_zeros + right_ones
            
            # Update the maximum score found so far.
            max_score = max(max_score, current_score)
            
        return max_score
```
