## Minimum Index of Two Lists
**Language:** python
**Tags:** python,hash table,list processing,minimum finding

### Description:
<p>This Python code aims to find common restaurant names between two lists such that the sum of their indices in the respective lists is minimized. If multiple common restaurants share this minimum sum, all of them should be returned.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Goal</strong>: Identify restaurant names that appear in both <code>list1</code> and <code>list2</code>.</li>
<li><strong>Condition</strong>: Among these common restaurants, select those for which the sum of their indices (one from <code>list1</code> and one from <code>list2</code>) is the <em>smallest possible</em>.</li>
<li><strong>Output</strong>: A list containing all such common restaurants that achieve this minimum index sum.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm proceeds in two main phases:</p>
<ul>
<li><strong>Phase 1: Preprocessing <code>list1</code> (Building a Hash Map)</strong><ul>
<li>It first iterates through <code>list1</code>, creating a dictionary (<code>list1_map</code>).</li>
<li>This dictionary maps each restaurant name from <code>list1</code> to its corresponding index. This allows for fast lookups later.</li>
</ul>
</li>
<li><strong>Phase 2: Iterating <code>list2</code> and Finding Minimum Sum</strong><ul>
<li>It initializes <code>min_sum</code> to positive infinity and <code>result</code> to an empty list.</li>
<li>It then iterates through <code>list2</code> using an index <code>j</code> and the restaurant name <code>s</code>.</li>
<li>For each <code>s</code> in <code>list2</code>:<ul>
<li>It checks if <code>s</code> exists as a key in <code>list1_map</code> (i.e., if it's a common restaurant).</li>
<li>If <code>s</code> is common:<ul>
<li>It retrieves its index <code>i</code> from <code>list1_map</code>.</li>
<li>It calculates <code>current_sum = i + j</code>.</li>
<li><strong>Comparison and Update</strong>:<ul>
<li>If <code>current_sum</code> is strictly less than <code>min_sum</code>, it means a new, smaller minimum has been found. <code>min_sum</code> is updated, and <code>result</code> is reset to contain only <code>s</code>.</li>
<li>If <code>current_sum</code> is equal to <code>min_sum</code>, it means <code>s</code> also achieves the current minimum sum. <code>s</code> is appended to the <code>result</code> list.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><strong>Final Output</strong>: After iterating through all of <code>list2</code>, the <code>result</code> list contains all restaurants that yielded the minimum index sum.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Hash Map for <code>list1</code> (<code>list1_map</code>)</strong>:<ul>
<li><strong>Decision</strong>: Use a hash map (Python dictionary) to store restaurant names from <code>list1</code> and their indices.</li>
<li><strong>Rationale</strong>: This enables average-case O(1) time complexity for checking if a restaurant from <code>list2</code> is present in <code>list1</code> and retrieving its index. This is crucial for performance compared to a linear scan of <code>list1</code> for each item in <code>list2</code>.</li>
</ul>
</li>
<li><strong>Single Pass for <code>list2</code></strong>:<ul>
<li><strong>Decision</strong>: Iterate through <code>list2</code> only once.</li>
<li><strong>Rationale</strong>: By leveraging the pre-built <code>list1_map</code>, all necessary comparisons and updates can be done efficiently in a single pass.</li>
</ul>
</li>
<li><strong><code>min_sum</code> and <code>result</code> Update Logic</strong>:<ul>
<li><strong>Decision</strong>: Reset <code>result = [s]</code> for a new minimum, but <code>result.append(s)</code> for an equal minimum.</li>
<li><strong>Rationale</strong>: Correctly handles the requirement to return <em>all</em> restaurants that achieve the minimum sum, not just the first one found.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of <code>list1</code> and <code>M</code> be the length of <code>list2</code>.
Assume <code>L_avg</code> is the average length of restaurant names.</p>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li>Building <code>list1_map</code>: O(N) for iterating through <code>list1</code>. Dictionary insertions are O(1) on average. Total: O(N).</li>
<li>Iterating <code>list2</code>: O(M) for the loop. Inside the loop, hash map lookups (<code>s in list1_map</code> and <code>list1_map[s]</code>) are O(1) on average. List appends are O(1) on average. Total: O(M).</li>
<li>Overall Time Complexity: <strong>O(N + M)</strong> (average case). In the worst-case scenario for hash collisions (highly unlikely with Python's dict implementation), lookups could degrade to O(N), making the total O(N*M).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>list1_map</code>: Stores up to <code>N</code> key-value pairs. Each key is a string, and each value is an integer. In terms of number of entries, it's O(N). If string lengths are considered, it's O(N * L_avg).</li>
<li><code>result</code>: In the worst case, all common restaurants could have the same minimum sum. This could be up to <code>min(N, M)</code> restaurants. So, O(min(N, M)).</li>
<li>Overall Space Complexity: <strong>O(N)</strong> (average case, as <code>list1_map</code> usually dominates). If string lengths are factored in, it would be O(N * L_avg).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Lists</strong>:<ul>
<li>If <code>list1</code> is empty, <code>list1_map</code> will be empty. No common restaurants will be found. <code>result</code> remains <code>[]</code>. Correct.</li>
<li>If <code>list2</code> is empty, the loop over <code>list2</code> won't execute. <code>result</code> remains <code>[]</code>. Correct.</li>
<li>If both are empty, <code>result</code> is <code>[]</code>. Correct.</li>
</ul>
</li>
<li><strong>No Common Restaurants</strong>:<ul>
<li>If <code>list1</code> and <code>list2</code> have no restaurant names in common, <code>s in list1_map</code> will always be <code>False</code>. <code>result</code> remains <code>[]</code>. Correct.</li>
</ul>
</li>
<li><strong>Single Common Restaurant</strong>:<ul>
<li>The logic correctly identifies and returns the single common restaurant if it also has the minimum index sum.</li>
</ul>
</li>
<li><strong>Multiple Common Restaurants with Same Minimum Sum</strong>:<ul>
<li>The <code>elif current_sum == min_sum: result.append(s)</code> logic correctly handles this, ensuring all such restaurants are collected.</li>
</ul>
</li>
<li><strong>Indices</strong>: Indices are non-negative, which is implicitly handled by the sum logic and <code>min_sum</code> initialization.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Minor Optimization (Early Exit)</strong>:<ul>
<li>Since indices <code>i</code> and <code>j</code> are always non-negative, if the current index <code>j</code> in <code>list2</code> alone is already greater than or equal to <code>min_sum</code>, then <code>i + j</code> (where <code>i &gt;= 0</code>) can never be less than <code>min_sum</code>. This allows for an early <code>break</code> from the loop over <code>list2</code>.</li>
</ul>
<pre><code class="language-python"># ...
min_sum = float('inf')
result = []

for j, s in enumerate(list2):
    if j &gt;= min_sum: # Optimization: If current list2 index is already too high
        break        # No further restaurants in list2 can yield a smaller sum
        
    if s in list1_map:
        i = list1_map[s]
        current_sum = i + j
        
        if current_sum &lt; min_sum:
            min_sum = current_sum
            result = [s]
        elif current_sum == min_sum:
            result.append(s)
            
return result
</code></pre>
</li>
<li><strong>Alternative if memory is very constrained (less practical for this problem)</strong>:<ul>
<li>Sort both lists and use a two-pointer approach. However, this would involve sorting a list of strings (O(N log N + M log M)) and the "index sum" requirement would become very tricky since indices change after sorting. The current hash map approach is generally superior for this problem due to its optimal time complexity.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The use of Python's built-in dictionary for <code>list1_map</code> is highly optimized and provides excellent average-case performance. The overall O(N+M) time complexity is optimal for this problem, as every element in both lists must be processed at least once in the worst case.</li>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this code. It processes string data internally without external interactions, file I/O, or user input interpretation that would introduce risks like injection attacks or data exposure.</li>
</ul>


### Code:
```python
class Solution:
    def findRestaurant(self, list1: List[str], list2: List[str]) -> List[str]:
        list1_map = {s: i for i, s in enumerate(list1)}
        
        min_sum = float('inf')
        result = []
        
        for j, s in enumerate(list2):
            if s in list1_map:
                i = list1_map[s]
                current_sum = i + j
                
                if current_sum < min_sum:
                    min_sum = current_sum
                    result = [s]
                elif current_sum == min_sum:
                    result.append(s)
                    
        return result
```
