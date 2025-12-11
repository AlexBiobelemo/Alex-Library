## Booking Concert Tickets in Groups
**Language:** python
**Tags:** segment tree,range query,seat allocation,data structures

### Description:
<p>This code implements a seat booking system for an arena or theater using segment trees to efficiently manage seat reservations. It handles two primary booking types: <code>gather</code> (booking <code>k</code> seats consecutively in a single row) and <code>scatter</code> (booking <code>k</code> seats by distributing them across multiple rows).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>BookMyShow</code> class simulates a booking system for an arena with <code>n</code> rows and <code>m</code> seats per row.
Its main goal is to efficiently handle two types of booking requests:</p>
<ul>
<li><strong><code>gather(k, maxRow)</code></strong>: Find the first available row (from row 0 up to <code>maxRow</code>) that has <code>k</code> <em>consecutive</em> available seats, book them, and return their row and starting seat number. If no such row exists, return an empty list.</li>
<li><strong><code>scatter(k, maxRow)</code></strong>: Book a total of <code>k</code> seats by distributing them among rows from 0 up to <code>maxRow</code>. Seats are filled greedily, taking as many as possible from lower-indexed rows first. Return <code>True</code> if <code>k</code> seats could be booked, <code>False</code> otherwise.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to use segment trees for fast range queries and updates on available seats.</p>
<ul>
<li><p><strong>Initialization (<code>__init__</code>)</strong>:</p>
<ul>
<li><code>self.n</code>: Number of rows.</li>
<li><code>self.m</code>: Seats per row.</li>
<li><code>self.seats</code>: A simple array <code>[0, 0, ..., 0]</code> of size <code>n</code> to store the <em>number of seats already occupied</em> in each row. This implicitly means <code>m - self.seats[row]</code> is the number of <em>available</em> seats.</li>
<li><strong><code>self.max_tree</code></strong>: A segment tree built to store the <em>maximum number of available seats</em> in any row within a given range. This is crucial for <code>gather</code>.</li>
<li><strong><code>self.sum_tree</code></strong>: A segment tree built to store the <em>total number of available seats</em> in a given range. This is crucial for <code>scatter</code>.</li>
<li>Both segment trees are initialized such that each leaf node (representing a row) has <code>m</code> available seats.</li>
</ul>
</li>
<li><p><strong>Segment Tree Operations (<code>build_max</code>, <code>build_sum</code>, <code>query_max</code>, <code>query_sum</code>, <code>update</code>)</strong>:</p>
<ul>
<li>These are standard segment tree implementations.</li>
<li><code>build_max</code>/<code>build_sum</code>: Recursively construct the trees from the leaf nodes upwards, storing max/sum of children.</li>
<li><code>query_max</code>/<code>query_sum</code>: Recursively traverse the tree to find the max/sum in a specified range <code>[l, r]</code>.</li>
<li><code>update</code>: Updates the value at a specific <code>idx</code> (row) in both segment trees and propagates the changes up to the root.</li>
</ul>
</li>
<li><p><strong><code>find_first_fit(node, start, end, l, r, k)</code></strong>:</p>
<ul>
<li>This is a specialized segment tree query. It traverses the <code>max_tree</code> to find the <em>first</em> row (lowest index) within the range <code>[l, r]</code> that has at least <code>k</code> available seats.</li>
<li>It prioritizes the left child, only going right if the left subtree doesn't contain a suitable row.</li>
<li>Base cases: If the current range is outside <code>[l, r]</code> or <code>max_tree[node] &lt; k</code>, it means no fit in this segment, returns <code>-1</code>. If it's a leaf node, it returns the row index.</li>
</ul>
</li>
<li><p><strong><code>gather(k, maxRow)</code></strong>:</p>
<ol>
<li>Clamps <code>maxRow</code> to <code>n-1</code> if out of bounds.</li>
<li>Calls <code>find_first_fit</code> on <code>self.max_tree</code> to find the lowest-indexed row (from 0 to <code>maxRow</code>) that can accommodate <code>k</code> seats.</li>
<li>If no such row is found, returns <code>[]</code>.</li>
<li>Otherwise, it determines the starting seat index (<code>self.seats[row]</code>), updates <code>self.seats[row]</code> by adding <code>k</code>, and then calculates the <code>available</code> seats remaining in that row.</li>
<li>Finally, it calls <code>update</code> to reflect the new <code>available</code> count in both segment trees for that specific <code>row</code>.</li>
<li>Returns <code>[row, seat]</code>.</li>
</ol>
</li>
<li><p><strong><code>scatter(k, maxRow)</code></strong>:</p>
<ol>
<li>Clamps <code>maxRow</code> to <code>n-1</code> if out of bounds.</li>
<li>Uses <code>query_sum</code> on <code>self.sum_tree</code> to check if the <em>total</em> available seats in rows <code>0</code> to <code>maxRow</code> are at least <code>k</code>. If not, returns <code>False</code>.</li>
<li>Iterates through rows from <code>0</code> to <code>maxRow</code>:<ul>
<li>If <code>k</code> becomes 0, breaks the loop (all seats booked).</li>
<li>Calculates <code>available</code> seats in the current row.</li>
<li>Determines <code>take</code>, the number of seats to book in this row (<code>min(available, k)</code>).</li>
<li>Updates <code>self.seats[row]</code> and decrements <code>k</code>.</li>
<li>Calculates <code>remaining</code> seats in the row.</li>
<li>Calls <code>update</code> to reflect <code>remaining</code> in both segment trees for the current <code>row</code>.</li>
</ul>
</li>
<li>Returns <code>True</code>.</li>
</ol>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Two Segment Trees (<code>max_tree</code> and <code>sum_tree</code>)</strong>:<ul>
<li><strong><code>max_tree</code></strong>: Essential for <code>gather</code>. The <code>find_first_fit</code> operation directly leverages the maximum available seats in a range to quickly prune search branches, allowing it to find a suitable row in logarithmic time. Without it, <code>gather</code> would likely require linear scan in the worst case.</li>
<li><strong><code>sum_tree</code></strong>: Essential for <code>scatter</code>'s initial check. It allows quickly determining if <code>k</code> total seats are available within the specified <code>maxRow</code> range without iterating through all rows, ensuring <code>scatter</code> can fail early.</li>
</ul>
</li>
<li><strong>Separate <code>self.seats</code> Array</strong>: While the segment trees store <em>available</em> seats, <code>self.seats</code> explicitly tracks <em>occupied</em> seats. This is important because <code>gather</code> needs to return the <em>starting seat index</em> which is <code>self.seats[row]</code> <em>before</em> the new booking. It also makes calculating <code>available</code> and <code>remaining</code> straightforward (<code>m - self.seats[row]</code>).</li>
<li><strong><code>find_first_fit</code> Algorithm</strong>: The recursive logic that tries the left child first is critical. This ensures that when multiple rows meet the <code>k</code> seat requirement, the one with the lowest index is always chosen, fulfilling the "first available row" requirement for <code>gather</code>.</li>
<li><strong>Greedy <code>scatter</code></strong>: The <code>scatter</code> method's loop iterating from <code>row 0</code> to <code>maxRow</code> and filling each row as much as possible is a greedy approach. It satisfies the requirement of distributing seats among the earliest possible rows.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the number of rows (<code>self.n</code>).</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><code>__init__(n, m)</code>: <code>O(N)</code> for initializing <code>self.seats</code> and <code>O(N)</code> for building each of the two segment trees. Total: <code>O(N)</code>.</li>
<li><code>build_max</code>/<code>build_sum</code>: <code>O(N)</code> each.</li>
<li><code>query_max</code>/<code>query_sum</code>: <code>O(log N)</code> for a range query.</li>
<li><code>find_first_fit</code>: <code>O(log N)</code> because it's a specialized segment tree traversal that prunes half the search space at each step.</li>
<li><code>update</code>: <code>O(log N)</code> for updating a single element and propagating changes.</li>
<li><code>gather(k, maxRow)</code>: Dominated by <code>find_first_fit</code> and <code>update</code>. Total: <code>O(log N)</code>.</li>
<li><code>scatter(k, maxRow)</code>:<ul>
<li>Initial <code>query_sum</code>: <code>O(log N)</code>.</li>
<li>The loop iterates at most <code>maxRow + 1</code> times (which can be <code>N</code> in the worst case). Inside the loop, an <code>update</code> operation is performed.</li>
<li>Worst case: <code>O(log N + N * log N) = O(N log N)</code>. This is the primary bottleneck.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>self.seats</code>: <code>O(N)</code> for storing seat occupancy for each row.</li>
<li><code>self.max_tree</code>, <code>self.sum_tree</code>: Each segment tree requires <code>O(4N)</code> space (typically <code>4*N</code> for array-based implementation).</li>
<li>Total: <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>k</code> seats requested</strong>:<ul>
<li><strong><code>k = 0</code></strong>: <code>gather</code> will find the first row and return <code>[row, seats[row]]</code> without changing anything (as <code>seats[row] += 0</code>). <code>scatter</code> will return <code>True</code> immediately as <code>k</code> is 0. Both are logically correct.</li>
<li><strong><code>k &gt; m</code> for <code>gather</code></strong>: <code>find_first_fit</code> will correctly return <code>-1</code> because no row can hold <code>k</code> seats (as the maximum available in any node will be at most <code>m</code>).</li>
<li><strong><code>k</code> larger than total available seats for <code>scatter</code></strong>: The initial <code>query_sum</code> will correctly detect this and <code>scatter</code> will return <code>False</code>.</li>
</ul>
</li>
<li><strong><code>maxRow</code> parameter</strong>:<ul>
<li><strong><code>maxRow</code> out of bounds (e.g., <code>n</code> or greater)</strong>: The code correctly clamps <code>maxRow</code> to <code>self.n - 1</code>, ensuring queries stay within valid row indices.</li>
<li><strong><code>maxRow = 0</code></strong>: Queries and iterations correctly restrict to only the first row (<code>row 0</code>).</li>
</ul>
</li>
<li><strong>Arena entirely empty/full</strong>:<ul>
<li><strong>Empty</strong>: Segment trees will reflect all <code>m</code> seats available per row. Operations will proceed normally.</li>
<li><strong>Full</strong>: <code>max_tree</code> will have 0s at leaves, <code>sum_tree</code> will have 0s at leaves. <code>find_first_fit</code> and <code>query_sum</code> will correctly report 0 availability, leading to failed bookings.</li>
</ul>
</li>
<li><strong>Concurrency</strong>: The current implementation is single-threaded and does not account for concurrent bookings. In a real-world scenario, this would need explicit locking or a transactional approach.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong><code>scatter</code> Performance Optimization</strong>:<ul>
<li>The <code>O(N log N)</code> complexity of <code>scatter</code> can be a significant bottleneck for large <code>N</code>. This is because it iterates through rows, performing an <code>O(log N)</code> update for each row that takes seats.</li>
<li><strong>Alternative Segment Tree</strong>: A more advanced segment tree could support "range fill" operations, or find the <em>first</em> <code>k</code> available seats in a range and update them. This typically involves a segment tree that stores not just sums/maxes, but also the actual available seats and potentially allows for lazy propagation on range updates where <code>k</code> seats are filled. However, this is significantly more complex to implement than simple range sum/max trees.</li>
<li><strong>Binary Search on Rows</strong>: If we wanted to optimize the loop within <code>scatter</code>, we could perhaps binary search for the next row that can take seats, but this doesn't fully remove the <code>N</code> factor for updates unless a batch update mechanism is in place.</li>
</ul>
</li>
<li><strong>Readability &amp; Maintainability</strong>:<ul>
<li><strong>Docstrings</strong>: Add comprehensive docstrings for each method explaining its purpose, parameters, and return value.</li>
<li><strong>Comments</strong>: Add comments for complex logic, especially within segment tree operations.</li>
<li><strong>Magic Numbers</strong>: <code>4 * n</code> for segment tree size is a common heuristic but could be explained.</li>
<li><strong>Helper Methods</strong>: The segment tree methods (<code>build</code>, <code>query</code>, <code>update</code>, <code>find_first_fit</code>) could be encapsulated into a separate <code>SegmentTree</code> helper class for better modularity if more than two trees were needed or if the segment tree logic was more complex. Here, with only two distinct trees, it's acceptable.</li>
</ul>
</li>
<li><strong>Type Hinting</strong>: The code already uses type hints, which is excellent for readability and maintainability.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck</strong>: As highlighted, the <code>scatter</code> method's <code>O(N log N)</code> worst-case time complexity is the primary performance concern. For <code>N</code> up to <code>10^5</code> (typical competitive programming constraint for <code>N log N</code>), this might be acceptable. For larger <code>N</code> or very frequent <code>scatter</code> calls, it could be too slow. A real-world booking system would likely need a more sophisticated data structure for range-filling operations or accept the <code>O(N log N)</code> if <code>N</code> is bounded.</li>
<li><strong>No Security Vulnerabilities</strong>: For this specific algorithmic problem, there are no inherent security vulnerabilities. The system deals with integer seat counts and indices, with no external inputs or sensitive data exposed in a way that would lead to injection attacks or data breaches.</li>
</ul>


### Code:
```python
class BookMyShow:

    def __init__(self, n: int, m: int):
        self.n = n
        self.m = m
        self.seats = [0] * n  # Track seats occupied in each row
        
        # Segment tree for maximum available seats in any row
        self.max_tree = [0] * (4 * n)
        self.build_max(0, 0, n - 1, m)
        
        # Segment tree for total available seats
        self.sum_tree = [0] * (4 * n)
        self.build_sum(0, 0, n - 1, m)
    
    def build_max(self, node, start, end, val):
        if start == end:
            self.max_tree[node] = val
        else:
            mid = (start + end) // 2
            self.build_max(2 * node + 1, start, mid, val)
            self.build_max(2 * node + 2, mid + 1, end, val)
            self.max_tree[node] = max(self.max_tree[2 * node + 1], self.max_tree[2 * node + 2])
    
    def build_sum(self, node, start, end, val):
        if start == end:
            self.sum_tree[node] = val
        else:
            mid = (start + end) // 2
            self.build_sum(2 * node + 1, start, mid, val)
            self.build_sum(2 * node + 2, mid + 1, end, val)
            self.sum_tree[node] = self.sum_tree[2 * node + 1] + self.sum_tree[2 * node + 2]
    
    def query_max(self, node, start, end, l, r):
        if r < start or l > end:
            return 0
        if l <= start and end <= r:
            return self.max_tree[node]
        mid = (start + end) // 2
        return max(self.query_max(2 * node + 1, start, mid, l, r),
                   self.query_max(2 * node + 2, mid + 1, end, l, r))
    
    def query_sum(self, node, start, end, l, r):
        if r < start or l > end:
            return 0
        if l <= start and end <= r:
            return self.sum_tree[node]
        mid = (start + end) // 2
        return (self.query_sum(2 * node + 1, start, mid, l, r) +
                self.query_sum(2 * node + 2, mid + 1, end, l, r))
    
    def find_first_fit(self, node, start, end, l, r, k):
        # Find first row in range [l, r] with at least k available seats
        if r < start or l > end or self.max_tree[node] < k:
            return -1
        if start == end:
            return start
        mid = (start + end) // 2
        # Try left subtree first
        left_result = self.find_first_fit(2 * node + 1, start, mid, l, r, k)
        if left_result != -1:
            return left_result
        return self.find_first_fit(2 * node + 2, mid + 1, end, l, r, k)
    
    def update(self, node, start, end, idx, val):
        if start == end:
            self.max_tree[node] = val
            self.sum_tree[node] = val
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(2 * node + 1, start, mid, idx, val)
            else:
                self.update(2 * node + 2, mid + 1, end, idx, val)
            self.max_tree[node] = max(self.max_tree[2 * node + 1], self.max_tree[2 * node + 2])
            self.sum_tree[node] = self.sum_tree[2 * node + 1] + self.sum_tree[2 * node + 2]

    def gather(self, k: int, maxRow: int) -> List[int]:
        if maxRow >= self.n:
            maxRow = self.n - 1
        
        # Find first row with at least k consecutive seats
        row = self.find_first_fit(0, 0, self.n - 1, 0, maxRow, k)
        
        if row == -1:
            return []
        
        # Allocate seats
        seat = self.seats[row]
        self.seats[row] += k
        available = self.m - self.seats[row]
        self.update(0, 0, self.n - 1, row, available)
        
        return [row, seat]

    def scatter(self, k: int, maxRow: int) -> bool:
        if maxRow >= self.n:
            maxRow = self.n - 1
        
        # Check if enough total seats available
        total = self.query_sum(0, 0, self.n - 1, 0, maxRow)
        if total < k:
            return False
        
        # Allocate seats row by row
        for row in range(maxRow + 1):
            if k == 0:
                break
            available = self.m - self.seats[row]
            if available > 0:
                take = min(available, k)
                self.seats[row] += take
                k -= take
                remaining = self.m - self.seats[row]
                self.update(0, 0, self.n - 1, row, remaining)
        
        return True
```
