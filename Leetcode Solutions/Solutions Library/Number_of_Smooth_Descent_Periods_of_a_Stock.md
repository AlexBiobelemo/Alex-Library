## Number of Smooth Descent Periods of a Stock
**Language:** python
**Tags:** python,array,counting,greedy algorithm

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> The goal is to find the total number of "smooth descent periods" within a given list of stock <code>prices</code>.</li>
<li><strong>Definition:</strong> A "smooth descent period" is a contiguous sequence of days where the price of each day is exactly one less than the price of the previous day (i.e., <code>prices[i] == prices[i-1] - 1</code>). A single day always counts as a smooth descent period.</li>
<li><strong>Code's Purpose:</strong> The <code>getDescentPeriods</code> method calculates this total count efficiently.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm iterates through the <code>prices</code> list once, maintaining the length of the <em>current</em> smooth descent period and accumulating the total number of periods.</p>
<ol>
<li><strong>Initialization:</strong><ul>
<li><code>total_periods</code> is set to 0, which will store the final count.</li>
<li><code>current_descent_length</code> is set to 0, tracking the length of the smooth descent segment <em>ending at the current day</em>.</li>
</ul>
</li>
<li><strong>Edge Case Handling:</strong><ul>
<li>If the input <code>prices</code> list is empty, it immediately returns <code>0</code> as there are no periods.</li>
</ul>
</li>
<li><strong>Iteration:</strong><ul>
<li>The code iterates through <code>prices</code> using an index <code>i</code> from <code>0</code> to <code>len(prices) - 1</code>.</li>
</ul>
</li>
<li><strong>Descent Check:</strong><ul>
<li>For each <code>prices[i]</code>:<ul>
<li>It checks if it's the <em>first day</em> (<code>i == 0</code>) OR if the current price <code>prices[i]</code> <strong>breaks</strong> the smooth descent sequence from the previous day (<code>prices[i] != prices[i-1] - 1</code>).</li>
<li>If either condition is true, a new smooth descent sequence effectively begins (or <code>i=0</code> is always a start), so <code>current_descent_length</code> is reset to <code>1</code>.</li>
<li>If <code>prices[i]</code> <strong>continues</strong> the smooth descent (<code>prices[i] == prices[i-1] - 1</code>), then <code>current_descent_length</code> is incremented.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Accumulation:</strong><ul>
<li>After determining <code>current_descent_length</code> for the day <code>i</code>, this value is added to <code>total_periods</code>. This is the key insight: if <code>current_descent_length</code> is <code>k</code>, it means there are <code>k</code> new smooth descent periods <em>ending at <code>prices[i]</code></em>. These are: <code>[prices[i]]</code>, <code>[prices[i-1], prices[i]]</code>, ..., <code>[prices[i-k+1], ..., prices[i]]</code>.</li>
</ul>
</li>
<li><strong>Return:</strong><ul>
<li>After iterating through all prices, <code>total_periods</code> holds the final count and is returned.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm:</strong><ul>
<li><strong>Dynamic Programming / Iterative Scan:</strong> The solution uses an iterative, single-pass approach that builds upon the state of the previous element. It's effectively a greedy approach that processes information locally to contribute to a global sum.</li>
<li>It avoids re-calculating lengths of descent periods by simply extending or restarting <code>current_descent_length</code>.</li>
</ul>
</li>
<li><strong>Data Structures:</strong><ul>
<li>Only simple integer variables (<code>total_periods</code>, <code>current_descent_length</code>) are used in addition to the input list.</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Space Efficiency:</strong> By processing elements one by one and only keeping track of the current descent length, the solution achieves optimal O(1) auxiliary space complexity. It avoids storing all sub-periods or using recursion.</li>
<li><strong>Time Efficiency:</strong> A single pass through the array ensures optimal O(N) time complexity.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong> O(N)<ul>
<li>The code iterates through the <code>prices</code> list exactly once, where N is the number of prices.</li>
<li>All operations within the loop (comparisons, assignments, additions) are constant time O(1).</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> O(1)<ul>
<li>The algorithm uses a fixed number of variables (<code>total_periods</code>, <code>current_descent_length</code>, loop index <code>i</code>) regardless of the input list size. No auxiliary data structures that scale with N are employed.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly:</p>
<ul>
<li><strong>Empty list (<code>[]</code>):</strong> Handled by <code>if not prices: return 0</code>. Correct.</li>
<li><strong>Single element list (<code>[5]</code>):</strong><ul>
<li><code>i=0</code>, <code>current_descent_length</code> becomes <code>1</code>. <code>total_periods</code> becomes <code>1</code>. Correct (the period <code>[5]</code>).</li>
</ul>
</li>
<li><strong>List with no smooth descents (<code>[10, 8, 5]</code>):</strong><ul>
<li>Each day is treated as a new descent period of length 1.</li>
<li><code>i=0</code>: <code>len=1</code>, <code>total=1</code> (<code>[10]</code>)</li>
<li><code>i=1</code>: <code>8 != 10-1</code>, <code>len=1</code>, <code>total=1+1=2</code> (<code>[8]</code>)</li>
<li><code>i=2</code>: <code>5 != 8-1</code>, <code>len=1</code>, <code>total=2+1=3</code> (<code>[5]</code>)</li>
<li>Correct.</li>
</ul>
</li>
<li><strong>Perfect smooth descent (<code>[5, 4, 3, 2, 1]</code>):</strong><ul>
<li><code>i=0</code>: <code>len=1</code>, <code>total=1</code></li>
<li><code>i=1</code>: <code>len=2</code>, <code>total=1+2=3</code></li>
<li><code>i=2</code>: <code>len=3</code>, <code>total=3+3=6</code></li>
<li><code>i=3</code>: <code>len=4</code>, <code>total=6+4=10</code></li>
<li><code>i=4</code>: <code>len=5</code>, <code>total=10+5=15</code></li>
<li>Correct. For a sequence of length <code>k</code>, the number of sub-sequences is <code>k * (k + 1) / 2</code>. Here, <code>5 * 6 / 2 = 15</code>.</li>
</ul>
</li>
<li><strong>Mixed sequence (<code>[3, 2, 1, 4, 3, 5]</code>):</strong><ul>
<li><code>i=0</code>: <code>len=1</code>, <code>total=1</code></li>
<li><code>i=1</code>: <code>len=2</code>, <code>total=1+2=3</code></li>
<li><code>i=2</code>: <code>len=3</code>, <code>total=3+3=6</code></li>
<li><code>i=3</code>: <code>len=1</code> (reset), <code>total=6+1=7</code></li>
<li><code>i=4</code>: <code>len=2</code>, <code>total=7+2=9</code></li>
<li><code>i=5</code>: <code>len=1</code> (reset), <code>total=9+1=10</code></li>
<li>Correct.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already very readable. Variable names are clear, and the comments accurately explain the core logic of <code>total_periods += current_descent_length</code>.</li>
<li><strong>Minor Optimization (Syntactic):</strong> The <code>i == 0</code> check is technically covered by the <code>prices[i] != prices[i-1] - 1</code> condition if we imagine a <code>prices[-1]</code> that doesn't satisfy the condition. However, keeping <code>i == 0</code> explicit makes the logic clearer and doesn't impact performance. No significant improvements are needed.</li>
<li><strong>Alternative Approaches:</strong><ul>
<li>While other approaches might involve identifying all maximal smooth descent segments first and then calculating <code>k * (k+1) / 2</code> for each segment, this current single-pass accumulation is more streamlined and directly calculates the sum without intermediate storage. No obvious alternative is significantly better in terms of complexity.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no inherent security vulnerabilities in this code. It performs simple numerical calculations on an input list and does not involve external interactions, user input processing, or sensitive data.</li>
<li><strong>Performance:</strong> The solution is optimally performant with O(N) time and O(1) space complexity. It cannot be asymptotically improved upon as every price point must be inspected at least once.</li>
</ul>


### Code:
```python
class Solution(object):
    def getDescentPeriods(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if not prices:
            return 0

        total_periods = 0
        current_descent_length = 0 

        for i in range(len(prices)):
            # If it's the first day, or the descent period is broken
            if i == 0 or prices[i] != prices[i-1] - 1:
                current_descent_length = 1
            # If the descent period continues
            else:
                current_descent_length += 1
            
            # Add all smooth descent periods ending at the current day
            # If current_descent_length is k, it means there are k periods ending at prices[i]:
            # [prices[i]], [prices[i-1], prices[i]], ..., [prices[i-k+1], ..., prices[i]]
            total_periods += current_descent_length
            
        return total_periods
```
