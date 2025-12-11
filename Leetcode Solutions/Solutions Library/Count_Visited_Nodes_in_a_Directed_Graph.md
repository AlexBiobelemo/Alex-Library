## Count Visited Nodes in a Directed Graph
**Language:** python
**Tags:** python,oop,dfs,cycle detection,hash map

### Description:
<p>This code addresses a common problem in graph theory: analyzing functional graphs, where each node has exactly one outgoing edge. The goal is to determine, for each node, the total number of distinct nodes visited starting from it until a cycle is completed.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>countVisitedNodes</code> function aims to calculate, for every node in a given functional graph, the total length of the path starting from that node, including the length of the cycle it eventually enters.</p>
<ul>
<li><strong>Input:</strong> A list <code>edges</code> where <code>edges[i]</code> represents the next node reachable from node <code>i</code>. This implies a directed graph where each node has an out-degree of exactly one.</li>
<li><strong>Output:</strong> A list <code>answer</code> of the same length as <code>edges</code>, where <code>answer[i]</code> is the number of nodes visited when traversing from node <code>i</code> until the cycle is fully traversed.</li>
<li><strong>Core Idea:</strong> Functional graphs decompose into components, each consisting of a cycle with directed trees (or "tails") leading into it. The algorithm performs a depth-first search (DFS)-like traversal for each unvisited node to identify these components, detect cycles, and propagate path lengths.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm iterates through each node, initiating a traversal if the node hasn't been fully processed yet. It uses distinct states to manage the DFS-like process:</p>
<ol>
<li><p><strong>Initialization:</strong></p>
<ul>
<li><code>n</code> is the number of nodes.</li>
<li><code>answer</code> is initialized with <code>-1</code> for all nodes.<ul>
<li><code>-1</code>: Node has not been visited or processed yet.</li>
<li><code>-2</code>: Node is currently in the active DFS path (i.e., being visited). This helps detect cycles.</li>
<li><code>&gt;0</code>: The final computed count of visited nodes for this node.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Main Loop (<code>for i in range(n)</code>):</strong></p>
<ul>
<li>Ensures all nodes are considered as starting points. If <code>answer[i]</code> is already <code>&gt;0</code>, it means <code>i</code> has been part of a previously computed path/cycle, so it's skipped.</li>
<li>If <code>answer[i]</code> is <code>-1</code>, a new traversal begins.</li>
</ul>
</li>
<li><p><strong>DFS-like Traversal (inner <code>while</code> loop):</strong></p>
<ul>
<li><code>current_path_nodes</code>: A list to store the sequence of nodes encountered in the current path.</li>
<li><code>path_map</code>: A dictionary mapping node IDs to their index in <code>current_path_nodes</code>. This allows <code>O(1)</code> lookup to find the start of a cycle.</li>
<li>The traversal starts from <code>i</code> and follows <code>edges[node]</code> repeatedly.</li>
<li>Each node encountered is marked <code>answer[node] = -2</code>, added to <code>current_path_nodes</code>, and its index is recorded in <code>path_map</code>.</li>
<li>This continues until <code>node</code> points to a node whose <code>answer</code> value is not <code>-1</code>.</li>
</ul>
</li>
<li><p><strong>Handling Traversal Termination:</strong></p>
<ul>
<li><p><strong>Case 1: <code>answer[node] == -2</code> (Cycle Detected):</strong></p>
<ul>
<li>The current <code>node</code> is part of the <code>current_path_nodes</code>, meaning a cycle has been found.</li>
<li><code>cycle_start_index = path_map[node]</code> identifies where the cycle begins within <code>current_path_nodes</code>.</li>
<li><code>cycle_length = path_index - cycle_start_index</code>.</li>
<li>All nodes <em>within the cycle</em> (<code>current_path_nodes[cycle_start_index:]</code>) are assigned <code>cycle_length</code> as their answer.</li>
<li>Nodes <em>before</em> the cycle in <code>current_path_nodes</code> (the "tail" leading into the cycle) have their answers computed: <code>distance_to_cycle_start + cycle_length</code>. This is done by iterating backward from <code>cycle_start_index - 1</code>.</li>
</ul>
</li>
<li><p><strong>Case 2: <code>answer[node] &gt; 0</code> (Path Leads to an Already Computed Node):</strong></p>
<ul>
<li>The current <code>node</code> points to a node whose path length (<code>known_length = answer[node]</code>) has already been determined (either as part of another cycle or another tail).</li>
<li>Nodes in <code>current_path_nodes</code> (the "tail" leading to this <code>known_node</code>) have their answers computed: <code>distance_to_known_node + known_length</code>. This is done by iterating backward from <code>path_index - 1</code>.</li>
</ul>
</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>State Tracking Array (<code>answer</code>):</strong> Using an array to store three distinct states (<code>-1</code> for unvisited, <code>-2</code> for visiting, <code>&gt;0</code> for computed) is crucial for efficiency. It acts as a memoization table and allows for effective cycle detection and path length propagation.</li>
<li><strong>DFS-like Traversal:</strong> This approach naturally explores paths in a functional graph until a cycle or an already resolved path is found.</li>
<li><strong><code>current_path_nodes</code> (List) and <code>path_map</code> (Dictionary):</strong><ul>
<li><code>current_path_nodes</code> stores the actual path traversed, enabling easy backward iteration to assign results after a cycle or resolved path is found.</li>
<li><code>path_map</code> provides <code>O(1)</code> average-case lookup to determine if a node encountered is already in the <em>current</em> path, which is essential for quickly identifying the start of a cycle.</li>
</ul>
</li>
<li><strong>Iterative Approach:</strong> The iterative (non-recursive) DFS avoids potential recursion depth limits for very long paths.</li>
<li><strong>Processing Order:</strong> The loops are structured to ensure that once a cycle or path is fully resolved, its constituent nodes' <code>answer</code> values are correctly set, allowing subsequent traversals that encounter these nodes to leverage the pre-computed values.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: O(N)</strong></p>
<ul>
<li>Each node <code>i</code> is visited by the outer <code>for</code> loop at most once as a starting point for a DFS-like traversal.</li>
<li>During any traversal, a node is added to <code>current_path_nodes</code> and <code>path_map</code> at most once.</li>
<li>The <code>answer[node] = -2</code> assignment happens at most once per node.</li>
<li>The final <code>answer[node] = &gt;0</code> assignment happens at most once per node.</li>
<li>The two inner <code>for</code> loops (for cycle propagation and tail propagation) iterate over nodes that were part of <code>current_path_nodes</code> for that specific traversal. Each node globally participates in such propagation at most once.</li>
<li>Therefore, each node and each edge is processed a constant number of times in total.</li>
</ul>
</li>
<li><p><strong>Space Complexity: O(N)</strong></p>
<ul>
<li><code>answer</code>: <code>O(N)</code> for storing the results.</li>
<li><code>current_path_nodes</code>: In the worst case (e.g., a single long path or cycle involving all nodes), this list can store up to <code>N</code> nodes. <code>O(N)</code>.</li>
<li><code>path_map</code>: Similarly, in the worst case, this dictionary can store up to <code>N</code> entries. <code>O(N)</code>.</li>
<li>Total space complexity is dominated by these data structures.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The algorithm correctly handles various edge cases due to its robust state management and propagation logic:</p>
<ul>
<li><strong>Single Node Graph (<code>edges = [0]</code>):</strong><ul>
<li>Node 0 starts. <code>answer[0]</code> becomes <code>-2</code>. <code>current_path_nodes = [0]</code>, <code>path_map = {0:0}</code>. <code>node</code> becomes <code>edges[0]=0</code>.</li>
<li><code>answer[node]</code> (which is <code>answer[0]</code>) is <code>-2</code>. Cycle detected.</li>
<li><code>cycle_start_index = path_map[0] = 0</code>. <code>cycle_length = 1 - 0 = 1</code>.</li>
<li><code>answer[0]</code> is set to <code>1</code>. Correct.</li>
</ul>
</li>
<li><strong>Disconnected Components:</strong> The outer <code>for i in range(n)</code> loop ensures that even if the graph has multiple disconnected functional components, each one will eventually be processed starting from one of its nodes.</li>
<li><strong>Self-Loops:</strong> A node <code>i</code> with <code>edges[i] = i</code> is treated as a cycle of length 1, which is handled by the <code>answer[node] == -2</code> case as described above.</li>
<li><strong>Long Chains Leading to a Cycle:</strong> Paths like <code>0 -&gt; 1 -&gt; 2 -&gt; ... -&gt; k -&gt; cycle_start_node</code> are correctly handled. The cycle is identified first, then lengths are propagated backward along the tail.</li>
<li><strong>Path Leading to an Already Computed Component:</strong> If a path leads to a node <code>X</code> whose <code>answer[X]</code> is already <code>&gt;0</code> (because <code>X</code> was part of a previously processed component), the current path's lengths are correctly calculated by adding their distance to <code>X</code> to <code>answer[X]</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is quite readable. Variable names are descriptive, and the comments clearly explain the different states of <code>answer</code> and the two main termination cases. The use of <code>path_map</code> and <code>current_path_nodes</code> is clear.</li>
<li><strong>No Major Performance Improvements:</strong> Given the optimal <code>O(N)</code> time and space complexity, there are no significant performance gains to be made with this approach.</li>
<li><strong>Alternative Approaches:</strong><ul>
<li><strong>Topological Sort (for DAGs with specific properties):</strong> Not directly applicable here since functional graphs <em>always</em> contain cycles.</li>
<li><strong>Tarjan's/Kosaraju's Algorithm (for general SCCs):</strong> While these can find strongly connected components, the specific structure of functional graphs (cycles with in-trees) allows for this more specialized and perhaps simpler DFS-based solution to directly compute path lengths. For this particular problem, the provided solution is more direct and efficient than a general SCC algorithm.</li>
<li><strong>Floyd's Cycle-Finding Algorithm (Tortoise and Hare):</strong> Could be used to detect the cycle and find its length. However, to propagate values back to all nodes in the <em>tail</em> leading to the cycle, we would still need to store the path or re-traverse it, making the current approach with <code>current_path_nodes</code> and <code>path_map</code> quite effective for the specific requirements of this problem.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code is purely algorithmic and operates on in-memory data structures. It does not interact with external systems, user input in a way that could lead to injection, file systems, or networks. Thus, there are no immediate security concerns.</li>
<li><strong>Performance:</strong> As noted, the solution achieves optimal <code>O(N)</code> time and space complexity. It's efficient for large inputs. The use of a dictionary (<code>path_map</code>) for <code>O(1)</code> average-case lookups (for cycle detection) is critical for maintaining this performance. In worst-case hash collisions for <code>path_map</code>, it could degrade to <code>O(N)</code> per lookup, making the overall worst-case <code>O(N^2)</code>, but typical Python dictionary implementations make this rare in practice.</li>
</ul>


### Code:
```python
```python
from typing import List

class Solution:
    def countVisitedNodes(self, edges: List[int]) -> List[int]:
        n = len(edges)
        answer = [-1] * n  # -1: unvisited, -2: currently visiting (in current DFS path), >0: computed length

        for i in range(n):
            if answer[i] == -1:
                current_path_nodes = []  # Stores nodes in the current path in order
                path_map = {}            # Stores node -> index in current_path_nodes for O(1) lookup
                
                node = i
                path_index = 0

                # Traverse until we hit an already visited node (either in current path or fully computed)
                while answer[node] == -1:
                    answer[node] = -2  # Mark as currently visiting
                    current_path_nodes.append(node)
                    path_map[node] = path_index
                    
                    node = edges[node]
                    path_index += 1
                
                # At this point, 'node' is either:
                # 1. A node whose 'answer' is already computed (answer[node] > 0).
                # 2. A node that is part of the current path (answer[node] == -2), meaning we found a cycle.

                if answer[node] == -2:  # Case 2: Found a cycle
                    cycle_start_index = path_map[node]
                    cycle_length = path_index - cycle_start_index

                    # All nodes in the cycle have 'answer = cycle_length'
                    for j in range(cycle_start_index, path_index):
                        answer[current_path_nodes[j]] = cycle_length
                    
                    # Nodes before the cycle in the current path (tail leading to the cycle)
                    # Their length is their distance to the cycle start + cycle_length.
                    for j in range(cycle_start_index - 1, -1, -1):
                        answer[current_path_nodes[j]] = (cycle_start_index - j) + cycle_length
                
                else:  # Case 1: 'node' points to an already computed path (answer[node] > 0)
                    known_length = answer[node]
                    
                    # Nodes in the current path (tail leading to the already computed node)
                    # Their length is their distance to 'node' + 'known_length'.
                    for j in range(path_index - 1, -1, -1):
                        answer[current_path_nodes[j]] = (path_index - j) + known_length
        
        return answer
```
