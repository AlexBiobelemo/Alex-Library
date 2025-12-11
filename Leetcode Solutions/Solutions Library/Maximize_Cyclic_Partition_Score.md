## Maximize Cyclic Partition Score
**Language:** python
**Tags:** python,oop,dynamic programming,array,cyclic array

### Description:
<p>This code addresses a problem that asks to find the maximum possible score by partitioning a cyclic array <code>nums</code> into at most <code>k</code> disjoint subsegments. The score for each subsegment is defined as <code>max_value - min_value</code> within that subsegment. The overall goal is to maximize the sum of these scores across all chosen partitions.</p>
<h2>1. Overview &amp; Intent</h2>
<ul>
<li><strong>Problem Goal</strong>: Find the maximum total score obtainable by selecting at most <code>k</code> disjoint partitions from a given cyclic integer array <code>nums</code>.</li>
<li><strong>Partition Score</strong>: For each chosen partition <code>[start, end]</code>, its score is <code>max(nums[start...end]) - min(nums[start...end])</code>.</li>
<li><strong>Cyclic Array</strong>: The array <code>nums</code> is treated as cyclic, meaning the element after <code>nums[n-1]</code> is <code>nums[0]</code>.</li>
<li><strong>Approach</strong>: The solution employs a dynamic programming (DP) approach. To handle the cyclic nature efficiently, it tests a limited set of "candidate starting points" for linearizing the array. For each linearization, it runs a 1D DP to find the maximum score for <code>k</code> partitions.</li>
</ul>
<h2>2. How It Works</h2>
<p>The solution proceeds in three main steps:</p>
<ol>
<li><p><strong>Edge Case Handling</strong>:</p>
<ul>
<li>It first checks if all elements in <code>nums</code> are identical (i.e., <code>min_val == max_val</code>). If so, any partition will have a <code>max - min</code> score of <code>0</code>, so the total score is <code>0</code>. This is an early exit.</li>
</ul>
</li>
<li><p><strong>Identify Candidate Cut Points (Heuristic)</strong>:</p>
<ul>
<li>This is a crucial optimization for performance. Instead of trying all <code>N</code> possible starting points for the cyclic array (which would lead to <code>O(N^2 K)</code> complexity), it identifies a limited set of "candidate" starting points.</li>
<li>It finds the global minimum (<code>min_val</code>) and global maximum (<code>max_val</code>) of the entire array.</li>
<li>It then collects the indices of the first few (up to 3) and last few (up to 3) occurrences of both <code>min_val</code> and <code>max_val</code> in the array using the <code>get_indices</code> helper function. This results in a small, constant number of indices.</li>
<li>For each such index <code>idx</code>, it adds <code>idx</code> and <code>(idx + 1) % n</code> to <code>candidate_starts</code>. This means a partition might start <em>at</em> an extremum or <em>immediately after</em> an extremum.</li>
</ul>
</li>
<li><p><strong>Optimized Linear DP Solver</strong>:</p>
<ul>
<li>For each <code>start_idx</code> in <code>candidate_starts</code>:<ul>
<li><strong>Linearization</strong>: The cyclic array is "linearized" by creating <code>arr = nums[start_idx:] + nums[:start_idx]</code>. This treats <code>arr</code> as a linear array starting at <code>start_idx</code>.</li>
<li><strong>Dynamic Programming</strong>: A 1D DP is used with <code>k+1</code> states, representing the number of partitions <code>p</code>. There are four states for each <code>p</code>:<ul>
<li><code>curr_s0[p]</code>: Maximum score for <code>p</code> partitions, where the <code>p</code>-th partition is active but currently "empty" (neither min nor max picked yet for it).</li>
<li><code>curr_s1[p]</code>: Max score for <code>p</code> partitions, where the <code>p</code>-th partition is active and its maximum value (<code>x</code>) has been picked. It's waiting for its minimum.</li>
<li><code>curr_s2[p]</code>: Max score for <code>p</code> partitions, where the <code>p</code>-th partition is active and its minimum value (<code>x</code>) has been picked. It's waiting for its maximum.</li>
<li><code>curr_s3[p]</code>: Max score for <code>p</code> partitions, where the <code>p</code>-th partition is completed (both min and max have been picked), or it's an "empty" partition with 0 score (e.g., a single element partition <code>[x]</code> has <code>x-x=0</code>).</li>
</ul>
</li>
<li><strong>Iteration</strong>: The DP iterates through each element <code>x</code> of the linearized <code>arr</code>. For each <code>x</code>, it updates the DP states for partition counts <code>p</code> from <code>k</code> down to <code>1</code>.</li>
<li><strong>Transitions</strong>: Each state <code>curr_sX[p]</code> is updated by considering possibilities:<ul>
<li>Extend an existing active partition of the same type (<code>prev_sX[p]</code>).</li>
<li>Start a new partition (<code>p</code>-th) from a completed <code>p-1</code> partitions (<code>prev_s3[p-1]</code>).</li>
<li>Transition between <code>s0</code>, <code>s1</code>, <code>s2</code>, <code>s3</code> based on whether the current element <code>x</code> is picked as a max (<code>+x</code>) or min (<code>-x</code>) for the <em>current</em> active partition. <code>s3</code> aggregates completed partitions.</li>
</ul>
</li>
<li><strong>Score Update</strong>: After iterating through all elements of <code>arr</code>, <code>global_max_score</code> is updated with the maximum value found in <code>curr_s3[p]</code> for any <code>p</code> from <code>1</code> to <code>k</code>.</li>
</ul>
</li>
</ul>
</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Cyclic Array Handling</strong>: The problem is cyclic, but the DP is linear. The common technique of trying <code>N</code> rotations is reduced to trying a <em>constant</em> number of rotations (<code>C</code>) based on the heuristic of <code>candidate_starts</code>. This is a critical performance trade-off.</li>
<li><strong>Heuristic for <code>candidate_starts</code></strong>: This is the most opinionated design choice. It assumes that an optimal solution for the cyclic array will always involve global extrema (or points adjacent to them) in some way. This drastically reduces the outer loop complexity from <code>O(N)</code> to <code>O(1)</code> (where <code>C</code> is the constant number of candidates, e.g., &lt;= 12).</li>
<li><strong>DP State Design (4 states)</strong>: The four states <code>s0, s1, s2, s3</code> are well-suited for problems where a "pair" of elements (here, max and min) must be chosen to complete a segment.<ul>
<li><code>s0</code> acts as a starting point for a new partition.</li>
<li><code>s1</code> and <code>s2</code> track when one half of the <code>max - min</code> score is picked.</li>
<li><code>s3</code> represents a completed partition, accumulating the full <code>max - min</code> score.</li>
</ul>
</li>
<li><strong>1D DP Optimization</strong>: The DP uses <code>O(K)</code> space per rotation by iterating <code>p</code> backwards (<code>for p in range(limit, 0, -1)</code>). This ensures that <code>prev_sX[p]</code> refers to the state from the <em>previous element <code>i-1</code></em>, while <code>prev_p_minus_1_s3</code> refers to the state <code>s3[p-1]</code> from the <em>current element <code>i</code></em> (which was already updated for <code>p-1</code>).</li>
<li><strong><code>INF</code> Value</strong>: Using a sufficiently small negative number (<code>-10**18</code>) for <code>INF</code> prevents valid scores from being overwritten and ensures <code>max</code> operations work correctly.</li>
</ul>
<h2>4. Complexity</h2>
<p>Let <code>N</code> be the length of <code>nums</code> and <code>K</code> be the maximum number of partitions.</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><strong>Step 1 (Edge Case)</strong>: <code>O(N)</code> for <code>min()</code> and <code>max()</code>.</li>
<li><strong>Step 2 (Candidate Cut Points)</strong>: <code>get_indices</code> scans the array <code>O(N)</code> twice. The number of candidate start points <code>C</code> is a small constant (e.g., at most <code>2 * (3 + 3) = 12</code>).</li>
<li><strong>Step 3 (DP Solver)</strong>:<ul>
<li>Outer loop runs <code>C</code> times.</li>
<li><code>arr</code> linearization: <code>O(N)</code>.</li>
<li>Inner DP loops: <code>N</code> iterations (for <code>i</code>) * <code>K</code> iterations (for <code>p</code>) * <code>O(1)</code> (for state transitions). This is <code>O(N * K)</code>.</li>
<li>Total DP for one candidate start: <code>O(N + N * K) = O(N * K)</code>.</li>
</ul>
</li>
<li><strong>Overall Time Complexity</strong>: <code>O(N) + C * O(N * K) = O(C * N * K)</code>. Since <code>C</code> is a constant, this simplifies to <strong><code>O(N * K)</code></strong>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>nums</code>, <code>arr</code>: <code>O(N)</code> for storing the input and its linearized version.</li>
<li><code>min_indices</code>, <code>max_indices</code>, <code>candidate_starts</code>: <code>O(C)</code> which is <code>O(1)</code>.</li>
<li>DP arrays (<code>curr_s0</code> through <code>curr_s3</code>): <code>O(K)</code> each, leading to <code>O(K)</code> total for DP states.</li>
<li><strong>Overall Space Complexity</strong>: <strong><code>O(N + K)</code></strong>.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong>Homogeneous Array</strong>: <code>[5, 5, 5]</code>. Handled by the initial <code>min_val == max_val</code> check, returning <code>0</code>. Correct.</li>
<li><strong>Single Element Array</strong>: <code>[7]</code>. <code>n=1</code>. <code>min_val == max_val</code> check returns <code>0</code>. Score for <code>[7]</code> is <code>7-7=0</code>. Correct.</li>
<li><strong><code>k=0</code></strong>: The final loop <code>for p in range(1, k + 1)</code> will not execute, and <code>global_max_score</code> will remain <code>0</code>. Correct, as no partitions means no score.</li>
<li><strong>Negative Numbers</strong>: The DP logic (<code>+x</code>, <code>-x</code>) and <code>INF</code> value (<code>-10**18</code>) correctly handle negative numbers.</li>
<li><strong>The Heuristic's Correctness</strong>: The primary potential issue is the heuristic for <code>candidate_starts</code>. While it significantly speeds up the solution, it <strong>is not generally guaranteed to find the globally optimal solution</strong> for all possible inputs unless the problem statement implies a specific property (e.g., "an optimal partition always involves a global extremum or its neighbor"). If an optimal partition's <code>max_value</code> and <code>min_value</code> are local extrema that are <em>not</em> global extrema of the entire <code>nums</code> array, and this partition is not adjacent to a global extremum, the solution might miss it. This is a common performance vs. correctness trade-off in competitive programming; often such a heuristic passes tests due to test case constraints or specific problem properties not explicitly stated.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Remove Heuristic (for true optimality)</strong>: If guaranteed optimality is paramount and <code>N * K</code> allows for <code>N</code> rotations, remove the <code>candidate_starts</code> heuristic and simply iterate <code>start_idx</code> from <code>0</code> to <code>n-1</code>. This increases complexity to <code>O(N^2 K)</code> time.</li>
<li><strong>Refine <code>get_indices</code></strong>: The <code>get_indices</code> function could be simplified. Instead of scanning from both ends with <code>if i not in idxs</code>, you could find <em>all</em> indices, then take a <code>set</code> of <code>idxs[:limit] + idxs[-limit:]</code> (using an appropriate <code>limit</code>).</li>
<li><strong>Clarity of DP Transitions</strong>: The DP transitions, especially for <code>s3</code>, are quite dense. Adding more inline comments to explain the reasoning for each <code>if t &gt; vX: vX = t</code> would significantly improve readability. For example, explicitly noting when <code>s1</code> closes by picking <code>x</code> as min, or <code>s2</code> closes by picking <code>x</code> as max.</li>
<li><strong>Magic Number</strong>: The number <code>3</code> in <code>if count &gt;= 3: break</code> is a "magic number". It could be replaced with a named constant like <code>EXTREMUM_INDEX_LIMIT</code> for better understanding.</li>
<li><strong>Pre-allocate DP arrays once</strong>: The current code re-initializes <code>curr_sX</code> arrays with <code>[INF] * (k + 1)</code> for each <code>start_idx</code>. While <code>[:] =</code> is efficient, if <code>k</code> is very large, creating these lists repeatedly might be marginally slower than creating them once outside the loop and then efficiently resetting their values (e.g., <code>for i in range(k+1): curr_sX[i] = INF</code>). For <code>k</code> up to <code>N</code>, the current approach is fine.</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Performance</strong>: The <code>O(N * K)</code> complexity (due to the heuristic) is efficient enough for typical competitive programming constraints (e.g., <code>N=10^5, K=10^5</code> leads to <code>10^{10}</code> which is too high; <code>N=10^5, K=100</code> might be <code>10^7</code> operations, which is acceptable). Without the heuristic, <code>O(N^2 K)</code> would likely result in a Time Limit Exceeded (TLE) error for larger <code>N</code>.</li>
<li><strong>Memory</strong>: The <code>O(N + K)</code> space complexity is generally well within typical memory limits (e.g., <code>10^5</code> integers for <code>N</code> and <code>K</code> is a few megabytes).</li>
<li><strong>Integer Overflow</strong>: Python's arbitrary-precision integers inherently handle large numbers, so <code>global_max_score</code> and intermediate DP values will not overflow, even if <code>k * (max - min)</code> becomes very large (e.g., <code>10^5 * 2 * 10^9 = 2 * 10^14</code>).</li>
</ul>


### Code:
```python
import sys

class Solution:
    def maximumScore(self, nums: list[int], k: int) -> int:
        n = len(nums)
        
        # 1. Edge Case: Homogeneous array
        min_val = min(nums)
        max_val = max(nums)
        if min_val == max_val:
            return 0
            
        # 2. Identify Candidate Cut Points
        # We collect indices of Global Min and Global Max.
        # To avoid TLE on arrays like [1, 1, 1, ...], we only take the 
        # first few and last few occurrences of the extrema.
        
        def get_indices(val):
            # Find all indices of 'val', but limit count for safety
            idxs = []
            # Scan from start
            count = 0
            for i in range(n):
                if nums[i] == val:
                    idxs.append(i)
                    count += 1
                    if count >= 3: break # Heuristic limit
            
            # Scan from end (to catch the other side of the cycle)
            count = 0
            for i in range(n - 1, -1, -1):
                if nums[i] == val:
                    if i not in idxs: # Avoid duplicates
                        idxs.append(i)
                    count += 1
                    if count >= 3: break
            return idxs

        min_indices = get_indices(min_val)
        max_indices = get_indices(max_val)
        
        candidate_starts = set()
        
        # For every significant extremum, we test cutting:
        # 1. Exactly at the index (Extremum is start of partition)
        # 2. Immediately after the index (Extremum is end of partition)
        for idx in min_indices + max_indices:
            candidate_starts.add(idx)
            candidate_starts.add((idx + 1) % n)
            
        # 3. Optimized Linear DP Solver
        # DP States:
        # 0: Active partition, empty (waiting to pick first element)
        # 1: Active, Max picked (+val), need Min
        # 2: Active, Min picked (-val), need Max
        # 3: Active, Both picked (Partition valid, can extend or close)
        
        INF = -10**18
        
        # Pre-allocate arrays to minimize overhead
        curr_s0 = [INF] * (k + 1)
        curr_s1 = [INF] * (k + 1)
        curr_s2 = [INF] * (k + 1)
        curr_s3 = [INF] * (k + 1)
        
        global_max_score = 0

        for start_idx in candidate_starts:
            # Linearize the cyclic array
            arr = nums[start_idx:] + nums[:start_idx]
            
            # Reset DP state
            # We use slice assignment for speed
            curr_s0[:] = [INF] * (k + 1)
            curr_s1[:] = [INF] * (k + 1)
            curr_s2[:] = [INF] * (k + 1)
            curr_s3[:] = [INF] * (k + 1)
            
            # Base case: 0 partitions completed with score 0
            curr_s3[0] = 0
            
            for i, x in enumerate(arr):
                # We can have at most i+1 partitions at index i
                # We can also not exceed k partitions
                limit = i + 1
                if limit > k: limit = k
                
                # Iterate backwards to perform 1D DP update using values from previous step (i-1)
                for p in range(limit, 0, -1):
                    # Cache previous states to avoid array lookups
                    prev_s0 = curr_s0[p]
                    prev_s1 = curr_s1[p]
                    prev_s2 = curr_s2[p]
                    prev_s3 = curr_s3[p]
                    
                    # Previous state from partition p-1 (to start a new partition)
                    prev_p_minus_1_s3 = curr_s3[p-1]

                    # --- State Transitions ---
                    
                    # State 0: Start new partition p, picking nothing yet
                    # Transition: From prev_s0 (extend empty) OR from finished p-1 (start new)
                    v0 = prev_s0
                    if prev_p_minus_1_s3 > v0: v0 = prev_p_minus_1_s3
                    
                    # State 1: Pick Max (+x)
                    # Transitions: Extend s1, or pick x from s0, or start new p and pick x
                    v1 = prev_s1
                    if prev_s0 != INF:
                        t = prev_s0 + x
                        if t > v1: v1 = t
                    if prev_p_minus_1_s3 != INF:
                        t = prev_p_minus_1_s3 + x
                        if t > v1: v1 = t
                    
                    # State 2: Pick Min (-x)
                    # Transitions: Extend s2, or pick x from s0, or start new p and pick x
                    v2 = prev_s2
                    if prev_s0 != INF:
                        t = prev_s0 - x
                        if t > v2: v2 = t
                    if prev_p_minus_1_s3 != INF:
                        t = prev_p_minus_1_s3 - x
                        if t > v2: v2 = t
                    
                    # State 3: Done (Valid Partition)
                    # Transitions: Extend s3, or close s1 (pick min), or close s2 (pick max)
                    # or close s0 (single element max-min=0), or start new single element
                    v3 = prev_s3
                    if prev_s1 != INF:
                        t = prev_s1 - x
                        if t > v3: v3 = t
                    if prev_s2 != INF:
                        t = prev_s2 + x
                        if t > v3: v3 = t
                    if prev_s0 != INF and prev_s0 > v3: 
                        v3 = prev_s0 # Case: [x] -> max-min = 0
                    if prev_p_minus_1_s3 != INF and prev_p_minus_1_s3 > v3: 
                        v3 = prev_p_minus_1_s3 # Case: Start new [x]

                    # Update current DP arrays
                    curr_s0[p] = v0
                    curr_s1[p] = v1
                    curr_s2[p] = v2
                    curr_s3[p] = v3
            
            # Check max score for this rotation across all valid partition counts (1 to k)
            # Since "at most k" implies we can use fewer if optimal (though usually using k is best)
            for p in range(1, k + 1):
                if curr_s3[p] > global_max_score:
                    global_max_score = curr_s3[p]
                
        return global_max_score
```
