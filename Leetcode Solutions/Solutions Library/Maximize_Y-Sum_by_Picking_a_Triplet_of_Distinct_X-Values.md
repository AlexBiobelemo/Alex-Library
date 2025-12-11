## Maximize Y-Sum by Picking a Triplet of Distinct X-Values
**Language:** python
**Tags:** python,hash map,sorting,greedy algorithm,maximum sum

### Description:
<p>This Python code aims to find the maximum sum of three <code>y</code> values, chosen such that their corresponding <code>x</code> values are distinct.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The function <code>maxSumDistinctTriplet</code> takes two lists, <code>x</code> and <code>y</code>, of equal length. Its goal is to select three items <code>(x[i], y[i])</code>, <code>(x[j], y[j])</code>, and <code>(x[k], y[k])</code> such that:</p>
<ul>
<li>The indices <code>i</code>, <code>j</code>, <code>k</code> are distinct.</li>
<li>The <code>x</code> values <code>x[i]</code>, <code>x[j]</code>, <code>x[k]</code> are distinct.</li>
<li>The sum <code>y[i] + y[j] + y[k]</code> is maximized.</li>
</ul>
<p>The code correctly interprets the requirement for distinct <code>x</code> values by first identifying the maximum <code>y</code> value associated with each unique <code>x</code> value. Then, it finds the sum of the three largest <code>y</code> values from this collection.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm proceeds in several logical steps:</p>
<ol>
<li><strong>Initial Checks</strong>: It first verifies if there are at least three elements in the input lists (<code>n &lt; 3</code>). If not, it's impossible to form a triplet, so it returns -1.</li>
<li><strong>Aggregate Max <code>y</code> per <code>x</code></strong>: It iterates through the <code>x</code> and <code>y</code> lists. For each unique <code>x_val</code>, it stores the <em>maximum</em> <code>y_val</code> encountered so far in a dictionary called <code>max_y_for_x</code>. This ensures that for any given <code>x</code> value, we only consider its best possible <code>y</code> partner.</li>
<li><strong>Extract Values</strong>: All the <code>y</code> values (which are the maximum <code>y</code> for their respective distinct <code>x</code> values) are extracted from the <code>max_y_for_x</code> dictionary into a new list named <code>distinct_x_max_y_values</code>.</li>
<li><strong>Check for Sufficient Distinct <code>x</code> Values</strong>: It then checks if there are at least three unique <code>x</code> values available (by checking the length of <code>distinct_x_max_y_values</code>). If not, it returns -1.</li>
<li><strong>Sort and Sum</strong>: The <code>distinct_x_max_y_values</code> list is sorted in descending order. The sum of the first three elements (the three largest <code>y</code> values corresponding to distinct <code>x</code> values) is then returned as the result.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong><code>max_y_for_x</code> Dictionary</strong>: This is the core data structure. It efficiently maps each unique <code>x</code> value to the largest <code>y</code> value associated with it. This is a crucial step that handles the "distinct <code>x</code> values" requirement, as we only care about the best <code>y</code> for any given <code>x</code>.</li>
<li><strong>Greedy Approach</strong>: By choosing the maximum <code>y</code> for each distinct <code>x</code> and then selecting the top three of these overall, the algorithm implicitly employs a greedy strategy. This is correct because to maximize a sum of three numbers, you should always pick the largest available numbers.</li>
<li><strong>Sorting</strong>: Sorting the list of maximum <code>y</code> values (<code>distinct_x_max_y_values</code>) allows for straightforward selection of the top three largest elements.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li>The initial loop to populate <code>max_y_for_x</code> iterates <code>n</code> times. Dictionary operations (insertion, lookup, update) take <code>O(1)</code> on average. So, this step is <code>O(n)</code>.</li>
<li>Extracting values from the dictionary into a list takes <code>O(D)</code> time, where <code>D</code> is the number of distinct <code>x</code> values (<code>D &lt;= n</code>).</li>
<li>Sorting <code>distinct_x_max_y_values</code> takes <code>O(D log D)</code> time.</li>
<li>Overall, the dominant factor is the sorting step. Since <code>D &lt;= n</code>, the total time complexity is <strong><code>O(n log n)</code></strong>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li>The <code>max_y_for_x</code> dictionary stores up to <code>D</code> key-value pairs.</li>
<li>The <code>distinct_x_max_y_values</code> list stores up to <code>D</code> elements.</li>
<li>In the worst case, all <code>x</code> values are distinct, so <code>D = n</code>. Thus, the space complexity is <strong><code>O(n)</code></strong>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n &lt; 3</code></strong>: Correctly handled by returning -1. Impossible to form a triplet.</li>
<li><strong>Fewer than 3 Distinct <code>x</code> Values</strong>: Correctly handled by checking <code>len(distinct_x_max_y_values) &lt; 3</code> and returning -1. For example, if <code>x = [1, 1, 2], y = [10, 20, 30]</code>, <code>max_y_for_x</code> will be <code>{1: 20, 2: 30}</code>, and <code>distinct_x_max_y_values</code> will have length 2.</li>
<li><strong>All <code>x</code> values are the same</strong>: <code>max_y_for_x</code> will have only one entry. <code>len(distinct_x_max_y_values)</code> will be 1, leading to a correct return of -1.</li>
<li><strong>Duplicate <code>x</code> values with varying <code>y</code> values</strong>: The dictionary ensures only the maximum <code>y</code> value for a given <code>x</code> is retained, which aligns with maximizing the sum.</li>
<li><strong>Negative <code>y</code> values</strong>: The algorithm correctly sums them. If all available <code>y</code> values are negative, the result will be the largest (least negative) possible sum of three.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Performance Optimization (Top-K Selection)</strong>:
Instead of sorting the entire <code>distinct_x_max_y_values</code> list (<code>O(D log D)</code>), we only need the top 3 elements. We can achieve this more efficiently using <code>heapq.nlargest(3, distinct_x_max_y_values)</code>. This function would find the 3 largest elements in <code>O(D log k)</code> time, where <code>k=3</code>. For a fixed <code>k</code>, this simplifies to <code>O(D)</code>.
This would reduce the overall time complexity from <code>O(n log n)</code> to <strong><code>O(n)</code></strong>.</p>
<pre><code class="language-python">import heapq

# ... (code up to distinct_x_max_y_values population) ...

if len(distinct_x_max_y_values) &lt; 3:
    return -1

# Optimized: get top 3 without full sort
top_three_y = heapq.nlargest(3, distinct_x_max_y_values)
return sum(top_three_y)
</code></pre>
</li>
<li><p><strong>Readability</strong>: The current code is quite readable. Variable names are descriptive. No major readability improvements are strictly necessary.</p>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: This code does not interact with external systems, files, or user input in a way that introduces common security vulnerabilities (e.g., injection, sensitive data exposure). Security is not a primary concern for this specific algorithm.</li>
<li><strong>Performance</strong>: The <code>O(n log n)</code> solution is generally acceptable for typical input sizes in competitive programming. However, as noted in "Improvements &amp; Alternatives," an <code>O(n)</code> approach using <code>heapq.nlargest</code> is available if <code>n</code> is very large or strict time limits apply.</li>
</ul>


### Code:
```python
class Solution(object):
    def maxSumDistinctTriplet(self, x, y):
        """
        :type x: List[int]
        :type y: List[int]
        :rtype: int
        """
        n = len(x)

        # If there are fewer than 3 elements, it's impossible to choose three distinct indices.
        if n < 3:
            return -1

        max_y_for_x = {}
        for i in range(n):
            x_val = x[i]
            y_val = y[i]
            if x_val not in max_y_for_x or y_val > max_y_for_x[x_val]:
                max_y_for_x[x_val] = y_val

        distinct_x_max_y_values = list(max_y_for_x.values())

       
        if len(distinct_x_max_y_values) < 3:
            return -1

        distinct_x_max_y_values.sort(reverse=True)

        return distinct_x_max_y_values[0] + distinct_x_max_y_values[1] + distinct_x_max_y_values[2]
```
