## Rotate List
**Language:** python
**Tags:** linked list,list rotation,pointer manipulation,data structure

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements the <code>rotateRight</code> function for a singly linked list. Its primary purpose is to reorder the nodes of a given linked list such that it is "rotated" to the right by <code>k</code> places. This means the last <code>k</code> nodes become the first <code>k</code> nodes, and the original first <code>length - k</code> nodes follow them. The rotation must happen in-place without creating new nodes, only by modifying pointers.</p>
<h3>2. How It Works</h3>
<p>The algorithm proceeds through several distinct phases:</p>
<ul>
<li><strong>Initial Checks</strong>: It first handles edge cases:<ul>
<li>If the list is empty (<code>head is None</code>).</li>
<li>If the list has only one node (<code>head.next is None</code>).</li>
<li>If <code>k</code> is 0 (no rotation needed).
In these cases, the original <code>head</code> is returned directly.</li>
</ul>
</li>
<li><strong>Determine Length and Tail</strong>: It traverses the entire list to:<ul>
<li>Calculate the total <code>length</code> of the linked list.</li>
<li>Find the <code>tail</code> node (the last node in the original list).</li>
</ul>
</li>
<li><strong>Form a Circular List</strong>: The <code>tail</code> node's <code>next</code> pointer is connected back to the original <code>head</code>, effectively transforming the linear list into a circular one.</li>
<li><strong>Calculate Effective Rotations</strong>: Since <code>k</code> can be greater than the list's <code>length</code>, the actual number of rotations needed is <code>k % length</code>. This <code>effective_k</code> ensures we don't perform redundant full rotations.</li>
<li><strong>Find New Tail and Head</strong>:<ul>
<li>The goal is to find the node that will become the <em>new tail</em> after rotation. This node is <code>(length - effective_k - 1)</code> steps away from the original <code>head</code>.</li>
<li>It traverses the circular list for <code>steps_to_new_tail</code> times to locate this <code>new_tail</code> node.</li>
<li>The node immediately after <code>new_tail</code> (which is <code>new_tail.next</code>) becomes the <code>new_head</code>.</li>
</ul>
</li>
<li><strong>Break the Circle</strong>: The <code>new_tail</code>'s <code>next</code> pointer is set to <code>None</code>, breaking the circular list at the correct point and forming the rotated linear list.</li>
<li><strong>Return New Head</strong>: The <code>new_head</code> of the rotated list is returned.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>In-Place Modification</strong>: The solution modifies existing node pointers rather than creating new nodes, which is efficient in terms of space complexity.</li>
<li><strong>Circular List Transformation</strong>: A clever approach is used by temporarily converting the list into a circular structure. This simplifies finding the "new" start and end points after rotation, as one can simply traverse <code>N - k</code> steps from the original head to find the new head.</li>
<li><strong>Modulo Operation for <code>k</code></strong>: Using <code>k % length</code> handles cases where <code>k</code> is larger than the list's length, preventing unnecessary full rotations and ensuring <code>effective_k</code> is always within <code>[0, length - 1]</code>.</li>
<li><strong>Identifying Split Point</strong>: The calculation <code>length - effective_k - 1</code> to find the new tail is key. It directly identifies the node <em>before</em> the new starting point of the list.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(N)<ul>
<li>Finding <code>length</code> and <code>tail</code>: O(N) (one full pass).</li>
<li>Connecting <code>tail</code> to <code>head</code>: O(1).</li>
<li>Calculating <code>effective_k</code>: O(1).</li>
<li>Finding <code>new_tail</code> and <code>new_head</code>: O(N) in the worst case (another pass up to <code>length - effective_k - 1</code> steps).</li>
<li>Breaking the circle: O(1).</li>
<li>Overall, the dominant factor is the traversals, making it O(N) where N is the number of nodes in the linked list.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(1)<ul>
<li>The algorithm uses a constant amount of extra space for pointers (<code>current</code>, <code>tail</code>, <code>new_tail</code>, <code>new_head</code>) and integer variables (<code>length</code>, <code>effective_k</code>, <code>steps_to_new_tail</code>), regardless of the list size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code correctly handles several critical edge cases:</p>
<ul>
<li><strong>Empty list (<code>head is None</code>)</strong>: Handled by the first <code>if</code> condition. Returns <code>None</code>.</li>
<li><strong>Single node list (<code>head.next is None</code>)</strong>: Handled by the first <code>if</code> condition. Returns the original <code>head</code>.</li>
<li><strong><code>k = 0</code></strong>: Handled by the first <code>if</code> condition. Returns the original <code>head</code>.</li>
<li><strong><code>k</code> is a multiple of <code>length</code> (e.g., <code>k = length</code>, <code>k = 2*length</code>)</strong>:<ul>
<li><code>effective_k</code> becomes <code>0</code>.</li>
<li><code>steps_to_new_tail</code> becomes <code>length - 0 - 1 = length - 1</code>.</li>
<li>The loop for <code>new_tail</code> will correctly make <code>new_tail</code> the original tail, and <code>new_head</code> the original head. The list remains unchanged, which is correct for <code>k</code> being a multiple of <code>length</code>.</li>
</ul>
</li>
<li><strong><code>k &gt; length</code></strong>: Correctly handled by the <code>k % length</code> operation, reducing <code>k</code> to its effective rotations.</li>
<li><strong><code>k = 1</code> (single rotation)</strong>: <code>effective_k = 1</code>. <code>steps_to_new_tail = length - 1 - 1 = length - 2</code>. This correctly positions <code>new_tail</code> one node before the original tail, making the original tail the <code>new_head</code>.</li>
<li><strong><code>k = length - 1</code> (almost full rotation)</strong>: <code>effective_k = length - 1</code>. <code>steps_to_new_tail = length - (length - 1) - 1 = 0</code>. <code>new_tail</code> will be the original <code>head</code>. <code>new_head</code> will be <code>head.next</code>. This correctly places the original head at the end.</li>
</ul>
<p>The logic is robust for all valid inputs.</p>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability of <code>steps_to_new_tail</code></strong>: While correct, the calculation <code>length - effective_k - 1</code> might require a moment of thought to grasp its intent. An inline comment explaining <em>why</em> this number of steps is chosen could be beneficial. For example: <code>steps_to_new_tail = length - effective_k - 1 # The node *before* the new head</code></li>
<li><strong>Alternative Split Point Calculation</strong>: Instead of finding the <code>new_tail</code> first and then <code>new_head</code>, one could directly find the <code>new_head</code> by traversing <code>length - effective_k</code> steps from the original <code>head</code>, and then the <code>new_tail</code> would be the node <em>before</em> it (which required remembering the previous node, or traversing to <code>new_head</code> and then back-tracking or using <code>new_head</code>'s predecessor). The current approach is clean for a singly linked list.</li>
<li><strong>Two-Pointer Approach (variation)</strong>: For finding the split point without first forming a circle, one could use two pointers:<ol>
<li>Move a <code>fast</code> pointer <code>k</code> steps ahead.</li>
<li>Then, move both <code>fast</code> and <code>slow</code> pointers one step at a time until <code>fast</code> reaches the end.</li>
<li>At this point, <code>slow</code> will be at the node just before the new head.
This alternative might be slightly less intuitive than the circular list approach for some, as it requires careful handling of <code>k % length</code> and the initial <code>k</code> steps. However, it avoids the temporary circular transformation.</li>
</ol>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The O(N) time complexity is optimal for this problem, as you inherently need to traverse the entire list at least once (to find its length) and potentially a second time (to find the split point).</li>
<li><strong>Robustness</strong>: The initial checks for <code>None</code> and single-node lists, as well as <code>k=0</code>, make the function robust against common edge cases. No memory leaks are apparent as only existing pointers are modified.</li>
</ul>


### Code:
```python
class Solution(object):
    def rotateRight(self, head, k):
        if not head or not head.next or k == 0:
            return head

        # 1. Find the length of the list and the tail node
        current = head
        length = 1
        while current.next:
            current = current.next
            length += 1
        
        # 'current' is now the tail node
        tail = current

        # 2. Make the list circular by connecting tail to head
        tail.next = head

        # 3. Calculate the effective number of rotations
        # k might be larger than length, so we take k % length
        effective_k = k % length
        
        # 4. Find the new tail and new head
        # The new tail is (length - effective_k - 1) nodes away from the original head.
        # The new head is (length - effective_k) nodes away from the original head.
        
        steps_to_new_tail = length - effective_k - 1
        
        new_tail = head
        for _ in range(steps_to_new_tail):
            new_tail = new_tail.next
        
        new_head = new_tail.next
        
        # 5. Break the circle
        new_tail.next = None
        
        return new_head
```
