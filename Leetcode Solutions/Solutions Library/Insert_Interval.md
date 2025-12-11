## Insert Interval
**Language:** python
**Tags:** python,intervals,merge intervals,list manipulation

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python method, <code>insert</code>, is designed to insert a <code>newInterval</code> into a list of <code>intervals</code>. The input <code>intervals</code> list is assumed to be sorted by start times and contains non-overlapping intervals. The goal is to produce a new list of intervals that remains sorted and non-overlapping, incorporating the <code>newInterval</code> by merging it with any existing intervals it overlaps with.</p>
<p><strong>Example:</strong></p>
<ul>
<li><code>intervals = [[1,3],[6,9]]</code>, <code>newInterval = [2,5]</code></li>
<li>Result: <code>[[1,5],[6,9]]</code> (where <code>[2,5]</code> merged with <code>[1,3]</code> to form <code>[1,5]</code>)</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm processes the <code>intervals</code> list in a single pass, conceptually divided into four main phases:</p>
<ol>
<li><strong>Add Non-Overlapping Preceding Intervals:</strong> It iterates through <code>intervals</code> and appends all intervals that completely end <em>before</em> <code>newInterval</code> starts to the <code>result</code> list. These intervals cannot possibly overlap with <code>newInterval</code> or any subsequent merged interval.</li>
<li><strong>Merge Overlapping Intervals:</strong> It then enters a phase where it checks for overlaps. As long as the current <code>intervals[i]</code> overlaps with <code>newInterval</code> (i.e., <code>intervals[i][0] &lt;= newInterval[1]</code>), it merges <code>intervals[i]</code> into <code>newInterval</code>. The merge operation updates <code>newInterval</code>'s start to the minimum of the two interval starts and its end to the maximum of the two interval ends, effectively expanding <code>newInterval</code> to cover the merged range. This process continues, consuming all overlapping intervals into a single, consolidated <code>newInterval</code>.</li>
<li><strong>Append Merged Interval:</strong> After the merge phase, the now potentially expanded <code>newInterval</code> (which represents the union of the original <code>newInterval</code> and all intervals it overlapped with) is appended to the <code>result</code> list.</li>
<li><strong>Add Non-Overlapping Succeeding Intervals:</strong> Finally, it appends any remaining intervals from the original <code>intervals</code> list to <code>result</code>. These intervals are guaranteed to start <em>after</em> the consolidated <code>newInterval</code> ends and thus do not overlap.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Linear Scan:</strong> The core design is a single linear scan (conceptual three-pass, actual one pass with an index) through the input <code>intervals</code>. This is efficient given the sorted input.</li>
<li><strong>In-Place <code>newInterval</code> Modification:</strong> The <code>newInterval</code> variable itself is used to accumulate the merged range during the second phase. This avoids creating many temporary interval objects during the merge process.</li>
<li><strong>Leveraging Sorted Input:</strong> The correctness and efficiency of this approach heavily rely on the input <code>intervals</code> being sorted by their start times and non-overlapping. This allows for simple comparisons (<code>intervals[i][1] &lt; newInterval[0]</code> and <code>intervals[i][0] &lt;= newInterval[1]</code>) to determine relative positions and overlaps without needing complex lookaheads or backtracking.</li>
<li><strong>Result List Construction:</strong> A new <code>result</code> list is built to store the output. This is a common and safe approach, avoiding complex in-place modifications of the original list, which can be tricky with insertions and deletions.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Where N is the number of intervals in the input <code>intervals</code> list.</li>
<li>Each interval in the <code>intervals</code> list is visited at most once by the index <code>i</code>. Operations inside the loops (append, <code>min</code>, <code>max</code>) are all O(1).</li>
<li>Therefore, the overall time complexity is linear with respect to the number of input intervals.</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li>A new list <code>result</code> is created to store the output. In the worst case (no overlaps), this list will contain N (original intervals) + 1 (new interval) elements.</li>
<li><code>newInterval</code> itself consumes constant extra space.</li>
<li>Thus, the space complexity is proportional to the number of intervals in the output list.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly due to its robust phase-based approach:</p>
<ul>
<li><strong>Empty <code>intervals</code> list:</strong><ul>
<li>Both initial <code>while</code> loops (phases 1 and 2) will be skipped as <code>n</code> is 0.</li>
<li><code>newInterval</code> is appended to <code>result</code>.</li>
<li>The final <code>while</code> loop (phase 4) is skipped.</li>
<li>Result: <code>[newInterval]</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong><code>newInterval</code> inserted at the beginning (no overlap):</strong><ul>
<li>Phase 1 is skipped.</li>
<li><code>newInterval</code> is appended.</li>
<li>Remaining intervals are appended.</li>
<li>Result: <code>[newInterval, original_intervals...]</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong><code>newInterval</code> inserted at the end (no overlap):</strong><ul>
<li>Phase 1 appends all original intervals.</li>
<li>Phase 2 is skipped.</li>
<li><code>newInterval</code> is appended.</li>
<li>Phase 4 is skipped.</li>
<li>Result: <code>[original_intervals..., newInterval]</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong><code>newInterval</code> completely consumed by an existing interval:</strong><ul>
<li>Example: <code>intervals = [[1,10]]</code>, <code>newInterval = [3,5]</code></li>
<li>Phase 1 skipped.</li>
<li>Phase 2 merges <code>[1,10]</code> with <code>[3,5]</code> to make <code>newInterval</code> become <code>[1,10]</code>.</li>
<li>Phase 3 appends <code>[1,10]</code>.</li>
<li>Result: <code>[[1,10]]</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong><code>newInterval</code> consumes multiple existing intervals:</strong><ul>
<li>Example: <code>intervals = [[1,2],[3,4],[5,6]]</code>, <code>newInterval = [1,6]</code></li>
<li>Phase 1 skipped.</li>
<li>Phase 2 merges all <code>[1,2]</code>, <code>[3,4]</code>, <code>[5,6]</code> into <code>newInterval</code>, which becomes <code>[1,6]</code>.</li>
<li>Phase 3 appends <code>[1,6]</code>.</li>
<li>Result: <code>[[1,6]]</code>. <strong>Correct.</strong></li>
</ul>
</li>
<li><strong>No overlap at all (insertion in the middle):</strong><ul>
<li>Example: <code>intervals = [[1,2],[6,8]]</code>, <code>newInterval = [3,5]</code></li>
<li>Phase 1 appends <code>[1,2]</code>.</li>
<li>Phase 2 is skipped as <code>intervals[1]</code> <code>[6,8]</code> does not overlap with <code>newInterval</code> <code>[3,5]</code>.</li>
<li>Phase 3 appends <code>[3,5]</code>.</li>
<li>Phase 4 appends <code>[6,8]</code>.</li>
<li>Result: <code>[[1,2],[3,5],[6,8]]</code>. <strong>Correct.</strong></li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already very readable due to clear variable names and excellent comments delineating the phases. No significant improvements needed here.</li>
<li><strong>Alternative Algorithms:</strong><ul>
<li><strong>Binary Search for Insertion Point:</strong> For very large <code>N</code>, one could use binary search (e.g., <code>bisect_left</code>) to find the potential starting index <code>i</code> for phase 1 or 2. However, since the merge phase still requires a linear scan from that point, and then appending remaining elements, the overall time complexity would remain O(N). While it might slightly reduce constant factors if <code>newInterval</code> is at the very beginning or end and doesn't overlap much, for general cases, the current linear scan is simpler and equally efficient asymptotically.</li>
<li><strong>More Functional Style:</strong> While possible, expressing the stateful merging logic (where <code>newInterval</code> is continuously updated) might become less clear or less performant with purely functional constructs like <code>map</code>/<code>filter</code>/<code>reduce</code> without careful design. The current iterative approach is often preferred for such problems in Python.</li>
</ul>
</li>
<li><strong>Minor Pythonic Touch:</strong><pre><code class="language-python"># Phase 1, 2, 4 can be slightly condensed into single loops if desired,
# but the current explicit phase separation is very clear.
# For example, phase 4:
# result.extend(intervals[i:]) # More concise
</code></pre>
However, using a <code>while</code> loop with an index is perfectly idiomatic and often more explicit in controlling iteration.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The solution is optimally performant for the given constraints (sorted input, non-overlapping intervals). Any solution requiring the construction of a new merged list will fundamentally need to iterate through most, if not all, elements, leading to O(N) time and O(N) space. This solution achieves those optimal bounds.</li>
<li><strong>Security:</strong> There are no apparent security vulnerabilities. The code operates purely on numerical interval data, performs standard list manipulations, and does not interact with external systems, files, or user input in a way that could introduce security risks (e.g., injection, resource exhaustion beyond expected use). The input consists of lists of integers, which are processed safely.</li>
</ul>


### Code:
```python
class Solution(object):
    def insert(self, intervals, newInterval):
        """
        :type intervals: List[List[int]]
        :type newInterval: List[int]
        :rtype: List[List[int]]
        """
        result = []
        i = 0
        n = len(intervals)

        # 1. Add all intervals that come before newInterval and don't overlap
        # An interval `intervals[i]` comes before `newInterval` if its end is less than `newInterval`'s start.
        while i < n and intervals[i][1] < newInterval[0]:
            result.append(intervals[i])
            i += 1

        # 2. Merge newInterval with all overlapping intervals
        # This loop continues as long as there is an overlap.
        # An overlap exists if the current interval's start is less than or equal to newInterval's end.
        # (Since intervals are sorted, if intervals[i][0] > newInterval[1], then no further intervals will overlap either).
        while i < n and intervals[i][0] <= newInterval[1]:
            newInterval[0] = min(newInterval[0], intervals[i][0])
            newInterval[1] = max(newInterval[1], intervals[i][1])
            i += 1

        # 3. Add the merged newInterval to the result
        result.append(newInterval)

        # 4. Add all remaining intervals (which come after the merged newInterval)
        while i < n:
            result.append(intervals[i])
            i += 1

        return result
```
