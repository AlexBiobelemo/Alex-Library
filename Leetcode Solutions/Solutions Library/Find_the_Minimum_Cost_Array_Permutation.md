## Find the Minimum Cost Array Permutation
**Language:** python
**Tags:** python,dynamic programming,bitmask,traveling salesman problem

### Description:
<p>This code solves a variation of the Traveling Salesperson Problem (TSP) using a bitmask dynamic programming approach. It aims to find a permutation of nodes <code>0</code> to <code>n-1</code> that minimizes a specific cost function while also being lexicographically smallest among optimal permutations.</p>
<h2>1. Overview &amp; Intent</h2>
<ul>
<li><strong>Problem:</strong> Find a permutation <code>P = [p_0, p_1, ..., p_{n-1}]</code> of <code>[0, 1, ..., n-1]</code> that minimizes the total "score." The score is calculated as the sum of <code>abs(p_i - nums[p_{i+1}])</code> for <code>i</code> from <code>0</code> to <code>n-2</code>, plus <code>abs(p_{n-1} - nums[p_0])</code> to complete a cycle.</li>
<li><strong>Goal:</strong> Return the lexicographically smallest permutation <code>P</code> that achieves this minimum score.</li>
<li><strong>Method:</strong> Employs dynamic programming with bitmasking, a standard technique for TSP-like problems with a small number of nodes.</li>
</ul>
<h2>2. How It Works</h2>
<p>The solution consists of two main phases:</p>
<ol>
<li><p><strong>Minimum Score Calculation (Dynamic Programming):</strong></p>
<ul>
<li>A recursive function <code>get_min_score(mask, last)</code> is defined and memoized using <code>@functools.cache</code>.</li>
<li><code>mask</code>: A bitmask where the <code>i</code>-th bit is set if node <code>i</code> has been visited.</li>
<li><code>last</code>: The index of the last node added to the current partial permutation.</li>
<li><strong>Base Case:</strong> If <code>mask</code> indicates all <code>n</code> nodes have been visited (<code>mask == (1 &lt;&lt; n) - 1</code>), the function returns the cost to close the cycle by returning to node <code>0</code>: <code>abs(last - nums[0])</code>.</li>
<li><strong>Recursive Step:</strong> For a given <code>(mask, last)</code> state, it iterates through all unvisited nodes (<code>next_node</code>). For each <code>next_node</code>, it calculates the <code>current_cost = abs(last - nums[next_node])</code> and recursively calls <code>get_min_score</code> for the new state (<code>mask | (1 &lt;&lt; next_node)</code>, <code>next_node</code>). The function then returns the minimum <code>current_cost + remaining_score</code> found across all <code>next_node</code> choices.</li>
</ul>
</li>
<li><p><strong>Permutation Reconstruction:</strong></p>
<ul>
<li>After the DP table (memoization cache) is filled, the code reconstructs the actual permutation.</li>
<li>It initializes the permutation <code>perm</code> with <code>[0]</code>, as the problem likely assumes starting from <code>0</code> for lexicographical ordering (or it's a common practice to fix a starting node in TSP). <code>visited_mask</code> is set to <code>1</code> (node <code>0</code> visited), and <code>current_node</code> is <code>0</code>.</li>
<li>It loops <code>n-1</code> times to find the remaining <code>n-1</code> nodes.</li>
<li>In each iteration:<ul>
<li>It determines the <code>target_score</code>, which is the minimum possible cost from the current <code>(visited_mask, current_node)</code> state to complete the cycle, retrieved from the cached <code>get_min_score</code> results.</li>
<li>It then iterates through <code>next_node</code> from <code>0</code> to <code>n-1</code> (to ensure lexicographical ordering).</li>
<li>If <code>next_node</code> is unvisited and choosing it leads to the <code>target_score</code> (i.e., <code>abs(current_node - nums[next_node]) + get_min_score(new_mask, next_node) == target_score</code>), then this <code>next_node</code> is part of an optimal path.</li>
<li>It's added to <code>perm</code>, <code>visited_mask</code> and <code>current_node</code> are updated, and the loop <code>break</code>s to pick the smallest possible <code>next_node</code> at this step.</li>
</ul>
</li>
</ul>
</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Bitmask Dynamic Programming:</strong> This is the core algorithm choice for TSP-like problems. It allows representing the set of visited nodes efficiently and exploring all subsets of nodes.</li>
<li><strong>Memoization (<code>@functools.cache</code>):</strong> Essential for DP. It stores the results of <code>get_min_score</code> for each unique <code>(mask, last)</code> state, preventing redundant computations of overlapping subproblems.</li>
<li><strong>Separation of DP and Reconstruction:</strong> A common and good practice. First, compute all optimal values for subproblems, then use those values to build the final solution path.</li>
<li><strong>Lexicographical Ordering Strategy:</strong><ul>
<li>The permutation implicitly starts with <code>0</code>.</li>
<li>During reconstruction, the inner loop iterates <code>next_node</code> from <code>0</code> to <code>n-1</code>. The <code>break</code> statement after finding the <em>first</em> optimal <code>next_node</code> ensures that the smallest possible valid node is chosen at each step, guaranteeing the lexicographically smallest overall permutation among those with minimum cost.</li>
</ul>
</li>
<li><strong>Cost Function:</strong> The <code>abs(last - nums[next_node])</code> defines an asymmetric cost (the cost from <code>A</code> to <code>B</code> is not necessarily the same as <code>B</code> to <code>A</code> because of <code>nums</code>), making it an Asymmetric Traveling Salesperson Problem (ATSP) variant.</li>
</ul>
<h2>4. Complexity</h2>
<p>Let <code>n</code> be the length of <code>nums</code>.</p>
<ul>
<li><strong>Time Complexity:</strong> <code>O(n^2 * 2^n)</code><ul>
<li>The <code>get_min_score</code> function explores <code>n * 2^n</code> unique states (n for <code>last</code>, <code>2^n</code> for <code>mask</code>).</li>
<li>For each state, it iterates <code>n</code> times (for <code>next_node</code>). This involves constant-time operations and cached lookups.</li>
<li>Thus, the DP calculation phase takes <code>O(n * 2^n * n) = O(n^2 * 2^n)</code>.</li>
<li>The reconstruction phase involves an outer loop of <code>n</code> iterations and an inner loop of <code>n</code> iterations, with cached <code>get_min_score</code> calls, making it <code>O(n^2)</code>.</li>
<li>The dominant factor is the DP calculation.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> <code>O(n * 2^n)</code><ul>
<li><code>functools.cache</code> stores the results for <code>n * 2^n</code> states. Each state stores an integer.</li>
<li>The recursion depth for <code>get_min_score</code> is <code>O(n)</code>.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong><code>n = 1</code>:</strong> The loop for reconstruction <code>for _ in range(n - 1)</code> runs 0 times. <code>perm</code> remains <code>[0]</code>. The base case for <code>get_min_score</code> correctly handles the cost <code>abs(0 - nums[0])</code>. The result <code>[0]</code> is correct.</li>
<li><strong><code>nums</code> values:</strong> The values in <code>nums</code> can be any integers, positive or negative, duplicates or unique. The <code>abs()</code> function handles all values correctly. The code only uses <code>nums[index]</code>, where <code>index</code> is a node <code>0...n-1</code>.</li>
<li><strong>Lexicographical Correctness:</strong> The reconstruction strategy of iterating <code>next_node</code> from <code>0</code> to <code>n-1</code> and <code>break</code>ing at the first valid optimal choice is crucial and correct for ensuring the lexicographically smallest permutation.</li>
<li><strong>Guaranteed Cycle:</strong> The base case of <code>get_min_score</code> ensures that the cycle is always closed by returning to node <code>0</code>.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Docstring Clarity:</strong> The <code>nums</code> parameter description "permutation permutation array" is a bit redundant and could be clearer, e.g., "A list of integers defining transition costs for nodes."</li>
<li><strong>Variable Naming:</strong> <code>n</code> could be more descriptive, like <code>num_nodes</code>.</li>
<li><strong>Early Exit in DP:</strong> For <code>get_min_score</code>, if <code>min_score</code> is already 0 (which is the lowest possible cost for an <code>abs()</code> function result), we could potentially break early from the <code>next_node</code> loop if all costs are non-negative. However, since the sum can grow, this is not always applicable.</li>
<li><strong>Performance for Large <code>n</code>:</strong> For <code>n &gt; 20-25</code>, <code>O(n^2 * 2^n)</code> becomes computationally infeasible. For larger instances, one would need to use approximation algorithms (e.g., nearest neighbor heuristic for TSP) or metaheuristics (e.g., genetic algorithms, simulated annealing) which do not guarantee optimality but can find good solutions in polynomial time.</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Performance:</strong> The exponential time and space complexity means this solution is only practical for very small input sizes (<code>n</code>). For typical competitive programming, <code>n</code> is usually limited to about <code>20</code> or <code>22</code> for <code>2^n</code> complexity. Beyond that, it will exceed time limits.</li>
<li><strong>Memory Usage:</strong> The <code>O(n * 2^n)</code> space for memoization can also be substantial for <code>n</code> near 20. <code>20 * 2^20</code> entries, where each entry is a Python integer, can consume several hundred MBs of RAM.</li>
<li><strong>Security:</strong> This code is purely algorithmic and computational. It does not interact with external systems, files, networks, or user input in a way that would introduce common security vulnerabilities. No specific security concerns are applicable beyond general code hygiene.</li>
</ul>


### Code:
```python
import functools
from typing import List

class Solution:
    def findPermutation(self, nums: List[int]) -> List[int]:
        """
        Solves the minimum cost permutation problem using Bitmask DP (TSP variant).
        
        Args:
            nums: A list of integers representing the permutation permutation array.
            
        Returns:
            A list of integers representing the lexicographically smallest permutation
            with the minimum score.
        """
        n = len(nums)
        
        # dp(mask, last) returns the minimum cost to visit the remaining unvisited
        # nodes (indicated by 0 bits in mask) and finally return to node 0.
        # mask: Bitmask representing visited nodes.
        # last: The index of the last node added to the current permutation.
        @functools.cache
        def get_min_score(mask: int, last: int) -> int:
            # Base case: All nodes visited. Close the cycle by returning to 0.
            if mask == (1 << n) - 1:
                return abs(last - nums[0])
            
            min_score = float('inf')
            
            # Try visiting every unvisited node next
            for next_node in range(n):
                if not (mask & (1 << next_node)):
                    # Calculate cost: current transition + rest of the path
                    current_cost = abs(last - nums[next_node])
                    remaining_score = get_min_score(mask | (1 << next_node), next_node)
                    total_score = current_cost + remaining_score
                    
                    if total_score < min_score:
                        min_score = total_score
                        
            return min_score

        # --- Reconstruction Phase ---
        # Start the permutation with 0 (lexicographically smallest start)
        perm = [0]
        visited_mask = 1
        current_node = 0
        
        # We need to find the remaining n-1 numbers
        for _ in range(n - 1):
            # The optimal score we must achieve from the current state
            target_score = get_min_score(visited_mask, current_node)
            
            # Iterate 0 to n-1 to find the lexicographically first valid next step
            for next_node in range(n):
                if not (visited_mask & (1 << next_node)):
                    # Check if picking this node leads to the optimal target_score
                    cost = abs(current_node - nums[next_node])
                    remain = get_min_score(visited_mask | (1 << next_node), next_node)
                    
                    if cost + remain == target_score:
                        perm.append(next_node)
                        visited_mask |= (1 << next_node)
                        current_node = next_node
                        break  # Important: break immediately to ensure lexicographical order
                        
        return perm
```
