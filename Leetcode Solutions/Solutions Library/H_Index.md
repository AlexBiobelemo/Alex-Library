## H Index
**Language:** python
**Tags:** python,h-index,sorting,algorithm

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a solution to find the h-index of a researcher given a list of their paper citation counts. The h-index is defined as the maximum integer <code>h</code> such that the researcher has <code>h</code> papers that each have at least <code>h</code> citations.</p>
<p>The core intent is to efficiently determine this <code>h</code> value from a list of <code>citations</code>.</p>
<h3>2. How It Works</h3>
<p>The algorithm proceeds in two main steps:</p>
<ul>
<li><strong>Sort Citations (Descending):</strong> The <code>citations</code> list is first sorted in descending order (from most cited to least cited). This critical step ensures that as we iterate through the list, <code>citations[i]</code> represents the (i+1)-th most cited paper.</li>
<li><strong>Linear Scan for h-index:</strong> The code then iterates through the sorted <code>citations</code> list using an index <code>i</code> (starting from 0).<ul>
<li>In each iteration, it checks if the citation count of the current paper (<code>citations[i]</code>) is greater than or equal to <code>i + 1</code>. The value <code>i + 1</code> represents the number of papers considered so far (including the current one) and also a potential h-index value.</li>
<li>If <code>citations[i] &gt;= i + 1</code>, it means that we have found at least <code>i + 1</code> papers with <code>i + 1</code> or more citations. Since we are iterating from most cited papers and <code>i + 1</code> is increasing, <code>i + 1</code> is a valid candidate for <code>h</code>, and we update <code>h</code> to this value.</li>
<li>If <code>citations[i] &lt; i + 1</code>, it implies that the current paper (the <code>(i+1)</code>-th most cited) does not have <code>i + 1</code> citations. Because the list is sorted in descending order, any subsequent papers will have even fewer or equal citations. Therefore, no <code>h'</code> greater than or equal to <code>i + 1</code> can be achieved, and we can <code>break</code> the loop, as the current <code>h</code> is the maximum possible.</li>
</ul>
</li>
<li><strong>Return Result:</strong> The final value of <code>h</code> is returned.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sorting the Input:</strong> The most significant design decision is to sort the <code>citations</code> list in descending order.<ul>
<li><strong>Advantage:</strong> This transformation allows for a straightforward linear scan to find the h-index. By ensuring <code>citations[i]</code> is always greater than or equal to <code>citations[i+1]</code>, the condition <code>citations[i] &gt;= i + 1</code> can be used directly to check the h-index definition.</li>
<li><strong>Trade-off:</strong> Sorting introduces an <code>O(N log N)</code> time complexity, which often dominates the overall performance.</li>
</ul>
</li>
<li><strong>Iterative Check with Early Exit:</strong> The linear loop with the <code>break</code> condition leverages the sorted property. Once a paper fails the <code>citations[i] &gt;= i + 1</code> check, no larger <code>h</code> can be found, allowing for an early termination and avoiding unnecessary iterations.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong><ul>
<li><code>citations.sort(reverse=True)</code>: <code>O(N log N)</code>, where <code>N</code> is the number of citations. This is due to Python's Timsort algorithm.</li>
<li><code>for</code> loop: <code>O(N)</code> in the worst case (when <code>h</code> is large or equal to <code>N</code>).</li>
<li><strong>Overall:</strong> <code>O(N log N)</code> because sorting is the dominant factor.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong><ul>
<li><code>citations.sort()</code>: Python's <code>list.sort()</code> performs an in-place sort with <code>O(N)</code> auxiliary space in the worst case for Timsort (though often closer to <code>O(log N)</code> for well-ordered data).</li>
<li>Variables (<code>h</code>, <code>i</code>): <code>O(1)</code> auxiliary space.</li>
<li><strong>Overall:</strong> <code>O(N)</code> in the worst case, considering the sort. If only auxiliary space is considered for in-place sort, it could be <code>O(log N)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm handles several edge cases correctly:</p>
<ul>
<li><strong>Empty <code>citations</code> list (<code>[]</code>):</strong><ul>
<li><code>len(citations)</code> is 0.</li>
<li>The <code>for</code> loop <code>range(0)</code> is skipped.</li>
<li><code>h</code> remains 0.</li>
<li><strong>Correct:</strong> An empty publication record has an h-index of 0.</li>
</ul>
</li>
<li><strong>All zero citations (<code>[0, 0, 0]</code>):</strong><ul>
<li>Sorted: <code>[0, 0, 0]</code></li>
<li><code>i = 0</code>: <code>citations[0]</code> (0) is not <code>&gt;= 0 + 1</code> (1). The loop breaks.</li>
<li><code>h</code> remains 0.</li>
<li><strong>Correct:</strong> No paper has 1 or more citations, so h-index is 0.</li>
</ul>
</li>
<li><strong>All high, equal citations (<code>[10, 10, 10]</code>):</strong><ul>
<li>Sorted: <code>[10, 10, 10]</code></li>
<li><code>i = 0</code>: <code>10 &gt;= 1</code> -&gt; <code>h = 1</code>.</li>
<li><code>i = 1</code>: <code>10 &gt;= 2</code> -&gt; <code>h = 2</code>.</li>
<li><code>i = 2</code>: <code>10 &gt;= 3</code> -&gt; <code>h = 3</code>.</li>
<li>Loop finishes. Returns 3.</li>
<li><strong>Correct:</strong> 3 papers have at least 3 citations.</li>
</ul>
</li>
<li><strong>Single citation (<code>[5]</code>):</strong><ul>
<li>Sorted: <code>[5]</code></li>
<li><code>i = 0</code>: <code>5 &gt;= 1</code> -&gt; <code>h = 1</code>.</li>
<li>Loop finishes. Returns 1.</li>
<li><strong>Correct:</strong> 1 paper has at least 1 citation.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<p>While the current solution is straightforward and generally efficient enough for typical constraints, there's a more optimal approach:</p>
<ul>
<li><strong>Counting Sort / Bucketing (Optimal Time Complexity):</strong><ul>
<li><strong>Idea:</strong> Instead of sorting the actual citation values, we can count the frequency of each citation value. Since the h-index cannot exceed the total number of papers (<code>N</code>), we only need to count up to <code>N</code> citations.</li>
<li><strong>Steps:</strong><ol>
<li>Create an array <code>counts</code> of size <code>N + 1</code>, initialized to zeros.</li>
<li>Iterate through the <code>citations</code> list: for each <code>c</code> in <code>citations</code>, increment <code>counts[min(c, N)]</code>. This ensures we only track counts up to <code>N</code>, as any paper with more than <code>N</code> citations effectively counts as having <code>N</code> citations for the purpose of the h-index.</li>
<li>Initialize <code>num_papers = 0</code>.</li>
<li>Iterate <code>i</code> from <code>N</code> down to 0:<ul>
<li>Add <code>counts[i]</code> to <code>num_papers</code>. <code>num_papers</code> now represents the total number of papers that have <em>at least</em> <code>i</code> citations.</li>
<li>If <code>num_papers &gt;= i</code>, then <code>i</code> is the maximum possible h-index. Return <code>i</code>.</li>
</ul>
</li>
<li>If the loop finishes (e.g., for an empty list), return 0.</li>
</ol>
</li>
<li><strong>Complexity:</strong><ul>
<li><strong>Time:</strong> <code>O(N)</code> for counting, <code>O(N)</code> for iterating buckets. Total <code>O(N)</code>. This is a significant improvement over <code>O(N log N)</code> for large <code>N</code>.</li>
<li><strong>Space:</strong> <code>O(N)</code> for the <code>counts</code> array.</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> For large input lists, the <code>O(N log N)</code> sorting step will dominate the execution time. The alternative counting sort approach offers an <code>O(N)</code> solution, which would be preferable for performance-critical applications or very large datasets.</li>
<li><strong>Security:</strong> This algorithm has no direct security implications as it operates purely on numerical data without external inputs, network calls, or sensitive data handling.</li>
</ul>


### Code:
```python
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        citations.sort(reverse=True) # Sort citations in descending order

        h = 0
        for i in range(len(citations)):
            if citations[i] >= i + 1:
                h = i + 1
            else:
                break
        return h
```
