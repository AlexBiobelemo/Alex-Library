## Jump Game
**Language:** python
**Tags:** greedy algorithm,jump game,array,python

### Description:
<p>This code solves the "Jump Game" problem, a classic algorithm challenge.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>canJump</code> function determines if it's possible to reach the last index of an array given a set of jump lengths.</p>
<ul>
<li><strong>Input</strong>: <code>nums</code>, a list of non-negative integers where <code>nums[i]</code> represents the maximum jump length from index <code>i</code>.</li>
<li><strong>Output</strong>: <code>True</code> if the last index can be reached from the first index (index 0), <code>False</code> otherwise.</li>
<li><strong>Goal</strong>: Find the most efficient way to check reachability.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a greedy approach to track the farthest index reachable at any point.</p>
<ul>
<li><strong>Initialization</strong>:<ul>
<li><code>n</code> stores the total number of elements in <code>nums</code>.</li>
<li><code>max_reach</code> is initialized to <code>0</code>, representing the farthest index we can currently reach starting from index <code>0</code>.</li>
</ul>
</li>
<li><strong>Iteration</strong>:<ul>
<li>The code iterates through the array from <code>i = 0</code> to <code>n-1</code>.</li>
<li><strong>Stuck Check</strong>: Inside the loop, it first checks <code>if i &gt; max_reach</code>. If the current index <code>i</code> is beyond what we could ever reach, it means we're stuck and cannot proceed further. In this case, it's impossible to reach the end, so the function returns <code>False</code>.</li>
<li><strong>Update Max Reach</strong>: It then updates <code>max_reach</code>. From the current position <code>i</code>, we can jump up to <code>i + nums[i]</code>. <code>max_reach</code> is updated to be the maximum of its current value and this new potential reach (<code>i + nums[i]</code>). This ensures <code>max_reach</code> always holds the <em>farthest</em> index we've ever been able to touch.</li>
<li><strong>Goal Check</strong>: After updating <code>max_reach</code>, it checks <code>if max_reach &gt;= n - 1</code>. If the farthest reachable point is already at or beyond the last index (<code>n-1</code>), it means we have successfully found a path to the end, so the function returns <code>True</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Greedy Approach</strong>: The core design decision is the use of a greedy algorithm. At each step, it calculates the <em>absolute farthest</em> point achievable. This strategy works because to reach the end, we only care about whether <em>any</em> path exists, not the optimal path (e.g., fewest jumps). Maximizing the reach at each step implicitly covers all intermediate paths.</li>
<li><strong><code>max_reach</code> Variable</strong>: This single variable is the key state. It effectively summarizes all possible paths explored so far by only storing the most optimistic outcome (the maximum reachable index). This avoids the need for complex path tracking or a queue/stack like in BFS/DFS.</li>
<li><strong>Efficiency</strong>: This greedy strategy avoids the overhead of more complex graph traversal algorithms (like Breadth-First Search or Dynamic Programming), which would typically involve storing states or building a DP table.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The algorithm iterates through the <code>nums</code> array exactly once, performing constant-time operations within the loop. <code>N</code> is the length of the <code>nums</code> array.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>It uses a fixed number of variables (<code>n</code>, <code>max_reach</code>, <code>i</code>) regardless of the input size. No additional data structures are allocated that scale with <code>N</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm handles several edge cases correctly:</p>
<ul>
<li><strong><code>nums = [0]</code></strong>: <code>n=1</code>. <code>i=0</code>. <code>max_reach</code> becomes <code>0</code>. <code>0 &gt;= 1-1</code> is <code>True</code>. Returns <code>True</code>. (Correct, already at the last index).</li>
<li><strong><code>nums = [0, 1]</code></strong>: <code>n=2</code>.<ul>
<li><code>i=0</code>, <code>nums[0]=0</code>. <code>max_reach</code> is <code>0</code>. <code>0 &gt; 0</code> is <code>False</code>. <code>max_reach = max(0, 0+0) = 0</code>. <code>0 &gt;= 1</code> is <code>False</code>.</li>
<li><code>i=1</code>. Now <code>i</code> (1) <code>&gt; max_reach</code> (0) is <code>True</code>. Returns <code>False</code>. (Correct, stuck at index 0).</li>
</ul>
</li>
<li><strong><code>nums = [3, 2, 1, 0, 4]</code></strong>: (Cannot reach)<ul>
<li><code>i=0, nums[0]=3</code>. <code>max_reach</code> becomes <code>3</code>.</li>
<li><code>i=1, nums[1]=2</code>. <code>max_reach</code> becomes <code>max(3, 1+2)=3</code>.</li>
<li><code>i=2, nums[2]=1</code>. <code>max_reach</code> becomes <code>max(3, 2+1)=3</code>.</li>
<li><code>i=3, nums[3]=0</code>. <code>max_reach</code> becomes <code>max(3, 3+0)=3</code>.</li>
<li><code>i=4</code>. Current <code>i</code> (4) <code>&gt; max_reach</code> (3) is <code>True</code>. Returns <code>False</code>. (Correct, stuck at index 3).</li>
</ul>
</li>
<li><strong><code>nums = [2, 0, 0]</code></strong>: (Can reach)<ul>
<li><code>i=0, nums[0]=2</code>. <code>max_reach</code> becomes <code>2</code>. <code>2 &gt;= 2</code> is <code>True</code>. Returns <code>True</code>. (Correct, can jump over the zeros).</li>
</ul>
</li>
<li><strong>Empty <code>nums</code> list</strong>: The provided code, if <code>nums</code> is empty (<code>n=0</code>), <code>range(n)</code> would be empty and the loop wouldn't run, resulting in no return value. In typical LeetCode contexts, <code>nums</code> is guaranteed to be non-empty. If it could be empty, an explicit check (<code>if not nums: return True</code> or <code>False</code>) would be needed depending on the problem's definition for an empty list. Assuming <code>nums</code> is never empty.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite readable. Variable names are descriptive, and comments explain the key conditions.</li>
<li><strong>Performance</strong>: The current O(N) time and O(1) space solution is optimal for this problem, as every position potentially needs to be considered to determine reachability. There are no significant performance improvements possible for this specific algorithm.</li>
<li><strong>Alternative Approaches (less efficient):</strong><ul>
<li><strong>Dynamic Programming (O(N^2) time, O(N) space)</strong>: A <code>dp</code> array where <code>dp[i]</code> is <code>True</code> if index <code>i</code> is reachable. <code>dp[i] = any(dp[j] and j + nums[j] &gt;= i)</code> for all <code>j &lt; i</code>. This is more complex and less efficient.</li>
<li><strong>Breadth-First Search (BFS) (O(N^2) time in worst case, O(N) space)</strong>: Treat indices as nodes in a graph and jump lengths as edges. Explore reachable nodes level by level. The greedy approach essentially optimizes BFS by only tracking the maximum reachable point, avoiding explicit queue management.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no direct security implications for this algorithm, as it deals purely with array traversal and numerical calculations without external input or system interaction.</li>
<li><strong>Performance</strong>: The algorithm is highly performant due to its linear time and constant space complexity. It's suitable for large input arrays. Integer overflow is not a concern given typical problem constraints for <code>nums[i]</code> and <code>n</code> (usually up to 10^5), as <code>i + nums[i]</code> won't exceed standard integer limits.</li>
</ul>


### Code:
```python
class Solution(object):
    def canJump(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        n = len(nums)
        max_reach = 0 # Represents the farthest index that can be reached so far.

        for i in range(n):
            # If the current index 'i' is beyond the farthest point we could reach,
            # it means we are stuck and cannot reach the end.
            if i > max_reach:
                return False

            # Update the farthest index that can be reached.
            # From the current position 'i', we can jump up to 'i + nums[i]'.
            max_reach = max(max_reach, i + nums[i])

            # If the farthest reachable index is already at or beyond the last index,
            # we know we can reach the end.
            if max_reach >= n - 1:
                return True
```
