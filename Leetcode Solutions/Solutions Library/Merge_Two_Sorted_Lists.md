## Merge Two Sorted Lists
**Language:** python
**Tags:** linkedlist,merge,sorted,python

### Description:
<p>Here's a breakdown of the provided Python code for merging two sorted linked lists.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a function <code>mergeTwoLists</code> that takes two sorted singly linked lists (<code>list1</code> and <code>list2</code>) as input and merges them into a single new sorted linked list.</p>
<ul>
<li><strong>Input</strong>: Two potentially empty sorted linked lists, where each node has a <code>val</code> and a <code>next</code> pointer.</li>
<li><strong>Output</strong>: A new sorted linked list containing all elements from both input lists. If both input lists are empty, an empty list (represented by <code>None</code>) is returned.</li>
<li><strong>Goal</strong>: To combine two ordered sequences of elements (stored in linked lists) into one single ordered sequence while maintaining sorted order.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The function uses an iterative approach with a "dummy head" node to simplify the merging process.</p>
<ol>
<li><p><strong>Initialize Dummy Node</strong>:</p>
<ul>
<li>A <code>dummy</code> <code>ListNode</code> is created. This node doesn't store any actual data; its <code>next</code> pointer will eventually point to the head of the merged list.</li>
<li>A <code>current</code> pointer is initialized to <code>dummy</code>. This <code>current</code> pointer will traverse the new merged list, always pointing to the last node added.</li>
</ul>
</li>
<li><p><strong>Iterative Merging</strong>:</p>
<ul>
<li>The code enters a <code>while</code> loop that continues as long as <em>both</em> <code>list1</code> and <code>list2</code> have nodes (i.e., are not <code>None</code>).</li>
<li><strong>Comparison</strong>: Inside the loop, it compares the <code>val</code> of the current node in <code>list1</code> with the <code>val</code> of the current node in <code>list2</code>.</li>
<li><strong>Append Smaller Node</strong>:<ul>
<li>If <code>list1.val</code> is less than or equal to <code>list2.val</code>, <code>list1</code>'s current node is appended to the <code>current.next</code> of the merged list. Then, <code>list1</code> is advanced to its next node (<code>list1 = list1.next</code>).</li>
<li>Otherwise (if <code>list2.val</code> is smaller), <code>list2</code>'s current node is appended to <code>current.next</code>, and <code>list2</code> is advanced (<code>list2 = list2.next</code>).</li>
</ul>
</li>
<li><strong>Advance Current</strong>: In either case, after appending a node, the <code>current</code> pointer is moved forward to the newly added node (<code>current = current.next</code>).</li>
</ul>
</li>
<li><p><strong>Append Remaining Nodes</strong>:</p>
<ul>
<li>After the <code>while</code> loop finishes, one of the input lists (or both, if they ran out at the same time) will be exhausted (<code>None</code>). The other list might still have remaining nodes.</li>
<li>An <code>if</code> condition checks if <code>list1</code> still has nodes. If so, its entire remaining tail is appended to <code>current.next</code>.</li>
<li>An <code>elif</code> condition checks if <code>list2</code> still has nodes. If so, its entire remaining tail is appended to <code>current.next</code>.</li>
<li>Since both input lists were sorted, appending the entire remainder of whichever list is left ensures the final merged list remains sorted.</li>
</ul>
</li>
<li><p><strong>Return Merged List</strong>:</p>
<ul>
<li>Finally, <code>dummy.next</code> is returned. This skips the initial dummy node and points directly to the actual head of the sorted merged list (or <code>None</code> if both input lists were empty).</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Iterative Approach</strong>: The solution uses an iterative <code>while</code> loop instead of recursion. This avoids potential stack overflow issues for very long lists and can sometimes be slightly more performant due to less function call overhead.</li>
<li><strong>Dummy Head Node</strong>:<ul>
<li><strong>Simplifies Logic</strong>: Eliminates the need for special handling of the first node of the merged list. Without a dummy node, you'd have to check if the merged list is empty before assigning its head.</li>
<li><strong>Consistent Appending</strong>: Allows <code>current.next = ...</code> to be used for every node appended, including the very first one, by starting <code>current</code> at the dummy.</li>
</ul>
</li>
<li><strong>In-Place Merging (Pointer Manipulation)</strong>:<ul>
<li>The solution does not create new <code>ListNode</code> objects for all elements. Instead, it reuses the existing nodes from <code>list1</code> and <code>list2</code> by simply rearranging their <code>next</code> pointers.</li>
<li><strong>Efficiency</strong>: This is highly efficient in terms of memory as it avoids allocating new memory for each node and relies only on O(1) auxiliary space for pointers.</li>
</ul>
</li>
<li><strong>Stable Sort</strong>: The condition <code>list1.val &lt;= list2.val</code> ensures that if two nodes have equal values, the node from <code>list1</code> is chosen first. This maintains the relative order of elements with equal values from their original lists, making the merge "stable."</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N + M)</strong><ul>
<li>Where N is the number of nodes in <code>list1</code> and M is the number of nodes in <code>list2</code>.</li>
<li>The <code>while</code> loop iterates once for each node that is added to the merged list. In the worst case, every node from both lists will be visited and appended exactly once.</li>
<li>Therefore, the number of operations is directly proportional to the total number of elements in both lists.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a constant amount of extra space for the <code>dummy</code> node and the <code>current</code>, <code>list1</code>, and <code>list2</code> pointers.</li>
<li>No additional data structures that grow with input size are created.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases gracefully:</p>
<ul>
<li><strong>Both lists empty</strong>:<ul>
<li><code>list1</code> and <code>list2</code> are <code>None</code>. The <code>while</code> loop is skipped.</li>
<li><code>if list1</code> and <code>elif list2</code> are both false.</li>
<li><code>dummy.next</code> (which is <code>None</code>) is returned, correctly representing an empty merged list.</li>
</ul>
</li>
<li><strong>One list empty</strong>:<ul>
<li>If <code>list1</code> is empty (<code>None</code>), the <code>while</code> loop is skipped.</li>
<li><code>if list1</code> is false, <code>elif list2</code> is true. <code>current.next = list2</code> makes the dummy's next point to the head of <code>list2</code>.</li>
<li><code>dummy.next</code> correctly returns <code>list2</code>.</li>
<li>Symmetrically, if <code>list2</code> is empty, <code>list1</code> is returned.</li>
</ul>
</li>
<li><strong>Single-node lists</strong>: Handled correctly, as the loop processes them and the remaining-nodes logic appends any leftover.</li>
<li><strong>Lists with identical values</strong>: The <code>list1.val &lt;= list2.val</code> condition ensures that if values are equal, the node from <code>list1</code> is picked first. This provides a stable merge.</li>
<li><strong>Lists with a large number of nodes</strong>: The iterative approach avoids recursion depth limits, making it robust for very long lists.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The current code is already very readable and idiomatic for linked list operations. Variable names are clear, and the structure is logical. No major readability improvements are immediately apparent.</li>
<li><strong>Recursive Alternative</strong>:<ul>
<li>A common alternative for this problem is a recursive solution. It can be more concise for some, but might have higher overhead for function calls and a risk of stack overflow for extremely long lists.</li>
<li><strong>Concept</strong>:<pre><code class="language-python">def mergeTwoListsRecursive(self, l1, l2):
    if not l1: return l2
    if not l2: return l1

    if l1.val &lt;= l2.val:
        l1.next = self.mergeTwoListsRecursive(l1.next, l2)
        return l1
    else:
        l2.next = self.mergeTwoListsRecursive(l1, l2.next)
        return l2
</code></pre>
</li>
<li>This recursive approach generally has the same O(N+M) time complexity, but O(N+M) space complexity in the worst case due to the call stack.</li>
</ul>
</li>
<li><strong>Input Validation</strong>: For a production system (not typically needed in competitive programming), one might add checks to ensure <code>list1</code> and <code>list2</code> are indeed <code>ListNode</code> objects or <code>None</code>.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The solution is highly performant.<ul>
<li><strong>Optimal Time</strong>: O(N+M) is the best possible time complexity because every element from both lists must be examined at least once to ensure correct sorting.</li>
<li><strong>Optimal Space</strong>: O(1) auxiliary space is also optimal, as it avoids creating new nodes and only uses a few pointers. This is crucial for memory efficiency, especially with very large linked lists.</li>
</ul>
</li>
<li><strong>Security</strong>: There are no inherent security vulnerabilities in this algorithm. It operates on self-contained data structures and does not involve external interactions, data parsing from untrusted sources, or complex arithmetic that could lead to overflows or underflows. The operations are simple pointer manipulations within the confines of the linked list nodes.</li>
</ul>


### Code:
```python
class Solution(object):
    def mergeTwoLists(self, list1, list2):
        """
        :type list1: Optional[ListNode]
        :type list2: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        dummy = ListNode()
        current = dummy

        while list1 and list2:
            if list1.val <= list2.val:
                current.next = list1
                list1 = list1.next
            else:
                current.next = list2
                list2 = list2.next
            current = current.next

        if list1:
            current.next = list1
        elif list2:
            current.next = list2

        return dummy.next
```
