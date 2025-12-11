## Rearranging Fruits
**Language:** python
**Tags:** python,oop,greedy,sorting,hashmap

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code aims to find the minimum cost to make two fruit baskets, <code>basket1</code> and <code>basket2</code>, identical in their contents (i.e., having the same multiset of fruits). The cost of swapping a fruit <code>x</code> from <code>basket1</code> with a fruit <code>y</code> from <code>basket2</code> is defined as <code>min(x, y)</code>. The problem assumes <code>basket1</code> and <code>basket2</code> initially have an equal number of fruits.</p>
<h3>2. How It Works</h3>
<p>The solution proceeds in four main stages:</p>
<ol>
<li><p><strong>Feasibility Check &amp; Initialization:</strong></p>
<ul>
<li>Counts the occurrences of each fruit type in <code>basket1</code> and <code>basket2</code> using <code>collections.Counter</code>.</li>
<li>Calculates the total count of each fruit type across both baskets.</li>
<li>Iterates through these total counts: if any fruit type has an odd total count, it's impossible to make the baskets identical (as each basket must have half the total count), so it immediately returns <code>-1</code>.</li>
<li>Identifies <code>min_cost</code>, the smallest numerical value among all fruit types present. This value is crucial for a potential two-step swap strategy.</li>
</ul>
</li>
<li><p><strong>Determine Excess Fruits (P and Q):</strong></p>
<ul>
<li>For each fruit type, it calculates the <code>desired_count</code> (which is <code>total_count // 2</code> since baskets must be identical).</li>
<li><code>P</code>: A list is populated with fruits that <code>basket1</code> has in <em>excess</em> of its <code>desired_count</code>. These fruits conceptually need to leave <code>basket1</code>.</li>
<li><code>Q</code>: Similarly, a list is populated with fruits that <code>basket2</code> has in <em>excess</em> of its <code>desired_count</code>. These fruits conceptually need to leave <code>basket2</code>.</li>
<li>Due to the problem constraint that initial basket sizes are equal and the feasibility check, <code>len(P)</code> will always equal <code>len(Q)</code>. Let this be <code>K</code>.</li>
<li>If <code>K</code> is 0, the baskets are already balanced, and the cost is 0.</li>
</ul>
</li>
<li><p><strong>Sort P and Q for Optimal Pairing:</strong></p>
<ul>
<li>Both lists, <code>P</code> and <code>Q</code>, are sorted in ascending order. This sorting is critical for the greedy strategy applied in the next step.</li>
</ul>
</li>
<li><p><strong>Calculate Minimum Cost:</strong></p>
<ul>
<li>The algorithm iterates <code>K</code> times (where <code>K</code> is the number of fruits that need to be swapped).</li>
<li>In each iteration, it considers a pair: the <code>i</code>-th smallest fruit from <code>P</code> (<code>P[i]</code>) and the <code>i</code>-th largest fruit from <code>Q</code> (<code>Q[K - 1 - i]</code>). This "smallest with largest" pairing is a standard greedy approach for minimizing sums of <code>min(a,b)</code> terms.</li>
<li>For each pair <code>(p_val, q_val)</code>, two swap options are considered:<ul>
<li><strong>Direct Swap:</strong> Swapping <code>p_val</code> from <code>basket1</code> with <code>q_val</code> from <code>basket2</code>. Cost: <code>min(p_val, q_val)</code>.</li>
<li><strong>Two-step Swap (via <code>min_cost</code> intermediary):</strong> Swapping <code>p_val</code> with an instance of <code>min_cost</code> fruit, and <code>q_val</code> with an instance of <code>min_cost</code> fruit. The effective cost for this indirect exchange is <code>2 * min_cost</code>. This strategy is always available because the <code>min_cost</code> fruit type exists and its total count is even, meaning it can be used as an intermediary.</li>
</ul>
</li>
<li>The <code>total_cost</code> is incremented by the minimum of these two options for each pair.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong><code>collections.Counter</code></strong>: Excellent choice for efficiently counting fruit frequencies.</li>
<li><strong>Early Exit for Odd Counts</strong>: The <code>if count % 2 != 0: return -1</code> check is a crucial correctness condition. If the total quantity of any fruit type across both baskets is odd, it's mathematically impossible to divide them equally between two identical baskets.</li>
<li><strong>Separation into <code>P</code> and <code>Q</code></strong>: Clearly delineates which specific fruits are "excess" in each basket and need to be moved.</li>
<li><strong>Greedy Pairing with Sorting</strong>: Sorting <code>P</code> and <code>Q</code> and then pairing the smallest <code>p</code> with the largest <code>q</code> (and so on) is a classic greedy strategy to minimize the sum of <code>min(a,b)</code> values. This is because by pairing extremes, you maximize the chance that the <code>min()</code> operation selects a smaller value, contributing less to the total.</li>
<li><strong>Two-Step Swap Optimization</strong>: The <code>2 * min_cost</code> strategy is a clever insight. If a direct swap between <code>p_val</code> and <code>q_val</code> is expensive (i.e., both <code>p_val</code> and <code>q_val</code> are large), it might be cheaper to perform two indirect swaps, each involving the globally cheapest fruit type (<code>min_cost</code>). This effectively leverages the cheapest resource as an intermediary.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity:</strong></p>
<ul>
<li><code>Counter</code> operations: O(N + M) where N and M are the lengths of <code>basket1</code> and <code>basket2</code> respectively.</li>
<li>Iterating <code>total_counts</code> for feasibility and <code>min_cost</code>: O(U), where U is the number of unique fruit types.</li>
<li>Determining <code>P</code> and <code>Q</code>: O(U + K), where K is the total number of fruits to be swapped (<code>len(P)</code> or <code>len(Q)</code>).</li>
<li>Sorting <code>P</code> and <code>Q</code>: O(K log K). This is generally the dominant factor.</li>
<li>Calculating final cost: O(K).</li>
<li><strong>Overall Time Complexity: O((N + M) + U + K log K). Given U &lt;= N+M and K &lt;= (N+M)/2, this simplifies to O((N + M) log (N + M)) or O(L log L) where L is the total number of fruits.</strong></li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong></p>
<ul>
<li><code>count1</code>, <code>count2</code>, <code>total_counts</code>: O(U) for storing unique fruit frequencies.</li>
<li><code>P</code>, <code>Q</code>: O(K) for storing the lists of excess fruits.</li>
<li><strong>Overall Space Complexity: O(U + K). Given U &lt;= N+M and K &lt;= (N+M), this simplifies to O(N + M) or O(L).</strong></li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Baskets:</strong> If both <code>basket1</code> and <code>basket2</code> are empty, <code>K</code> will be 0, and the code correctly returns <code>0</code>.</li>
<li><strong>Already Balanced:</strong> If baskets are identical initially, <code>P</code> and <code>Q</code> will be empty, <code>K</code> will be 0, and the code correctly returns <code>0</code>.</li>
<li><strong>Impossible to Balance:</strong> If any fruit type has an odd total count, the code correctly returns <code>-1</code> in the feasibility check. This is critical for problem correctness.</li>
<li><strong>Single Fruit Type:</strong> The logic holds even if all fruits are of the same type. For example, <code>basket1 = [5, 5, 5]</code>, <code>basket2 = [5, 5, 5]</code> (assuming equal size constraint). <code>P</code> and <code>Q</code> would be empty, cost 0. If <code>basket1 = [5, 5, 5, 5]</code>, <code>basket2 = [5, 5]</code> (violates equal size, so not a valid input for problem 2524). If <code>basket1 = [5,5,5,1]</code>, <code>basket2 = [5,1,1,1]</code>, <code>P=[5]</code>, <code>Q=[1]</code>. <code>min_cost=1</code>. <code>total_cost = min(min(5,1), 2*1) = min(1,2) = 1</code>. This is correct.</li>
<li><strong>Correctness of <code>len(P) == len(Q)</code>:</strong> This relies on the problem's implicit constraint that <code>len(basket1) == len(basket2)</code>. With this, for every fruit <code>f</code>, if <code>basket1</code> has an excess of <code>f</code>, <code>basket2</code> must have a deficit of <code>f</code> (and vice-versa), ensuring the total number of fruits needing to be moved out of <code>basket1</code> equals the total needing to be moved out of <code>basket2</code>.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is generally very readable due to clear variable names, comments, and logical structuring. No significant readability improvements are needed.</li>
<li><strong>Performance:</strong><ul>
<li>The current approach is optimal for the given constraints. For very large <code>excess</code> values, <code>P.extend([cost] * excess)</code> might create a large temporary list. While Python's <code>extend</code> is optimized, repeatedly appending <code>cost</code> in a loop (<code>for _ in range(excess): P.append(cost)</code>) would be an alternative with possibly different memory characteristics (though likely negligible for typical competitive programming limits).</li>
<li>If fruit costs were within a very small, fixed range, a counting sort could be used instead of <code>P.sort()</code> and <code>Q.sort()</code>, reducing sorting time to O(K + max_cost_value). However, given <code>min_cost</code> can be <code>10^9</code>, this is not applicable here.</li>
</ul>
</li>
<li><strong>Alternative for <code>P</code> and <code>Q</code> representation</strong>: Instead of creating full lists <code>P</code> and <code>Q</code>, one could store <code>Counter</code> objects for excess fruits and iterate through their sorted keys <code>K</code> times. This might slightly reduce memory if the number of <em>distinct</em> excess fruit types is much smaller than <code>K</code>. However, <code>P</code> and <code>Q</code> directly store the <code>K</code> values needed for sorting and pairing, which is perfectly fine.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> No security implications. The code operates purely on in-memory integer lists provided as input, with no external interactions, file I/O, or network communication.</li>
<li><strong>Performance:</strong> The solution's <code>O(L log L)</code> time complexity is efficient and well within typical limits for competitive programming problems where <code>L</code> (total number of fruits) can be up to <code>10^5</code> or <code>10^6</code>. Python's <code>sort()</code> (Timsort) performs well in practice. Memory usage <code>O(L)</code> is also reasonable. For extremely large inputs (e.g., billions of fruits), a different approach might be necessary, but this is beyond typical problem constraints.</li>
</ul>


### Code:
```python
from collections import Counter
from typing import List

class Solution:
    def minCost(self, basket1: List[int], basket2: List[int]) -> int:
        
        # 1. Feasibility Check and Initialization 
        count1 = Counter(basket1)
        count2 = Counter(basket2)
        total_counts = count1 + count2
        
        min_cost = float('inf')
        
        for cost, count in total_counts.items():
            if count % 2 != 0:
                return -1  # Must have an even total count for every cost
            min_cost = min(min_cost, cost)

        # 2. Determine Excess Fruits (P and Q)
        P = []  # Fruits that must leave basket1
        Q = []  # Fruits that must leave basket2
        
        for cost, total_count in total_counts.items():
            desired_count = total_count // 2
            
            # Excess in basket1
            excess1 = count1.get(cost, 0) - desired_count
            if excess1 > 0:
                P.extend([cost] * excess1)
            
            # Excess in basket2
            excess2 = count2.get(cost, 0) - desired_count
            if excess2 > 0:
                Q.extend([cost] * excess2)
        
        K = len(P)
        if K == 0:
            return 0  # Already balanced

        # 3. Sort P and Q for Optimal Pairing
        # We need K swaps. The optimal pairing to minimize the sum of min(p, q) is 
        # smallest P with largest Q, second smallest P with second largest Q, etc.
        P.sort()
        Q.sort()
        
        # 4. Calculate Minimum Cost
        total_cost = 0
        
        # The cost of the two-step swap (via the global minimum) is 2 * m
        two_step_cost = 2 * min_cost
        
        for i in range(K):
            # Optimal pairing for direct swap: P[i] (smallest) with Q[K - 1 - i] (largest)
            p_val = P[i]
            q_val = Q[K - 1 - i]
            
            # The direct swap cost
            direct_cost = min(p_val, q_val)
            
            # The final cost for this single swap is the minimum of the direct swap cost 
            # and the two-step swap cost (2m).
            total_cost += min(direct_cost, two_step_cost)
            
        return total_cost
```
