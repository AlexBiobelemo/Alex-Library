## Find Closest Node To Given Two Nodes
**Language:** python
**Tags:** python,graph,traversal,distance calculation,functional graph

### Description:
<p>This Python code finds the "closest meeting node" between two starting nodes in a functional graph. A functional graph is a directed graph where each node has an out-degree of at most one (i.e., <code>edges[i]</code> points to at most one next node, or <code>-1</code> for no outgoing edge). The "distance" here is simply the number of steps along a path.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given a functional graph represented by an <code>edges</code> array (where <code>edges[i]</code> is the next node from <code>i</code>), and two starting nodes <code>node1</code> and <code>node2</code>, find a node <code>m</code> such that the maximum of the distances from <code>node1</code> to <code>m</code> and from <code>node2</code> to <code>m</code> is minimized.</li>
<li><strong>Output:</strong> The index of such a meeting node <code>m</code>. If multiple nodes satisfy the minimum maximum distance, return the one with the smallest index. If no common meeting node exists, return -1.</li>
<li><strong>Core Idea:</strong> Calculate the distances from <code>node1</code> to all reachable nodes, and similarly for <code>node2</code>. Then, iterate through all nodes to find one that minimizes the maximum of these two distances.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Initialize Graph Size:</strong> Determine <code>n</code>, the total number of nodes, from the length of the <code>edges</code> array.</li>
<li><strong><code>get_distances</code> Function:</strong><ul>
<li>This helper function takes a <code>start_node</code> as input.</li>
<li>It initializes a <code>distances</code> array of size <code>n</code> with <code>-1</code> (indicating unreachable).</li>
<li>It performs a single path traversal starting from <code>start_node</code>:<ul>
<li>It moves from <code>current_node</code> to <code>edges[current_node]</code>, incrementing the <code>dist</code> count at each step.</li>
<li>It records the distance to each node encountered.</li>
<li>The traversal stops if it hits a dead end (<code>-1</code>) or enters a cycle (by encountering an already visited node whose distance has already been set).</li>
</ul>
</li>
<li>It returns the <code>distances</code> array for the given <code>start_node</code>.</li>
</ul>
</li>
<li><strong>Calculate Distances from Both Start Nodes:</strong><ul>
<li><code>dist1 = get_distances(node1)</code>: Stores distances from <code>node1</code> to all reachable nodes.</li>
<li><code>dist2 = get_distances(node2)</code>: Stores distances from <code>node2</code> to all reachable nodes.</li>
</ul>
</li>
<li><strong>Find Optimal Meeting Node:</strong><ul>
<li>Initialize <code>min_max_dist</code> to infinity and <code>result_node</code> to -1.</li>
<li>Iterate through all possible nodes <code>i</code> from <code>0</code> to <code>n-1</code>:<ul>
<li>Check if node <code>i</code> is reachable from <em>both</em> <code>node1</code> (<code>dist1[i] != -1</code>) and <code>node2</code> (<code>dist2[i] != -1</code>).</li>
<li>If reachable, calculate <code>current_max_dist = max(dist1[i], dist2[i])</code>.</li>
<li>If <code>current_max_dist</code> is less than <code>min_max_dist</code>, update <code>min_max_dist</code> and set <code>result_node = i</code>.</li>
<li>The problem's tie-breaking rule (smallest index) is naturally handled because the loop iterates <code>i</code> in increasing order.</li>
</ul>
</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Functional Graph Traversal:</strong> The <code>get_distances</code> function leverages the specific property of a functional graph (each node has at most one outgoing edge). This allows a simple iterative path traversal instead of a more general Breadth-First Search (BFS) or Depth-First Search (DFS), as there's only one "next" node to explore.</li>
<li><strong>Distance Storage:</strong> Using two separate arrays (<code>dist1</code>, <code>dist2</code>) to store distances from each starting node simplifies the lookup and comparison process.</li>
<li><strong>Iterative Search for Meeting Node:</strong> A straightforward linear scan through all nodes after distance computation is effective. This avoids complex graph traversals from <code>node1</code> and <code>node2</code> simultaneously.</li>
<li><strong>Tie-breaking (Smallest Index):</strong> The <code>for i in range(n)</code> loop inherently ensures that if multiple nodes achieve the same <code>min_max_dist</code>, the one with the lowest index is chosen first and thus stored in <code>result_node</code>.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong> <code>O(N)</code><ul>
<li><code>get_distances</code> function: In the worst case, it traverses a path that visits all <code>N</code> nodes before hitting a cycle or dead end. Each step is <code>O(1)</code>. So, <code>O(N)</code>.</li>
<li>Main logic: Two calls to <code>get_distances</code> (<code>2 * O(N)</code>) and one loop iterating <code>N</code> times (<code>O(N)</code>).</li>
<li>Total: <code>O(N)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> <code>O(N)</code><ul>
<li><code>dist1</code> array: <code>O(N)</code></li>
<li><code>dist2</code> array: <code>O(N)</code></li>
<li>Total: <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>No Common Meeting Node:</strong> If no node <code>i</code> is reachable from both <code>node1</code> and <code>node2</code>, <code>result_node</code> will remain its initial value of -1, which is correct.</li>
<li><strong>Cycles:</strong> The <code>get_distances</code> function correctly handles cycles by stopping when it encounters a node whose distance has already been set (<code>distances[current_node] == -1</code>). This prevents infinite loops and ensures the shortest distance along the specific path traversed.</li>
<li><strong>Dead Ends:</strong> If <code>edges[current_node]</code> is -1, the traversal in <code>get_distances</code> naturally stops, correctly indicating that further nodes are unreachable along that path.</li>
<li><strong><code>node1 == node2</code>:</strong> The logic still works. <code>dist1</code> and <code>dist2</code> will be identical. <code>max(0,0)</code> for the start node itself will correctly yield 0.</li>
<li><strong>Disconnected Components:</strong> Nodes only reachable from <code>node1</code> (or <code>node2</code>) but not the other will have <code>-1</code> in the respective distance array, correctly excluding them from consideration as meeting points.</li>
<li><strong>Tie-breaking:</strong> The iteration <code>for i in range(n)</code> guarantees that if multiple nodes have the same minimal maximum distance, the one with the smallest index is chosen first, satisfying the problem requirement.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong><ul>
<li>The <code>get_distances</code> function is well-commented and its purpose is clear.</li>
<li>The explicit comment regarding tie-breaking is helpful.</li>
</ul>
</li>
<li><strong>Performance:</strong> The current <code>O(N)</code> time and space complexity is optimal for this problem, as all nodes/edges may need to be visited in the worst case to determine reachability and distances. No significant performance improvements are likely without changing the problem constraints.</li>
<li><strong>Robustness:</strong><ul>
<li>Input validation could be added (e.g., checking if <code>node1</code> and <code>node2</code> are within <code>0</code> and <code>n-1</code>, or if <code>edges</code> contains valid indices). In competitive programming, inputs are usually assumed valid.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no inherent security vulnerabilities in this code. It performs numerical computations and array manipulations without external input or complex data parsing that might expose typical security flaws.</li>
<li><strong>Performance:</strong> The solution is highly efficient (<code>O(N)</code>). For typical graph sizes (up to 10^5 nodes), this approach will execute very quickly. Memory usage is also proportional to the number of nodes, which is efficient.</li>
</ul>


### Code:
```python
class Solution(object):
    def closestMeetingNode(self, edges, node1, node2):
        """
        :type edges: List[int]
        :type node1: int
        :type node2: int
        :rtype: int
        """
        n = len(edges)

        def get_distances(start_node):
            """
            Performs a traversal starting from start_node and returns an array
            where distances[i] is the distance from start_node to node i.
            -1 indicates that node i is not reachable from start_node.
            """
            distances = [-1] * n
            current_node = start_node
            dist = 0
            while current_node != -1 and distances[current_node] == -1:
                distances[current_node] = dist
                current_node = edges[current_node]
                dist += 1
            return distances

        # Calculate distances from node1 to all reachable nodes
        dist1 = get_distances(node1)
        # Calculate distances from node2 to all reachable nodes
        dist2 = get_distances(node2)

        min_max_dist = float('inf')
        result_node = -1

        # Iterate through all nodes to find the best meeting node
        for i in range(n):
            # Check if node i is reachable from both node1 and node2
            if dist1[i] != -1 and dist2[i] != -1:
                current_max_dist = max(dist1[i], dist2[i])

                # If we found a new minimum maximum distance
                if current_max_dist < min_max_dist:
                    min_max_dist = current_max_dist
                    result_node = i
               

        return result_node
```
