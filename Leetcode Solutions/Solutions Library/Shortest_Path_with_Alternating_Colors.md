## Shortest Path with Alternating Colors
**Language:** python
**Tags:** python,graph,bfs,shortest path,alternating path

### Description:
<p>This code finds the shortest path from node 0 to all other nodes in a directed graph, with the constraint that the colors of the edges in the path must alternate (red, blue, red, blue, or blue, red, blue, red, etc.).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This problem asks for the shortest alternating-colored paths from a source node (node 0) to all other nodes in a given directed graph. "Alternating" means that if an edge from <code>u</code> to <code>v</code> is red, the next edge from <code>v</code> to <code>w</code> must be blue, and vice-versa. The output should be a list where <code>answer[i]</code> is the length of the shortest alternating path to node <code>i</code>, or -1 if no such path exists.</p>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a Breadth-First Search (BFS) algorithm, augmented to handle the alternating color constraint.</p>
<ul>
<li><strong>Graph Representation</strong>: An adjacency list <code>adj</code> is built. <code>adj[i][0]</code> stores a list of neighbors reachable from node <code>i</code> via a red edge, and <code>adj[i][1]</code> stores neighbors reachable via a blue edge.</li>
<li><strong>Distance Tracking</strong>: A 2D array <code>dist[node][color]</code> keeps track of the shortest path length to <code>node</code> where the <em>last edge taken to reach <code>node</code></em> was of <code>color</code> (0 for red, 1 for blue). All distances are initialized to infinity.</li>
<li><strong>BFS Initialization</strong>:<ul>
<li>Node 0 is the starting point. It can be reached in 0 steps. Conceptually, a path of length 0 to node 0 can be considered as having "ended" with either a red or a blue edge (as there was no preceding edge).</li>
<li>So, <code>dist[0][0]</code> and <code>dist[0][1]</code> are both set to 0.</li>
<li>The BFS queue is initialized with <code>(0, 0, 0)</code> and <code>(0, 1, 0)</code>. These tuples represent <code>(current_node, color_of_last_edge_to_reach_node, path_length)</code>.</li>
</ul>
</li>
<li><strong>BFS Traversal</strong>:<ul>
<li>The algorithm dequeues a <code>(curr_node, prev_edge_color, path_len)</code>.</li>
<li>To maintain the alternating path, the <em>next</em> edge must be of the opposite color (<code>color_to_take_next = 1 - prev_edge_color</code>).</li>
<li>It then iterates through all <code>neighbor</code> nodes reachable from <code>curr_node</code> using an edge of <code>color_to_take_next</code>.</li>
<li>If <code>path_len + 1</code> (the new path length to <code>neighbor</code>) is shorter than the currently recorded <code>dist[neighbor][color_to_take_next]</code>, the distance is updated, and the <code>(neighbor, color_to_take_next, path_len + 1)</code> tuple is enqueued.</li>
</ul>
</li>
<li><strong>Result Compilation</strong>: After the BFS completes, <code>answer[i]</code> for each node <code>i</code> is the minimum of <code>dist[i][0]</code> and <code>dist[i][1]</code>. If this minimum is still <code>float('inf')</code>, it means the node is unreachable, so <code>answer[i]</code> is set to -1.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Adjacency List with Color Segmentation</strong>: The use of <code>adj[node][0]</code> for red edges and <code>adj[node][1]</code> for blue edges is an efficient way to represent a graph with colored edges. It allows direct lookup of neighbors by a specific color, which is crucial for the alternating path logic.</li>
<li><strong>BFS for Shortest Paths</strong>: BFS is the standard and optimal algorithm for finding shortest paths in unweighted graphs (where each edge has a "weight" of 1), which applies here.</li>
<li><strong>State Representation in BFS (<code>dist</code> array and Queue elements)</strong>:<ul>
<li>The core design decision is to make the BFS state dependent not just on the <code>node</code> and <code>path_length</code>, but also on the <code>color_of_the_last_edge_taken_to_reach_that_node</code>. This is achieved by the <code>dist[node][color]</code> array and the <code>(node, color, path_length)</code> tuples in the queue. This state tracking is fundamental for enforcing the alternating color constraint.</li>
<li><code>dist[node][color]</code> effectively stores the shortest path to <code>node</code> <em>ending with an edge of <code>color</code></em>.</li>
</ul>
</li>
<li><strong>Initialization of Node 0</strong>: Starting the queue with <code>(0, 0, 0)</code> and <code>(0, 1, 0)</code> correctly handles the initial state. It allows paths to begin from node 0 using either a red or a blue edge as the very first step.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(N + E)<ul>
<li>Building the adjacency list takes O(E_red + E_blue) = O(E) time, where E is the total number of edges.</li>
<li>The BFS processes each unique <code>(node, last_edge_color)</code> state at most once. There are <code>N</code> nodes and 2 possible last edge colors, leading to <code>2N</code> states.</li>
<li>For each state, it iterates through outgoing edges. Each edge <code>(u,v)</code> will be considered at most twice: once when <code>prev_edge_color</code> for <code>u</code> is 0 (red) and we look for blue edges to <code>v</code>, and once when <code>prev_edge_color</code> for <code>u</code> is 1 (blue) and we look for red edges to <code>v</code>.</li>
<li>Therefore, the BFS traversal is O(N + E).</li>
<li>The final step of compiling the answer array takes O(N).</li>
<li><strong>Overall time complexity is dominated by O(N + E)</strong>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(N + E)<ul>
<li>The <code>adj</code> adjacency list requires O(N + E) space.</li>
<li>The <code>dist</code> array requires O(N * 2) = O(N) space.</li>
<li>The BFS queue <code>q</code> in the worst case can hold up to <code>2N</code> elements (if all nodes are directly reachable in an alternating fashion). This requires O(N) space.</li>
<li><strong>Overall space complexity is O(N + E)</strong>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Unreachable Nodes</strong>: If a node <code>i</code> cannot be reached by any alternating path from node 0, <code>dist[i][0]</code> and <code>dist[i][1]</code> will remain <code>float('inf')</code>. The code correctly identifies this and sets <code>answer[i]</code> to -1.</li>
<li><strong>Disconnected Graph</strong>: The BFS will only explore the connected component reachable from node 0. Other nodes will correctly remain unreachable (<code>-1</code> in the answer).</li>
<li><strong>No Red/Blue Edges</strong>: If either <code>redEdges</code> or <code>blueEdges</code> lists are empty, the corresponding parts of the adjacency list will be empty. The BFS will correctly not traverse non-existent edges.</li>
<li><strong>Path to Node 0</strong>: The shortest path to node 0 itself is 0, which is correctly initialized and returned in <code>answer[0]</code>.</li>
<li><strong>Self-loops and Parallel Edges</strong>: Standard BFS behavior handles these correctly. Self-loops are treated like any other edge. Multiple edges of the same color between two nodes don't change the shortest path logic.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability with Named Constants</strong>: Using named constants for edge colors (e.g., <code>RED = 0</code>, <code>BLUE = 1</code>) would make the code more readable and self-documenting than using magic numbers <code>0</code> and <code>1</code>.<pre><code class="language-python">RED = 0
BLUE = 1
# ...
adj = [[[], []] for _ in range(n)]
# ...
for a, b in redEdges:
    adj[a][RED].append(b)
# ...
color_to_take_next = 1 - prev_edge_color # This would become BLUE if prev was RED, etc.
</code></pre>
</li>
<li><strong>Type Hinting Clarity</strong>: While <code>List[List[int]]</code> is given, for complex graph structures, custom type aliases could clarify <code>(node, color, length)</code> tuples.</li>
<li><strong>Potential Optimization (Minor)</strong>: If memory is a critical constraint for extremely large <code>N</code>, and the graph is sparse, an alternative <code>dist</code> structure could be a dictionary mapping <code>(node, color)</code> tuples to distances, though this usually adds a constant factor overhead. For the typical constraints of this problem type, the <code>list</code> of <code>list</code> approach for <code>dist</code> is perfectly fine and often faster due to direct indexing.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The chosen BFS algorithm provides optimal performance for this problem's constraints. Using <code>collections.deque</code> ensures O(1) time complexity for queue operations (append, popleft), which is crucial for efficient BFS. No obvious performance bottlenecks exist.</li>
<li><strong>Security</strong>: The code is purely algorithmic and does not involve external input, network communication, file I/O, or sensitive data handling. Therefore, there are no specific security concerns.</li>
</ul>


### Code:
```python
import collections

class Solution:
    def shortestAlternatingPaths(self, n: int, redEdges: List[List[int]], blueEdges: List[List[int]]) -> List[int]:
        adj = [[[], []] for _ in range(n)]
        for a, b in redEdges:
            adj[a][0].append(b)
        for u, v in blueEdges:
            adj[u][1].append(v)

        dist = [[float('inf')] * 2 for _ in range(n)]

        q = collections.deque()

        dist[0][0] = 0
        dist[0][1] = 0

        q.append((0, 0, 0)) # (node, color_of_last_edge_taken_to_reach_node, current_path_length)
        q.append((0, 1, 0)) # (node, color_of_last_edge_taken_to_reach_node, current_path_length)

        while q:
            curr_node, prev_edge_color, path_len = q.popleft()

            color_to_take_next = 1 - prev_edge_color

            for neighbor in adj[curr_node][color_to_take_next]:
                if path_len + 1 < dist[neighbor][color_to_take_next]:
                    dist[neighbor][color_to_take_next] = path_len + 1
                    q.append((neighbor, color_to_take_next, path_len + 1))

        answer = [-1] * n
        for i in range(n):
            min_dist = min(dist[i][0], dist[i][1])
            if min_dist != float('inf'):
                answer[i] = min_dist

        return answer
```
