## Detect Pattern of Length M Repeated K or More TImes
**Language:** python
**Tags:** array,pattern matching,brute force,sublist

### Description:
<p>The provided Python code defines a function <code>containsPattern</code> that checks for a specific pattern repetition within an array.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The function <code>containsPattern(arr, m, k)</code> aims to determine if there exists a pattern of a specified <code>length</code> (<code>m</code>) within the input array <code>arr</code> that repeats <code>k</code> or more times <strong>consecutively</strong>.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm proceeds as follows:</p>
<ul>
<li><strong>Initial Check</strong>: It first verifies if the array <code>arr</code> is long enough to even contain <code>k</code> repetitions of a pattern of length <code>m</code> (i.e., <code>n &lt; m * k</code>). If not, it immediately returns <code>False</code>.</li>
<li><strong>Iterate Potential Starts</strong>: It then iterates through all possible starting positions (<code>i</code>) for the <em>first</em> occurrence of a pattern of length <code>m</code>. The loop range <code>range(n - m * k + 1)</code> ensures that there's always enough space in the array for <code>k</code> consecutive repetitions if the pattern starts at <code>i</code>.</li>
<li><strong>Define Candidate Pattern</strong>: For each starting position <code>i</code>, it extracts <code>arr[i : i + m]</code> to define the <code>pattern</code> it will try to match.</li>
<li><strong>Check for Consecutive Repetitions</strong>:<ul>
<li>It initializes a <code>repetitions</code> counter to <code>1</code> (for the <code>pattern</code> itself).</li>
<li>It then enters an inner <code>while</code> loop, starting its check from the index <code>i + m</code> (the start of the block immediately following the <code>pattern</code>).</li>
<li>In each iteration of the inner loop, it extracts the <code>next_block</code> of length <code>m</code> from the current checking position.</li>
<li><strong>Match</strong>: If <code>next_block</code> is identical to <code>pattern</code>, it increments <code>repetitions</code>. If <code>repetitions</code> reaches <code>k</code> or more, it means the condition is met, and the function returns <code>True</code>. The <code>current_check_start</code> is advanced by <code>m</code> to look for the next block.</li>
<li><strong>No Match</strong>: If <code>next_block</code> does not match <code>pattern</code>, the consecutive repetition is broken. The inner loop breaks, and the outer loop moves to the next possible starting position <code>i</code>.</li>
</ul>
</li>
<li><strong>No Pattern Found</strong>: If the outer loop completes without finding any pattern that repeats <code>k</code> or more times consecutively, the function returns <code>False</code>.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Fixed-Size Sliding Window</strong>: The core strategy involves fixing a candidate <code>pattern</code> of length <code>m</code> and then using a sliding window of the same size to check for consecutive matches immediately after it.</li>
<li><strong>Early Exit</strong>: The function uses <code>return True</code> as soon as <code>k</code> consecutive repetitions are found, which is an efficient optimization.</li>
<li><strong>Python List Slicing</strong>: Python's convenient list slicing (<code>arr[start:end]</code>) is used to extract both the <code>pattern</code> and subsequent <code>next_block</code>s. This creates new list objects for each slice.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: <code>O(N^2)</code></strong><ul>
<li>The outer loop runs approximately <code>N - m*k</code> times, which is <code>O(N)</code> in the worst case.</li>
<li>Inside the outer loop, <code>pattern</code> extraction takes <code>O(m)</code> time due to list slicing.</li>
<li>The inner <code>while</code> loop can iterate up to <code>N/m</code> times in the worst case (if all subsequent blocks match or almost match).</li>
<li>Within each inner <code>while</code> loop iteration, <code>next_block</code> extraction takes <code>O(m)</code> time, and comparing <code>next_block</code> with <code>pattern</code> also takes <code>O(m)</code> time (as it compares elements one by one).</li>
<li>Combining these, the worst-case time complexity is approximately <code>O(N * (m + (N/m) * m)) = O(N * (m + N)) = O(N^2)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: <code>O(m)</code></strong><ul>
<li>The space used is primarily for storing the <code>pattern</code> and <code>next_block</code> slices, each of which consumes <code>O(m)</code> space. Since these are temporary variables and only one of each is active at a time, the auxiliary space complexity is <code>O(m)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Array too short (<code>n &lt; m*k</code>)</strong>: Correctly handled by the initial check, returning <code>False</code>.</li>
<li><strong><code>m=1</code></strong>: The logic correctly handles patterns of length 1 (single elements). The comparisons become <code>O(1)</code>.</li>
<li><strong><code>k=1</code></strong>: The problem definition usually implies "at least <code>k</code> repetitions". If <code>k=1</code> means "contains any pattern of length <code>m</code>", the code correctly identifies this. If <code>n &gt;= m*k</code>, the <code>repetitions</code> count starts at 1, and <code>1 &gt;= k</code> (1 &gt;= 1) is true, leading to an immediate <code>True</code> return after the pattern is defined.</li>
<li><strong>No matching pattern</strong>: If no pattern ever repeats <code>k</code> times consecutively, the loops will complete, and the function correctly returns <code>False</code>.</li>
<li><strong>Exactly <code>k</code> repetitions</strong>: The <code>if repetitions &gt;= k</code> condition correctly catches scenarios where exactly <code>k</code> repetitions are found.</li>
<li><strong>Invalid <code>m</code> or <code>k</code> (e.g., <code>m=0</code>, <code>k=0</code>)</strong>: The code assumes <code>m &gt;= 1</code> and <code>k &gt;= 1</code>, which are typical constraints for such problems. If <code>m=0</code>, slicing <code>arr[i:i+m]</code> would create an empty list, and subsequent logic would break or lead to an infinite loop.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Optimize Slicing Overhead</strong>:<ul>
<li><strong>Direct Comparison</strong>: Instead of creating new list objects with slicing for <code>pattern</code> and <code>next_block</code> (which involves memory allocation and copying), consider comparing the subarrays directly using a helper function or an inner <code>for</code> loop that compares elements at specific indices. This would improve the constant factor of performance and reduce space complexity to <code>O(1)</code>.<pre><code class="language-python"># Example for direct comparison
# def compare_subarrays(arr, start1, start2, length):
#     for x in range(length):
#         if arr[start1 + x] != arr[start2 + x]:
#             return False
#     return True
# ... then use: if compare_subarrays(arr, i, current_check_start, m):
</code></pre>
</li>
</ul>
</li>
<li><strong>Rabin-Karp Algorithm for Large <code>m</code></strong>: For very large pattern lengths (<code>m</code>), string searching algorithms like Rabin-Karp (using rolling hashes) could compare blocks in <code>O(1)</code> average time (amortized), reducing the overall time complexity from <code>O(N^2)</code> to <code>O(N * (N/m))</code> or <code>O(N)</code> on average. However, this adds complexity with hash collision handling.</li>
<li><strong>KMP/Z-Algorithm (More Complex Adaptation)</strong>: While primarily for finding occurrences of a <em>known</em> pattern, these algorithms could potentially be adapted to find repeating patterns more efficiently, but it would be a more involved change for this specific problem structure.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance for Large Inputs</strong>: The <code>O(N^2)</code> time complexity, particularly with the overhead of Python list slicing and object creation, means this solution might be too slow for very large input arrays (e.g., <code>N &gt; 5000</code>). For competitive programming or performance-critical applications with large <code>N</code>, the direct comparison optimization mentioned above would be highly recommended.</li>
<li><strong>Memory Usage</strong>: While <code>O(m)</code> space is generally efficient, if <code>m</code> itself is extremely large (e.g., <code>N/2</code>), then <code>O(N)</code> space is used, which might be a concern for memory-constrained environments, especially with repeated allocations for slices. The direct comparison approach would address this by reducing space to <code>O(1)</code>.</li>
</ul>


### Code:
```python
class Solution(object):
    def containsPattern(self, arr, m, k):
        """
        :type arr: List[int]
        :type m: int
        :type k: int
        :rtype: bool
        """
        n = len(arr)

        # The total length required for k repetitions of a pattern of length m is m * k.
        # If the array is shorter than this, no such pattern can exist.
        if n < m * k:
            return False

        # Iterate through all possible starting positions for the first pattern block.
        # The last possible starting position 'i' for the first block is such that
        # the k-th block (which ends at i + k*m) still fits within the array.
        # So, i + k*m <= n, which means i <= n - k*m.
        # The range for 'i' should be from 0 up to n - k*m (inclusive).
        for i in range(n - m * k + 1):
            # Define the candidate pattern, which is the subarray of length 'm' starting at 'i'.
            pattern = arr[i : i + m]

            # Initialize the count of consecutive repetitions for this pattern.
            # The first occurrence is already counted.
            repetitions = 1
            
            # Start checking from the block immediately following the first one.
            # This block starts at index i + m.
            current_check_start = i + m
            
            # Continue checking as long as there's enough space in the array
            # for another block of length 'm'.
            while current_check_start + m <= n:
                # Get the next block to compare.
                next_block = arr[current_check_start : current_check_start + m]
                
                # If the next block matches the defined pattern:
                if next_block == pattern:
                    repetitions += 1
                    # If we have found 'k' or more repetitions, we have succeeded.
                    if repetitions >= k:
                        return True
                    # Move to check the block after the current one.
                    current_check_start += m
                else:
                    # If the next block does not match the pattern,
                    # this specific pattern starting at 'i' does not repeat 'k' times.
                    # Break from the inner loop and try the next starting position 'i'.
                    break
        
        # If the outer loop completes without finding any pattern that repeats 'k' or more times,
        # then no such pattern exists.
        return False
```
