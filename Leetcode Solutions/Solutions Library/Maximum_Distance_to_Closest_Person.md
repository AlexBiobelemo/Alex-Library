## Maximum Distance to Closest Person
**Language:** python
**Tags:** python,array,linear scan,gap analysis,optimization

### Description:
<p>This code addresses the problem of finding the maximum distance an unseated person, "Alex", can sit from the <em>closest</em> occupied seat. Alex wants to maximize this minimum distance.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to determine the largest possible distance Alex can achieve from <em>any</em> person. This means identifying the largest continuous empty segment where Alex can sit right in the middle (or at one end if it's at the start or end of the row) to maximize the distance to their nearest neighbor.</p>
<ul>
<li><strong>Input</strong>: A list <code>seats</code> of integers, where <code>1</code> represents an occupied seat and <code>0</code> represents an empty seat.</li>
<li><strong>Output</strong>: An integer representing the maximum distance Alex can sit from their closest neighbor.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm identifies three distinct scenarios where Alex might achieve the maximum distance:</p>
<ol>
<li><strong>Seats at the beginning</strong>: If there are empty seats before the very first person (<code>1</code>).</li>
<li><strong>Seats between two people</strong>: If there's a gap of empty seats between two occupied seats.</li>
<li><strong>Seats at the end</strong>: If there are empty seats after the very last person (<code>1</code>).</li>
</ol>
<p>The code processes these scenarios in a single pass after locating the first person:</p>
<ul>
<li><strong>Initialize</strong>: It first finds the index of the <code>first_person_idx</code>.</li>
<li><strong>Handle Beginning Gap</strong>: <code>max_dist</code> is initialized with <code>first_person_idx</code>, which correctly calculates the distance if Alex sits at index 0 and <code>first_person_idx</code> is the first person. This covers the first scenario.</li>
<li><strong>Handle Middle Gaps</strong>: It then iterates from <code>first_person_idx + 1</code> to the end of the <code>seats</code> array.<ul>
<li>When it encounters another person (<code>seats[i] == 1</code>), it calculates the length of the gap (<code>i - prev_person_idx</code>).</li>
<li>For a gap, the optimal place for Alex is exactly in the middle. The distance to the closest person is <code>gap_length // 2</code> (integer division).</li>
<li><code>max_dist</code> is updated if this value is greater than the current <code>max_dist</code>.</li>
<li><code>prev_person_idx</code> is updated to the current person's index.</li>
</ul>
</li>
<li><strong>Handle End Gap</strong>: After the loop finishes, <code>prev_person_idx</code> holds the index of the <em>last</em> person found. The distance for any empty seats at the very end of the row is <code>(n - 1) - prev_person_idx</code>.</li>
<li><strong>Final Result</strong>: <code>max_dist</code> is updated one last time with this end gap distance, and then returned.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Single-Pass (Mostly)</strong>: The core logic uses a single loop to find all people after the first, making it efficient. An initial loop finds the very first person, then the main loop processes the rest.</li>
<li><strong>Separate Case Handling</strong>: Explicitly handles the beginning, middle, and end gaps. This simplifies the logic for each type of gap rather than trying to generalize.</li>
<li><strong><code>prev_person_idx</code></strong>: This variable is crucial for keeping track of the last seen person, enabling the calculation of gap lengths.</li>
<li><strong>Integer Division (<code>// 2</code>)</strong>: Correctly calculates the maximum distance for middle gaps. For example, in <code>1 0 0 0 1</code>, the gap is 4. Alex sits at <code>1 0 (X) 0 1</code>, distance is 2 (<code>4 // 2</code>).</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Finding <code>first_person_idx</code>: In the worst case (e.g., <code>[0,0,0,1,...]</code>), this loop runs N times.</li>
<li>Main loop: Iterates from <code>first_person_idx + 1</code> to <code>n-1</code>, visiting each seat at most once.</li>
<li>Overall, the number of operations scales linearly with the number of seats <code>N</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm only uses a few constant-space variables (<code>n</code>, <code>first_person_idx</code>, <code>max_dist</code>, <code>prev_person_idx</code>, <code>i</code>). No auxiliary data structures are created that scale with input size.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly:</p>
<ul>
<li><strong><code>[0,0,0,1,0,1]</code></strong>:<ul>
<li><code>first_person_idx = 3</code>. <code>max_dist</code> initialized to <code>3</code>.</li>
<li><code>i = 5</code>, <code>seats[5] == 1</code>. Gap <code>5-3 = 2</code>. <code>max_dist = max(3, 2//2) = max(3,1) = 3</code>. <code>prev_person_idx = 5</code>.</li>
<li>End gap: <code>max(3, (6-1)-5) = max(3, 0) = 3</code>.</li>
<li>Correct output: 3 (Alex sits at index 0).</li>
</ul>
</li>
<li><strong><code>[1,0,1,0,0,0]</code></strong>:<ul>
<li><code>first_person_idx = 0</code>. <code>max_dist</code> initialized to <code>0</code>.</li>
<li><code>i = 2</code>, <code>seats[2] == 1</code>. Gap <code>2-0 = 2</code>. <code>max_dist = max(0, 2//2) = max(0,1) = 1</code>. <code>prev_person_idx = 2</code>.</li>
<li>End gap: <code>max(1, (6-1)-2) = max(1, 3) = 3</code>.</li>
<li>Correct output: 3 (Alex sits at index 5).</li>
</ul>
</li>
<li><strong><code>[1,0,0,0,1]</code></strong>:<ul>
<li><code>first_person_idx = 0</code>. <code>max_dist</code> initialized to <code>0</code>.</li>
<li><code>i = 4</code>, <code>seats[4] == 1</code>. Gap <code>4-0 = 4</code>. <code>max_dist = max(0, 4//2) = max(0,2) = 2</code>. <code>prev_person_idx = 4</code>.</li>
<li>End gap: <code>max(2, (5-1)-4) = max(2, 0) = 2</code>.</li>
<li>Correct output: 2 (Alex sits at index 2).</li>
</ul>
</li>
<li><strong><code>[0,1,0]</code></strong>:<ul>
<li><code>first_person_idx = 1</code>. <code>max_dist</code> initialized to <code>1</code>.</li>
<li>Loop doesn't find another person. <code>prev_person_idx = 1</code>.</li>
<li>End gap: <code>max(1, (3-1)-1) = max(1, 1) = 1</code>.</li>
<li>Correct output: 1 (Alex sits at index 0 or index 2).</li>
</ul>
</li>
</ul>
<p>The problem constraints typically guarantee at least one <code>0</code> and at least one <code>1</code> in <code>seats</code>, ensuring <code>first_person_idx</code> is always valid and there's always at least one place Alex can sit.</p>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The current code is already very readable, with descriptive variable names and helpful comments. No significant readability improvements are needed.</li>
<li><strong>Alternative Approach (Two-Pass with Left/Right Arrays)</strong>:<ol>
<li>Create a <code>left_dist</code> array: For each seat <code>i</code>, store the distance to the nearest person on its left. If no person on the left, use infinity.</li>
<li>Create a <code>right_dist</code> array: For each seat <code>i</code>, store the distance to the nearest person on its right. If no person on the right, use infinity.</li>
<li>Iterate through <code>seats</code>. For each empty seat (<code>0</code>), calculate <code>min(left_dist[i], right_dist[i])</code>. Track the maximum of these minimums.
This approach is conceptually sound and easier to parallelize but requires O(N) auxiliary space, making the current O(1) space solution generally preferred.</li>
</ol>
</li>
<li><strong>Alternative (Optimized Single Pass)</strong>: While the current solution is effectively a single pass after finding the initial person, one could combine the "find first person" part into the main loop if <code>prev_person_idx</code> is carefully initialized (e.g., to <code>-infinity</code> or by checking if it's the first person found). However, the current separation of concerns (beginning, middle, end) is quite clear and robust.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no security implications. The code operates purely on an in-memory list of integers and does not interact with external systems, user input, files, or network resources.</li>
<li><strong>Performance</strong>: The solution is optimally efficient with O(N) time complexity and O(1) space complexity. It's not possible to solve this problem faster than O(N) because every seat must be examined at least once to determine its state.</li>
</ul>


### Code:
```python
class Solution(object):
    def maxDistToClosest(self, seats):
        """
        :type seats: List[int]
        :rtype: int
        """
        n = len(seats)
        
        # Find the index of the first person
        first_person_idx = -1
        for i in range(n):
            if seats[i] == 1:
                first_person_idx = i
                break
        
        # Initialize max_dist with the distance for seats before the first person.
        # If Alex sits at index 0, the distance to the first person (at first_person_idx) is first_person_idx.
        # This handles cases like [0,0,0,1,0,1] where the max distance might be at the beginning.
        max_dist = first_person_idx 
        
        # Iterate through the rest of the seats to find gaps between consecutive people
        prev_person_idx = first_person_idx
        for i in range(first_person_idx + 1, n):
            if seats[i] == 1:
                # Found a new person at index 'i'.
                # The gap is between prev_person_idx and i.
                # The maximum distance to the closest person in this gap is half the length of the gap.
                # Example: 1 0 0 0 1. Gap length is 4. Max dist is 4 // 2 = 2.
                max_dist = max(max_dist, (i - prev_person_idx) // 2)
                prev_person_idx = i
        
        # After iterating through all people, consider the empty seats at the end of the row.
        # The last person found is at prev_person_idx.
        # If there are empty seats after prev_person_idx, the maximum distance is (n - 1) - prev_person_idx.
        # Example: [1,0,1,0,0,0]. prev_person_idx = 2. n = 6. (6-1) - 2 = 3.
        max_dist = max(max_dist, (n - 1) - prev_person_idx)
        
        return max_dist
```
