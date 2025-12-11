## Sum of Beautiful Subsequences
**Language:** python
**Tags:** python,oop,fenwick tree,coordinate compression,inclusion-exclusion

### Description:
<p>This code calculates a "total beauty" for a given list of integers <code>nums</code>. The "beauty" is derived from specific non-decreasing subsequences within <code>nums</code> and their greatest common divisors (GCDs). It employs dynamic programming, a Fenwick Tree (Binary Indexed Tree) for efficient counting, coordinate compression, and the inclusion-exclusion principle.</p>
<h2>1. Overview &amp; Intent</h2>
<ul>
<li><strong>Goal</strong>: Calculate <code>totalBeauty</code>, which is defined as the sum of <code>g * (count of non-decreasing subsequences whose GCD is *exactly* g)</code> for all possible <code>g</code>.</li>
<li><strong>Problem Domain</strong>: This is a combination of number theory (divisors, GCD, inclusion-exclusion) and dynamic programming on subsequences.</li>
<li><strong>Core Idea</strong>:<ul>
<li>First, count non-decreasing subsequences where <em>all elements are multiples of <code>g</code></em> (stored in <code>F[g]</code>).</li>
<li>Then, use inclusion-exclusion to derive the count of non-decreasing subsequences where the GCD of all elements is <em>exactly</em> <code>g</code> (stored in <code>count[g]</code>).</li>
<li>Finally, sum <code>g * count[g]</code> for all <code>g</code>.</li>
</ul>
</li>
</ul>
<h2>2. How It Works</h2>
<p>The solution proceeds in four main stages:</p>
<ol>
<li><strong>Precompute Divisors</strong>:<ul>
<li>Initializes <code>M = max(nums)</code>.</li>
<li>Creates a <code>divs</code> array where <code>divs[j]</code> stores a list of all positive divisors of <code>j</code> for <code>j</code> from 1 to <code>M</code>. This is done efficiently by iterating through potential divisors <code>i</code> and adding <code>i</code> to <code>divs[j]</code> for all its multiples <code>j</code>.</li>
</ul>
</li>
<li><strong>Group Contributions by <code>g</code></strong>:<ul>
<li>Iterates through each number <code>num</code> in the input <code>nums</code> along with its original index <code>i</code>.</li>
<li>For each <code>num</code>, it finds all its divisors <code>g</code> (using the precomputed <code>divs</code> array).</li>
<li>It populates a <code>contributions_for_g</code> dictionary (a <code>defaultdict</code>), where <code>contributions_for_g[g]</code> is a list of <code>(num, original_index)</code> pairs for all numbers <code>num</code> in <code>nums</code> that are multiples of <code>g</code>. The order of these pairs within the list preserves their original relative order in <code>nums</code>.</li>
</ul>
</li>
<li><strong>Calculate <code>F[g]</code> using Fenwick Tree and Coordinate Compression</strong>:<ul>
<li><code>F[g]</code> is designed to store the sum of counts of non-decreasing subsequences <em>whose elements are all multiples of <code>g</code></em>.</li>
<li>Iterates <code>g</code> from 1 to <code>M</code>.</li>
<li>For each <code>g</code>:<ul>
<li>If <code>contributions_for_g[g]</code> is empty, <code>F[g]</code> is 0, continue.</li>
<li><strong>Coordinate Compression</strong>: Extracts unique <code>num</code> values that are multiples of <code>g</code>, sorts them, and maps each unique value to a compressed <code>rank</code> (1-indexed). This makes the Fenwick Tree size proportional to the number of <em>unique</em> values for <code>g</code>, not <code>M</code>.</li>
<li><strong>Fenwick Tree (BIT)</strong>: Initializes a Fenwick Tree <code>tree</code> of size <code>compressed_size + 1</code>.</li>
<li><strong>Dynamic Programming</strong>: Iterates through <code>(num, original_index)</code> pairs in <code>contributions_for_g[g]</code> (maintaining original relative order).<ul>
<li><code>rank = val_to_rank[num]</code>.</li>
<li><code>count_less = query(rank - 1)</code>: Queries the BIT to get the total count of non-decreasing subsequences ending with a value <em>less than</em> the current <code>num</code> (and also multiples of <code>g</code>).</li>
<li><code>count_g = (1 + count_less)</code>: This represents all non-decreasing subsequences ending with the current <code>num</code>. The <code>1</code> accounts for the subsequence <code>[num]</code> itself.</li>
<li><code>F[g] = (F[g] + count_g)</code>: Accumulates these counts into <code>F[g]</code>.</li>
<li><code>update(rank, count_g)</code>: Updates the BIT at <code>rank</code> by adding <code>count_g</code>. This makes <code>count_g</code> available for subsequent numbers in <code>contributions_for_g[g]</code> that are greater than or equal to <code>num</code>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><strong>Inclusion-Exclusion and Final Sum</strong>:<ul>
<li>Initializes <code>count</code> array of size <code>M + 1</code> to store the <em>exact</em> counts for each <code>g</code>.</li>
<li>Iterates <code>g</code> from <code>M</code> down to 1 (this reverse order is crucial for inclusion-exclusion).</li>
<li>If <code>F[g]</code> is 0, skip.</li>
<li><code>count[g] = F[g]</code>: Initialize <code>count[g]</code> with the total count of subsequences whose elements are <em>multiples of <code>g</code></em>.</li>
<li><strong>Subtract Multiples</strong>: For each multiple <code>multiple = 2*g, 3*g, ...</code> up to <code>M</code>: <code>count[g] = (count[g] - count[multiple] + MOD) % MOD</code>. This subtracts the counts of subsequences whose GCD is actually <code>2g</code>, <code>3g</code>, etc., which were implicitly included in <code>F[g]</code>. Since we iterate <code>g</code> downwards, <code>count[multiple]</code> (e.g., <code>count[2g]</code>) would have already been precisely calculated.</li>
<li><code>beauty = (g * count[g]) % MOD</code>: Calculates the beauty contribution for <code>g</code>.</li>
<li><code>total_beauty = (total_beauty + beauty) % MOD</code>: Accumulates to the final result.</li>
</ul>
</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Precomputing Divisors (<code>divs</code>)</strong>: This is a standard optimization in number theory problems. It avoids repeatedly calculating divisors for each number, improving efficiency when many numbers need their divisors found.</li>
<li><strong>Fenwick Tree (BIT)</strong>: Used within the <code>F[g]</code> calculation to efficiently count non-decreasing subsequences. For each <code>g</code>, it allows <code>O(log K)</code> updates and <code>O(log K)</code> queries (where <code>K</code> is the number of unique values for that <code>g</code>), which is much faster than an <code>O(K)</code> linear scan for each element in a naive DP approach.</li>
<li><strong>Coordinate Compression</strong>: Applied before using the Fenwick Tree for each <code>g</code>. This is vital because the actual values in <code>nums</code> can be large, but the <em>number</em> of unique values that are multiples of a specific <code>g</code> might be much smaller than <code>M</code>. Coordinate compression reduces the size of the Fenwick Tree to <code>O(number_of_unique_values)</code>, making it practical.</li>
<li><strong>Inclusion-Exclusion Principle</strong>: The core mathematical technique for deriving <code>count[g]</code> (subsequences with <em>exactly</em> GCD <code>g</code>) from <code>F[g]</code> (subsequences with GCD <em>a multiple of <code>g</code></em>). Iterating <code>g</code> from <code>M</code> down to 1 is critical for this principle to work correctly, as it ensures that when <code>count[g]</code> is being calculated, all <code>count[multiple_of_g]</code> values (where <code>multiple_of_g &gt; g</code>) are already final.</li>
<li><strong><code>collections.defaultdict(list)</code></strong>: A convenient and efficient way to group <code>(num, index)</code> pairs by their common divisor <code>g</code>. It handles automatic list creation for new keys.</li>
<li><strong>Modulo Arithmetic (<code>MOD</code>)</strong>: Used extensively to prevent integer overflow, which is common in counting problems with large intermediate sums.</li>
</ul>
<h2>4. Complexity</h2>
<p>Let <code>N</code> be the length of <code>nums</code> and <code>M</code> be the maximum value in <code>nums</code>.</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><strong>Precompute Divisors</strong>: <code>O(M log M)</code>. This is due to the harmonic series sum <code>M/1 + M/2 + ... + M/M</code>.</li>
<li><strong>Group Contributions</strong>: For each <code>num</code> in <code>nums</code>, we iterate its divisors. The total number of divisors across all numbers up to <code>M</code> is <code>sum(tau(k))</code> for <code>k=1..M</code>, where <code>tau(k)</code> is the number of divisors of <code>k</code>. The maximum number of divisors for any <code>k &lt;= 10^5</code> is 128 (for 75600 or 92400). In the worst case, this could be roughly <code>O(N * tau_max)</code>, where <code>tau_max</code> is the maximum number of divisors for any <code>num</code> in <code>nums</code>. More precisely, <code>sum_{i=1 to N} tau(nums[i])</code>. Let <code>S_tau = sum_{i=1 to N} tau(nums[i])</code>. This phase is <code>O(S_tau)</code>.</li>
<li><strong>Calculate <code>F[g]</code></strong>:<ul>
<li>Outer loop runs <code>M</code> times.</li>
<li>Inside, for each <code>g</code>, processing <code>contributions_for_g[g]</code> (which has <code>K_g</code> elements):<ul>
<li>Building <code>unique_vals</code> and <code>val_to_rank</code> involves sorting <code>K_g</code> elements: <code>O(K_g log K_g)</code>.</li>
<li>Fenwick Tree operations: There are <code>K_g</code> updates and queries. Each BIT operation is <code>O(log compressed_size)</code>, where <code>compressed_size &lt;= N</code>.</li>
</ul>
</li>
<li>Summing over all <code>g</code>, the total number of BIT operations is <code>S_tau</code>. So this phase is <code>O(S_tau * log N)</code>.</li>
</ul>
</li>
<li><strong>Inclusion-Exclusion</strong>: Outer loop <code>M</code> times. Inner <code>while</code> loop runs <code>M/g</code> times for each <code>g</code>. Total <code>sum_{g=1 to M} M/g = O(M log M)</code>.</li>
<li><strong>Overall Time Complexity</strong>: <code>O(M log M + S_tau * log N)</code>. Given typical constraints (<code>N, M &lt;= 10^5</code>), <code>S_tau</code> is often dominated by <code>N * log M</code> in the average case, making it roughly <code>O(M log M + N log M log N)</code>. <code>S_tau</code> can be <code>N * 128</code> in the worst case for <code>M &lt;= 10^5</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>divs</code>: <code>O(M log M)</code> in total (sum of lengths of all divisor lists).</li>
<li><code>contributions_for_g</code>: <code>O(S_tau)</code> total pairs stored.</li>
<li><code>F</code> and <code>count</code> arrays: <code>O(M)</code>.</li>
<li>Fenwick Tree (<code>tree</code>), <code>unique_vals</code>, <code>val_to_rank</code>: Max <code>O(N)</code> elements (for a single <code>g</code>).</li>
<li><strong>Overall Space Complexity</strong>: <code>O(M log M + S_tau)</code>.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong>Empty <code>nums</code></strong>: Handled by <code>if not nums: return 0</code>. Correct.</li>
<li><strong>Single element <code>nums</code></strong>: <code>M</code> would be that element. <code>divs</code> would correctly list its divisors. <code>F[g]</code> would be 1 for <code>g</code> dividing <code>M</code>. Inclusion-exclusion would correctly yield <code>count[M] = 1</code>, and <code>total_beauty = M</code>. Correct.</li>
<li><strong>Duplicate numbers in <code>nums</code></strong>: The Fenwick Tree approach with coordinate compression correctly handles duplicates. If multiple identical numbers appear at different original indices, they will map to the same rank, and each <code>update</code> operation will add to the BIT, correctly counting subsequences involving those duplicates.</li>
<li><strong>Non-decreasing subsequences</strong>: The Fenwick Tree <code>query(rank - 1)</code> correctly counts subsequences ending with elements strictly less than the current <code>num</code>. When <code>1 + count_less</code> is added, it correctly forms new subsequences, implicitly ensuring the non-decreasing property when processing numbers in their original order.</li>
<li><strong>Modulo Arithmetic</strong>: Used consistently throughout calculations involving sums, preventing overflow for large counts.</li>
<li><strong>Inclusion-Exclusion Order</strong>: The iteration <code>g</code> from <code>M</code> down to 1 for inclusion-exclusion is critical and correctly implemented.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Fenwick Tree as a separate class/function</strong>: The <code>query</code> and <code>update</code> functions are nested locally. While functional, encapsulating them into a reusable <code>FenwickTree</code> class or as <code>Solution</code> methods could improve modularity and readability if this pattern is used elsewhere.</li>
<li><strong>Clarity of <code>contributions_for_g</code></strong>: The current structure means that for <code>g</code>, <code>contributions_for_g[g]</code> contains <code>(num, original_index)</code> pairs in the order they appeared in the <em>original <code>nums</code> array</em>. This is essential for counting <em>subsequences</em> (which respect original relative order). If the problem allowed any combination, sorting <code>contributions_for_g[g]</code> by <code>num</code> before processing would be an alternative. The current approach is correct for subsequences.</li>
<li><strong>Precomputing <code>divs</code> optimization</strong>: The current divisor precomputation (<code>for i in range(1, M + 1): for j in range(i, M + 1, i): divs[j].append(i)</code>) is standard and efficient. No significant algorithmic improvement easily applicable here.</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Large <code>M</code></strong>: If <code>M</code> were significantly larger (e.g., <code>10^9</code>), the <code>divs</code> array and <code>F</code>, <code>count</code> arrays would consume too much memory and time. For such cases, a different approach (e.g., factorizing numbers on-the-fly, or using a segmented sieve for divisors) would be required. However, for typical competitive programming constraints (<code>M</code> up to <code>10^5</code> or <code>10^6</code>), the current approach is feasible.</li>
<li><strong>Python Overhead</strong>: Python's dynamic typing and object overhead can sometimes lead to slower execution compared to compiled languages like C++. For very tight time limits on large inputs, a direct C++ implementation of the same algorithm would likely perform faster. However, the chosen algorithms and data structures are efficient at an abstract level.</li>
<li><strong>Memory Management</strong>: The <code>divs</code> list of lists and <code>contributions_for_g</code> dictionary can consume substantial memory if <code>M</code> is large and numbers have many divisors. Python handles memory automatically, but this can become a bottleneck in extreme cases.</li>
</ul>


### Code:
```python
import collections
from typing import List

class Solution:
    def totalBeauty(self, nums: List[int]) -> int:
        MOD = 10**9 + 7
        
        if not nums:
            return 0
        
        M = max(nums)
        
        # --- Precompute Divisors ---
        divs = [[] for _ in range(M + 1)]
        for i in range(1, M + 1):
            for j in range(i, M + 1, i):
                divs[j].append(i)
        
        # Group contributions by g
        contributions_for_g = collections.defaultdict(list)
        for i, num in enumerate(nums):
            for g in divs[num]:
                contributions_for_g[g].append((num, i))
        
        # --- Calculate F[g] with Coordinate Compression ---
        F = [0] * (M + 1)
        
        for g in range(1, M + 1):
            if not contributions_for_g[g]:
                continue
            
            # Extract unique values for this g and compress coordinates
            values_for_g = [num for num, _ in contributions_for_g[g]]
            unique_vals = sorted(set(values_for_g))
            val_to_rank = {v: i + 1 for i, v in enumerate(unique_vals)}
            compressed_size = len(unique_vals)
            
            # Use regular array-based Fenwick Tree with compressed coordinates
            tree = [0] * (compressed_size + 1)
            
            def query(idx):
                s = 0
                while idx > 0:
                    s = (s + tree[idx]) % MOD
                    idx -= idx & (-idx)
                return s
            
            def update(idx, delta):
                while idx <= compressed_size:
                    tree[idx] = (tree[idx] + delta) % MOD
                    idx += idx & (-idx)
            
            for num, original_index in contributions_for_g[g]:
                rank = val_to_rank[num]
                
                # Count subsequences ending at values < num
                count_less = query(rank - 1) if rank > 1 else 0
                count_g = (1 + count_less) % MOD
                F[g] = (F[g] + count_g) % MOD
                
                # Update with current count
                update(rank, count_g)
        
        # --- Inclusion-Exclusion and Final Sum ---
        count = [0] * (M + 1)
        total_beauty = 0
        
        for g in range(M, 0, -1):
            if F[g] == 0:
                continue
            
            count[g] = F[g]
            
            # Subtract counts of multiples
            multiple = 2 * g
            while multiple <= M:
                count[g] = (count[g] - count[multiple] + MOD) % MOD
                multiple += g
            
            beauty = (g * count[g]) % MOD
            total_beauty = (total_beauty + beauty) % MOD
        
        return total_beauty
```
