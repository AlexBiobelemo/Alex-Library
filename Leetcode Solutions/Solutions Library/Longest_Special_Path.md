## Longest Special Path
**Language:** python
**Tags:** python,object-oriented programming,depth-first search,graph traversal,hash map

### Description:
<p>This code finds the "longest special path" in a given graph. Based on the logic, a "special path" is defined as a simple path (no repeated nodes) where all <code>nums</code> values of the nodes in the path are unique. If multiple paths have the maximum length, the one with the minimum number of nodes is preferred. The "length" of the path is the sum of edge weights.</p>
<p>The algorithm uses Depth-First Search (DFS) combined with a sliding window approach to efficiently track the valid "special path" segment within the current DFS recursion stack.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Find the longest path in a graph such that all node values (<code>nums[node_id]</code>) along the path are unique. If there's a tie in length, prioritize the path with fewer nodes.</li>
<li><strong>Input:</strong><ul>
<li><code>edges</code>: A list of <code>[u, v, w]</code> representing an undirected edge between <code>u</code> and <code>v</code> with weight <code>w</code>.</li>
<li><code>nums</code>: A list where <code>nums[i]</code> is the integer value associated with node <code>i</code>.</li>
</ul>
</li>
<li><strong>Output:</strong> A list <code>[max_length, min_nodes]</code> representing the length of the longest special path and the minimum number of nodes for such a path.</li>
<li><strong>Assumption:</strong> The graph is likely a tree or a general graph where only simple paths (no repeated nodes) are considered. The <code>p</code> parameter in DFS prevents going back to the immediate parent, treating it like a tree traversal for path finding. The problem implicitly assumes a single connected component starting from node 0, or that node 0 is a sufficient root to find the desired path.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Graph Representation:</strong> An adjacency list <code>adj</code> is built from the <code>edges</code>, storing neighbors and their corresponding edge weights.</li>
<li><strong>Initialization:</strong><ul>
<li><code>res = [0, 1]</code> initializes the global result. A single node itself is a path of length 0 and 1 node, which is always valid.</li>
<li><code>dist_stack</code>: A stack (list) to store the cumulative distance from the initial DFS root to each node currently in the recursion path. The index in this stack corresponds to the depth.</li>
<li><code>last_seen</code>: A hash map <code>{value: depth_index}</code> to quickly check if a <code>nums</code> value has been encountered in the <em>current</em> path and at which depth it was last seen.</li>
</ul>
</li>
<li><strong>DFS Traversal (<code>dfs(u, p, start_index, curr_dist)</code>):</strong><ul>
<li><code>u</code>: Current node.</li>
<li><code>p</code>: Parent node (to avoid immediate backtracking in an undirected graph).</li>
<li><code>start_index</code>: The depth index in <code>dist_stack</code> where the <em>current valid special path</em> begins. This defines the "sliding window."</li>
<li><code>curr_dist</code>: Cumulative distance from the initial DFS root to <code>u</code>.</li>
<li><strong>Duplicate Check:</strong> When visiting <code>u</code>, <code>nums[u]</code> is checked against <code>last_seen</code>.<ul>
<li>If <code>nums[u]</code> was previously seen at <code>prev_index</code> and <code>prev_index &gt;= start_index</code>, it means <code>nums[u]</code> is a duplicate within the current valid window. The <code>new_start</code> for the valid path must be adjusted to <code>prev_index + 1</code> to exclude the previous occurrence and everything before it.</li>
</ul>
</li>
<li><strong>Path Calculation:</strong><ul>
<li><code>path_len</code>: Calculated as <code>curr_dist - dist_stack[new_start]</code>. This effectively subtracts the distance to the start of the valid window, yielding the length of the unique-value path segment. If <code>new_start</code> is beyond the current <code>dist_stack</code> length, it means the current node is the only valid node in the path segment, so length is 0.</li>
<li><code>num_nodes</code>: <code>len(dist_stack) - new_start + 1</code>. This counts nodes from <code>new_start</code> to the current node.</li>
</ul>
</li>
<li><strong>Result Update:</strong> <code>res</code> is updated if <code>path_len</code> is greater, or if <code>path_len</code> is equal and <code>num_nodes</code> is smaller.</li>
<li><strong>Pre-Recursion:</strong> The <code>curr_dist</code> is pushed onto <code>dist_stack</code>, and <code>nums[u]</code>'s current <code>depth_index</code> is stored in <code>last_seen</code>.</li>
<li><strong>Recursive Call:</strong> DFS is called for all unvisited neighbors <code>v</code>.</li>
<li><strong>Backtracking:</strong> After visiting all children, the entries added for <code>u</code> are removed from <code>dist_stack</code> and <code>last_seen</code> to restore the state for the parent's continued DFS.</li>
</ul>
</li>
<li><strong>Initial Call:</strong> The DFS starts from node 0 with no parent, <code>start_index</code> 0, and <code>curr_dist</code> 0.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Adjacency List:</strong> Standard and efficient for representing sparse graphs, allowing quick access to neighbors.</li>
<li><strong>DFS:</strong> A natural choice for exploring all paths from a starting node in a tree-like structure.</li>
<li><strong><code>sys.setrecursionlimit</code>:</strong> Addresses the potential for deep recursion stacks in highly skewed (path-like) trees, preventing <code>RecursionError</code>.</li>
<li><strong><code>dist_stack</code>:</strong> An implicit representation of the current path from the DFS root. Storing cumulative distances allows for <code>O(1)</code> calculation of any sub-path length by subtracting two cumulative distances.</li>
<li><strong><code>last_seen</code> Hash Map:</strong> Provides <code>O(1)</code> average-time lookup for node values within the current path, critical for the unique-value constraint. It stores <em>depth indices</em>, not just boolean presence, which is key for correctly adjusting the <code>start_index</code>.</li>
<li><strong>Sliding Window Logic (<code>start_index</code>, <code>new_start</code>):</strong> This is the core algorithmic technique. It dynamically maintains the longest possible valid path segment (unique <code>nums</code> values) ending at the current node <code>u</code> as the DFS progresses.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong> O(N + E)<ul>
<li><strong>Graph Construction:</strong> O(N + E), where N is the number of nodes and E is the number of edges.</li>
<li><strong>DFS Traversal:</strong> Each node is visited once, and each edge is processed twice (once for <code>u</code> to <code>v</code>, once for <code>v</code> to <code>u</code> in an undirected graph).</li>
<li><strong>Operations within DFS:</strong> <code>adj</code> list iteration, <code>dist_stack</code> (append, pop, index access), and <code>last_seen</code> (get, set, del) are all <code>O(1)</code> on average (for hash map).</li>
<li><strong>Total:</strong> The overall time complexity is dominated by the graph traversal, resulting in O(N + E).</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> O(N + E)<ul>
<li><strong>Adjacency List (<code>adj</code>):</strong> O(N + E) to store the graph.</li>
<li><strong><code>dist_stack</code>:</strong> In the worst case (a path graph or highly skewed tree), it can store N elements, O(N).</li>
<li><strong><code>last_seen</code>:</strong> In the worst case (all <code>nums</code> values unique), it can store N elements, O(N).</li>
<li><strong>Recursion Stack:</strong> In the worst case (a path graph), the recursion depth can be N, O(N).</li>
<li><strong>Total:</strong> The overall space complexity is O(N + E).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Single Node Graph (N=1):</strong> The code initializes <code>res = [0, 1]</code>. The DFS starts, <code>dist_stack</code> appends <code>0</code>, <code>last_seen</code> adds <code>nums[0]</code>. <code>path_len</code> will be <code>0</code>, <code>num_nodes</code> will be <code>1</code>. The <code>res</code> remains <code>[0, 1]</code>, which is correct.</li>
<li><strong>Disconnected Graph:</strong> The DFS starts from node 0. If the graph is disconnected and the component containing node 0 does not contain the longest special path, the result will be incorrect. This algorithm finds the longest special path in the <em>connected component reachable from node 0</em>. For a general graph that might be disconnected, an outer loop iterating <code>dfs</code> for all unvisited nodes would be needed. Assuming the problem implies a single connected component or that node 0 is a sufficient root.</li>
<li><strong>Path with All Identical <code>nums</code> Values:</strong> E.g., <code>nums = [1, 1, 1]</code>. When the second <code>1</code> is encountered, <code>new_start</code> will correctly shift, making <code>path_len</code> 0 for any path segments containing duplicates.</li>
<li><strong>Path with All Unique <code>nums</code> Values:</strong> <code>new_start</code> will always remain <code>0</code>, and <code>path_len</code> will simply be <code>curr_dist</code>. This is correctly handled.</li>
<li><strong>Graph Type (Tree vs. General Graph):</strong> The <code>if v != p:</code> check is crucial. It ensures that DFS explores paths without immediately returning to the parent, effectively treating the graph as a tree for path exploration. This implies we are looking for simple paths. If cycles were allowed (which is unusual for "longest path" problems unless "longest cycle" is specified), this check would be different.</li>
<li><strong>Negative Edge Weights:</strong> The problem implies "length" and uses <code>w</code> as a distance, suggesting non-negative weights. If negative weights were allowed, the concept of "longest path" with unique nodes could become more complex, especially if negative cycles exist. Assuming <code>w &gt;= 0</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Explicit Problem Definition:</strong> The term "special path" is inferred. An explicit problem statement (e.g., "path with unique node values") would eliminate ambiguity.</li>
<li><strong>Variable Naming:</strong><ul>
<li><code>dist_stack</code>: Could be <code>current_path_cumulative_distances</code> for better clarity.</li>
<li><code>last_seen</code>: Could be <code>value_to_last_depth_index</code>.</li>
<li><code>start_index</code>: Could be <code>valid_window_start_depth_index</code>.</li>
</ul>
</li>
<li><strong>Function Signature / Return Values:</strong><ul>
<li>Instead of modifying a closure variable <code>res</code>, the <code>dfs</code> function could return the <code>[max_len, min_nodes]</code> found in its subtree, and the caller would aggregate these. This makes the function more pure and easier to test.</li>
</ul>
</li>
<li><strong>Iterative DFS:</strong> For extremely deep graphs, an iterative DFS (using an explicit stack) can be used to avoid Python's recursion depth limit entirely, though <code>sys.setrecursionlimit</code> largely mitigates this here.</li>
<li><strong>Global <code>visited</code> set for disconnected graphs:</strong> If the graph can be disconnected and the global longest path is required, an outer loop would iterate through all nodes, calling DFS on unvisited nodes and tracking visited nodes with a set.</li>
<li><strong>Start Node:</strong> For finding the absolute longest path in a general tree, DFS might be run from all nodes, or more specialized algorithms for tree diameter could be adapted. However, given the code structure, a single DFS from node 0 is sufficient for many common problem variations (e.g., "longest path starting from node 0").</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong><code>sys.setrecursionlimit</code>:</strong> While necessary for deep recursion in Python, setting it too high might consume excessive memory on the call stack if the tree is truly a long path. It's a pragmatic solution, but iterative DFS is a more robust alternative for arbitrary depth.</li>
<li><strong>Hash Map Performance (<code>last_seen</code>):</strong> Python's <code>dict</code> is implemented as a hash table. Its <code>O(1)</code> average-case performance relies on good hash functions and collision resolution. In pathological cases (extremely poor hash distribution or malicious inputs), performance could degrade to <code>O(N)</code>, but this is rare with standard types like integers.</li>
</ul>


### Code:
```python
import sys

# Increase recursion depth for deep trees (skewed trees could hit default limits)
sys.setrecursionlimit(10**6)

class Solution:
    def longestSpecialPath(self, edges: list[list[int]], nums: list[int]) -> list[int]:
        n = len(nums)
        
        # Build Adjacency List
        adj = [[] for _ in range(n)]
        for u, v, w in edges:
            adj[u].append((v, w))
            adj[v].append((u, w))
        
        # Result: [max_length, min_nodes]
        # Initialize with 0 length and 1 node (a single node is always a valid path)
        res = [0, 1]
        
        # Stack to store the distance from root to the node at depth i
        # This corresponds to the 'path' stack
        dist_stack = []
        
        # Map to store the last depth index where a value was seen
        # { value: depth_index }
        last_seen = {}
        
        def dfs(u, p, start_index, curr_dist):
            val = nums[u]
            
            # Check if this value has been seen in the current ancestry
            prev_index = last_seen.get(val, -1)
            
            # If the duplicate is valid (within the current window), shrink window
            # The new start must be after the previous occurrence
            new_start = start_index
            if prev_index >= start_index:
                new_start = prev_index + 1
            
            
            path_len = curr_dist
            if new_start < len(dist_stack):
                path_len = curr_dist - dist_stack[new_start]
            else:
                # If new_start equals current depth, path is just the node itself (len 0)
                path_len = 0
                
            num_nodes = len(dist_stack) - new_start + 1
            
            # Update global result
            if path_len > res[0]:
                res[0] = path_len
                res[1] = num_nodes
            elif path_len == res[0]:
                res[1] = min(res[1], num_nodes)
            
            # --- PREPARE RECURSION ---
            dist_stack.append(curr_dist)
            last_seen[val] = len(dist_stack) - 1 # Current depth index
            
            for v, w in adj[u]:
                if v != p:
                    dfs(v, u, new_start, curr_dist + w)
            
            # --- BACKTRACK ---
            dist_stack.pop()
            if prev_index != -1:
                last_seen[val] = prev_index
            else:
                del last_seen[val]

        # Start DFS from root 0
        dfs(0, -1, 0, 0)
        
        return res
```
