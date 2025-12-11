## Merge K Sorted List
**Language:** python
**Tags:** min-heap,linked list,merge,sorting

### Description:
<p>This code effectively solves the classic problem of merging <em>k</em> sorted linked lists into a single sorted linked list using a min-priority queue (min-heap).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to take a list of <code>k</code> individual sorted linked lists and combine them into one single sorted linked list. It aims to do this efficiently by always picking the smallest available node across all current list heads.</p>
<h3>2. How It Works</h3>
<ol>
<li><p><strong>Initialization</strong>:</p>
<ul>
<li>A <code>min_heap</code> (priority queue) is created to store nodes.</li>
<li>A <code>dummy_head</code> node (with an arbitrary value like 0) is created. This simplifies the logic for appending the first node to the merged list, avoiding special casing. <code>current</code> points to <code>dummy_head</code>.</li>
</ul>
</li>
<li><p><strong>Initial Heap Population</strong>:</p>
<ul>
<li>The code iterates through each of the <code>k</code> input linked lists.</li>
<li>For every non-empty list, its head node is pushed onto the <code>min_heap</code>.</li>
<li>Each element pushed to the heap is a tuple: <code>(node.val, list_index, node_object)</code>.<ul>
<li><code>node.val</code> is the primary sorting key for the heap.</li>
<li><code>list_index</code> serves as a tie-breaker. If two nodes have the same <code>val</code>, Python's <code>heapq</code> would then try to compare <code>ListNode_object</code> directly, which isn't generally supported or meaningful. The <code>list_index</code> prevents this comparison error.</li>
<li><code>node_object</code> is the actual <code>ListNode</code> so its <code>next</code> pointer can be accessed later.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Merging Loop</strong>:</p>
<ul>
<li>The code enters a <code>while</code> loop that continues as long as the <code>min_heap</code> is not empty.</li>
<li>In each iteration:<ul>
<li>The smallest node (based on <code>val</code>) is extracted from the <code>min_heap</code> using <code>heapq.heappop()</code>.</li>
<li>This extracted <code>node</code> is appended to the <code>current</code> position of the merged list (<code>current.next = node</code>).</li>
<li><code>current</code> is then advanced to this newly added node (<code>current = current.next</code>).</li>
<li>If the extracted <code>node</code> has a <code>node.next</code> (meaning it's not the last node of its original list), that <code>node.next</code> is pushed onto the <code>min_heap</code>. This ensures that the "next" smallest element from that particular list becomes available for comparison. The <code>list_index</code> from the original tuple is reused for this new node.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Result</strong>:</p>
<ul>
<li>Once the <code>min_heap</code> is empty, all nodes have been processed.</li>
<li>The merged sorted linked list starts from <code>dummy_head.next</code> (because <code>dummy_head</code> itself was just a placeholder).</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Min-Heap (Priority Queue)</strong>: This is the core data structure choice. It efficiently keeps track of the smallest available node from the <em>k</em> current list heads, allowing for <code>O(log k)</code> retrieval and insertion. Without it, finding the minimum would require iterating through <code>k</code> elements in each step, leading to worse performance.</li>
<li><strong>Tuple in Heap <code>(value, list_index, ListNode_object)</code></strong>:<ul>
<li><code>value</code>: Essential for the heap's primary sorting.</li>
<li><code>list_index</code>: Crucial for Python's <code>heapq</code>. If two nodes have identical <code>value</code>s, <code>heapq</code> would proceed to compare the next element in the tuple. Without <code>list_index</code>, it would attempt to compare two <code>ListNode</code> objects directly, which would raise a <code>TypeError</code> because <code>ListNode</code> objects are not inherently comparable. The <code>list_index</code> provides a stable, comparable tie-breaker.</li>
<li><code>ListNode_object</code>: Needed to connect to the merged list and to retrieve the <code>next</code> node from the original list.</li>
</ul>
</li>
<li><strong>Dummy Head Node</strong>: Simplifies the linked list construction logic. Instead of needing to handle the very first node of the merged list as a special case, all appends can be done uniformly via <code>current.next</code>.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>k</code> be the number of linked lists, and <code>N</code> be the total number of nodes across all <code>k</code> lists.</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><strong>Initial Heap Population</strong>: <code>k</code> <code>heappush</code> operations. Each <code>heappush</code> takes <code>O(log k)</code> time. Total: <code>O(k log k)</code>.</li>
<li><strong>Merging Loop</strong>: Each of the <code>N</code> nodes is extracted from the heap exactly once, and its successor (if it exists) is pushed onto the heap exactly once.<ul>
<li>Each <code>heappop</code> takes <code>O(log k)</code> time.</li>
<li>Each <code>heappush</code> takes <code>O(log k)</code> time.</li>
<li>Total <code>N</code> iterations, each taking <code>O(log k)</code>. Total: <code>O(N log k)</code>.</li>
</ul>
</li>
<li><strong>Overall Time Complexity</strong>: <code>O(k log k + N log k)</code>. Since <code>N</code> is typically much larger than <code>k</code> (or at least <code>N &gt;= k</code>), this simplifies to <strong><code>O(N log k)</code></strong>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><strong><code>min_heap</code></strong>: At any point, the heap stores at most one node from each of the <code>k</code> linked lists (specifically, the current head of each list). Therefore, the heap can hold at most <code>k</code> elements.</li>
<li><strong><code>dummy_head</code>, <code>current</code></strong>: <code>O(1)</code> additional space.</li>
<li><strong>Overall Space Complexity</strong>: <strong><code>O(k)</code></strong>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>lists</code> input</strong>: If <code>lists</code> is an empty list, the initial <code>for</code> loop won't execute, <code>min_heap</code> remains empty, and <code>dummy_head.next</code> (which is <code>None</code>) is correctly returned.</li>
<li><strong><code>lists</code> contains empty lists</strong>: The <code>if head:</code> check correctly skips any empty input lists during initial heap population.</li>
<li><strong><code>lists</code> contains only one non-empty list</strong>: <code>k=1</code>. The heap operations become <code>O(log 1)</code>, essentially <code>O(1)</code>. The single list is correctly copied.</li>
<li><strong>All nodes have the same value</strong>: The <code>list_index</code> in the heap tuple ensures that nodes with identical values are handled correctly without <code>TypeError</code> (as <code>ListNode</code> objects aren't directly comparable) and provides a stable sorting order.</li>
<li><strong>Very long or very short lists</strong>: The algorithm's logic naturally handles lists of varying lengths, as nodes are processed one by one until all lists are exhausted.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Readability</strong>: The code is already quite readable, with good variable names and comments explaining the heap tuple's components.</p>
</li>
<li><p><strong>Performance (Alternative Approach - Divide and Conquer)</strong>:</p>
<ul>
<li>Another common approach is to merge two lists at a time. For example, merge <code>lists[0]</code> and <code>lists[1]</code> into <code>L1</code>, then merge <code>lists[2]</code> and <code>lists[3]</code> into <code>L2</code>, and so on. Then merge <code>L1</code> and <code>L2</code>, etc., until only one list remains. This is a divide-and-conquer strategy.</li>
<li>This approach also achieves <strong><code>O(N log k)</code> time complexity</strong> and <code>O(1)</code> auxiliary space (if done in-place, modifying existing <code>ListNode.next</code> pointers) or <code>O(N)</code> if creating new nodes. The heap approach often has better constant factors for <code>k</code> lists.</li>
</ul>
</li>
<li><p><strong>Robustness</strong>: The current solution is robust for the specified problem constraints.</p>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The chosen min-heap approach is a highly efficient and generally optimal algorithm for this problem, achieving the best possible time complexity of <code>O(N log k)</code>. Python's <code>heapq</code> module is implemented in C, providing good performance for heap operations.</li>
<li><strong>Security</strong>: This algorithm is a pure data structure manipulation problem and doesn't inherently involve external input, network communication, or sensitive data handling, so there are no direct security implications.</li>
</ul>


### Code:
```python
import heapq

class Solution(object):
    def mergeKLists(self, lists):
        """
        :type lists: List[Optional[ListNode]]
        :rtype: Optional[ListNode]
        """
        min_heap = []
        
        # Push the head of each non-empty list into the min-heap
        # The heap stores (value, list_index, ListNode_object)
        # list_index is used as a tie-breaker for nodes with the same value,
        # ensuring that ListNode objects are not directly compared by heapq.
        for i, head in enumerate(lists):
            if head:
                heapq.heappush(min_heap, (head.val, i, head))
        
        # Create a dummy head for the merged list to simplify appending
        dummy_head = ListNode(0) 
        current = dummy_head
        
        while min_heap:
            # Pop the smallest node from the heap
            val, list_index, node = heapq.heappop(min_heap)
            
            # Append this node to the merged list
            current.next = node
            current = current.next
            
            # If the popped node has a next node, push it to the heap
            # Use the same list_index for the next node from the same list
            if node.next:
                heapq.heappush(min_heap, (node.next.val, list_index, node.next))
                
        return dummy_head.next
```
