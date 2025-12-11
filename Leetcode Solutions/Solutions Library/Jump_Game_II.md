## Jump Game II
**Language:** python
**Tags:** python,greedy algorithm,array,minimum jumps

### Description:
<p>This code solves the "Jump Game II" problem, which asks for the minimum number of jumps to reach the last index of an array. It uses a clever greedy approach.</p>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem</strong>: Given an array <code>nums</code> of non-negative integers, where <code>nums[i]</code> represents the maximum jump length from index <code>i</code>.</li>
<li><strong>Goal</strong>: Find the minimum number of jumps required to reach the last index (index <code>n-1</code>) starting from the first index (index <code>0</code>).</li>
<li><strong>Code's Purpose</strong>: To implement an efficient, greedy algorithm that calculates this minimum number of jumps.</li>
</ul>
<h3>2. How It Works</h3>
<p>The algorithm employs a greedy strategy, iterating through the array and keeping track of how far it can reach. It's essentially simulating a "level-by-level" traversal without explicitly using a queue (like in BFS).</p>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li><code>jumps</code>: Counts the total jumps made. Starts at 0.</li>
<li><code>current_end</code>: Marks the farthest index reachable with the <em>current</em> number of jumps. Initially 0. When the iteration <code>i</code> reaches <code>current_end</code>, it signifies that we must make another jump.</li>
<li><code>farthest</code>: Tracks the maximum index reachable from <em>any</em> position visited so far within the current jump's range. Initially 0.</li>
</ul>
</li>
<li><p><strong>Iteration</strong>:</p>
<ul>
<li>The loop iterates from <code>i = 0</code> up to <code>n - 2</code> (excluding the last element as a jump source itself).</li>
<li>In each step <code>i</code>, it updates <code>farthest</code>: <code>farthest = max(farthest, i + nums[i])</code>. This ensures <code>farthest</code> always holds the maximum reach possible from any point within the current jump's scope.</li>
<li><strong>Jump Trigger</strong>: If <code>i</code> becomes equal to <code>current_end</code>:<ul>
<li>This means we have reached the end of the range covered by the <em>previous</em> jump.</li>
<li>We must make a new jump, so <code>jumps</code> is incremented.</li>
<li>The new <code>current_end</code> becomes <code>farthest</code>. This new <code>current_end</code> represents the maximum range we could have achieved with the newly incremented <code>jumps</code>.</li>
<li><strong>Early Exit</strong>: Immediately after updating <code>current_end</code>, if <code>current_end</code> already reaches or surpasses <code>n - 1</code>, we have successfully reached the end, so we return <code>jumps</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Return</strong>: If the loop finishes without an early exit (which implies <code>n &lt;= 1</code> or the last jump reaches exactly <code>n-1</code> at the end of the loop), <code>jumps</code> is returned.</p>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Greedy Approach</strong>: The core idea is to always try to extend the maximum reach (<code>farthest</code>) possible from the current segment of the array. When a jump is "forced" (when <code>i == current_end</code>), it makes the best possible jump to <code>farthest</code>. This ensures the minimum number of jumps because it always maximizes progress with each jump.</li>
<li><strong><code>current_end</code> as a Boundary</strong>: This variable is crucial for deciding <em>when</em> to increment the jump count. It acts as a frontier: once we cross it, a jump has been "completed," and we start a new one, extending our reach as far as possible.</li>
<li><strong>No Explicit Graph/Queue</strong>: Unlike a typical BFS approach for shortest path problems, this greedy solution avoids explicit graph construction or queue management, making it more streamlined.</li>
<li><strong>Loop Limit (<code>n-1</code>)</strong>: The loop stops at <code>n-1</code> because once <code>i</code> reaches <code>n-1</code>, we are already at the destination, and no further jumps <em>from</em> <code>n-1</code> are needed.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The code iterates through the <code>nums</code> array once using a single <code>for</code> loop.</li>
<li>All operations inside the loop (comparisons, <code>max</code>, additions, assignments) are constant time, O(1).</li>
<li>Therefore, the total time complexity is directly proportional to the number of elements <code>N</code> in the <code>nums</code> array.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>n</code>, <code>jumps</code>, <code>current_end</code>, <code>farthest</code>) regardless of the input array size. No auxiliary data structures that scale with <code>N</code> are employed.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n &lt;= 1</code> (Empty or Single Element Array)</strong>:<ul>
<li><strong>Case</strong>: <code>nums = []</code> or <code>nums = [x]</code>.</li>
<li><strong>Code Behavior</strong>: The initial <code>if n &lt;= 1: return 0</code> correctly handles this, returning 0 jumps as no jumps are needed.</li>
</ul>
</li>
<li><strong>Reaching <code>n-1</code> Early</strong>:<ul>
<li><strong>Case</strong>: The <code>farthest</code> reach extends beyond or exactly to <code>n-1</code> midway through the iteration.</li>
<li><strong>Code Behavior</strong>: The <code>if current_end &gt;= n - 1: return jumps</code> check ensures that as soon as the last index is reachable with the <em>current</em> count of jumps, the result is returned immediately, preventing unnecessary further iteration and guaranteeing the minimum jumps.</li>
</ul>
</li>
<li><strong>Guaranteed Reachability</strong>: The typical problem statement for "Jump Game II" guarantees that the last index can always be reached. If this were not guaranteed, the algorithm would return the number of jumps calculated up to <code>n-1</code>, even if the last index was truly unreachable. This would be incorrect for an unreachable scenario. However, under standard problem constraints, it's correct.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Robustness for Unreachable Target</strong>: If the problem didn't guarantee reachability, one could add a check: if <code>i == current_end</code> and <code>farthest &lt;= i</code> (meaning we're stuck or cannot move forward), then return <code>-1</code> to indicate impossibility. However, for the given problem constraints, this isn't strictly necessary.</li>
<li><strong>Readability</strong>: The variable names (<code>jumps</code>, <code>current_end</code>, <code>farthest</code>) are quite descriptive and align well with the greedy strategy, making the code relatively easy to understand for this type of problem.</li>
<li><strong>Alternative Approaches</strong>:<ul>
<li><strong>Breadth-First Search (BFS)</strong>: This problem can be modeled as finding the shortest path in a graph where indices are nodes and jumps are edges. BFS guarantees the minimum number of steps. While also O(N) for this specific problem due to properties of <code>nums[i]</code>, the greedy solution is often more concise and performs slightly better in practice as it avoids explicit queue management.</li>
<li><strong>Dynamic Programming</strong>: <code>dp[i]</code> could store the minimum jumps to reach index <code>i</code>. <code>dp[i] = 1 + min(dp[j])</code> for all <code>j &lt; i</code> such that <code>j + nums[j] &gt;= i</code>. This would be an O(N^2) solution, which is less efficient than the greedy O(N) approach.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The O(N) time complexity and O(1) space complexity are optimal for this problem, as every element must be considered at least once to determine the best jumps. You cannot do better than linear time.</li>
<li><strong>Input Validation</strong>: Assuming <code>nums</code> contains non-negative integers as per problem constraints. If <code>nums[i]</code> could be negative, the logic for <code>farthest</code> might need adjustment, but <code>nums[i] &lt; 0</code> for jump lengths is usually not allowed. The problem typically specifies valid input ranges.</li>
</ul>


### Code:
```python
class Solution(object):
    def jump(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = len(nums)
        if n <= 1:
            return 0

        jumps = 0
        current_end = 0  # The farthest index reachable with the current number of jumps
        farthest = 0     # The farthest index reachable from any point considered so far

        # Iterate through the array. We don't need to consider the last element
        # as a jump source because if we reach it, we are done.
        for i in range(n - 1):
            # Update the farthest point we can reach from the current position i
            farthest = max(farthest, i + nums[i])

            # If we have reached the end of the range that the current jump covers
            if i == current_end:
                # We must make another jump
                jumps += 1
                # The new current_end becomes the farthest point we could reach
                # from the previous jump's range
                current_end = farthest
                
                # If the new current_end already reaches or surpasses the last index,
                # we have found the minimum number of jumps.
                if current_end >= n - 1:
                    return jumps
        
        return jumps
```
