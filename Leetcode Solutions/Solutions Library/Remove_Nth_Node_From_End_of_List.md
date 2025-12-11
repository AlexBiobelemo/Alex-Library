## Remove Nth Node From End of List
**Language:** python
**Tags:** linked list,two pointers,deletion,algorithm

### Description:
<p>This code effectively solves the problem of removing the Nth node from the end of a singly linked list. It employs a common and efficient two-pointer technique.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given the <code>head</code> of a singly linked list and an integer <code>n</code>, remove the <code>n</code>th node from the end of the list and return its head.</li>
<li><strong>Goal:</strong> Efficiently locate and remove a specific node based on its position from the end, which requires a single pass through the list.</li>
</ul>
<h3>2. How It Works</h3>
<p>The solution uses a two-pointer approach with a <code>dummy</code> node to simplify edge cases:</p>
<ol>
<li><strong>Initialize Dummy Node:</strong> A <code>dummy</code> node is created and points to the original <code>head</code>. This is crucial for handling cases where the head of the list itself needs to be removed (e.g., <code>n</code> equals the list's length). The final result will be <code>dummy.next</code>.</li>
<li><strong>Initialize Two Pointers:</strong> <code>first</code> and <code>second</code> pointers are both initialized to the <code>dummy</code> node.</li>
<li><strong>Advance <code>first</code> Pointer:</strong> The <code>first</code> pointer is advanced <code>n + 1</code> steps ahead. This creates a gap of <code>n</code> nodes between <code>first</code> and <code>second</code>. The <code>+1</code> is critical: when <code>first</code> reaches <code>None</code> (the end of the list), <code>second</code> will be positioned exactly one node <em>before</em> the node to be removed.</li>
<li><strong>Move Both Pointers Together:</strong> Both <code>first</code> and <code>second</code> pointers are then moved forward one step at a time until <code>first</code> reaches <code>None</code>.</li>
<li><strong>Remove Node:</strong> At this point, <code>second</code> is pointing to the node <em>preceding</em> the <code>n</code>th node from the end. The removal is performed by updating <code>second.next</code> to skip over the target node: <code>second.next = second.next.next</code>.</li>
<li><strong>Return Head:</strong> The method returns <code>dummy.next</code>, which is the new head of the modified list.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dummy Node:</strong><ul>
<li><strong>Purpose:</strong> Simplifies handling of edge cases, particularly when the node to be removed is the original head of the list. Without it, a special check for <code>if n == length_of_list</code> would be needed, and the return value would be different.</li>
<li><strong>Trade-off:</strong> Minimal extra space for one <code>ListNode</code> object.</li>
</ul>
</li>
<li><strong>Two-Pointer Approach (One Pass):</strong><ul>
<li><strong>Purpose:</strong> Allows solving the problem in a single traversal of the linked list. To find the <code>n</code>th node from the end, one needs to know the list's total length or use a relative offset. Two pointers provide this relative offset.</li>
<li><strong>Offset <code>n + 1</code>:</strong> The specific initial advance of <code>n + 1</code> steps for <code>first</code> is key. It ensures that <code>second</code> lands on the node <em>before</em> the target, enabling direct modification of <code>second.next</code>.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(L)</strong><ul>
<li>The <code>first</code> pointer advances <code>n + 1</code> times.</li>
<li>Both pointers then advance together until <code>first</code> reaches the end, which takes approximately <code>(L - n - 1)</code> steps (where <code>L</code> is the total length of the list).</li>
<li>The total number of steps is <code>(n + 1) + (L - n - 1) = L</code>. Thus, it's a single pass through the list.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>Only a constant number of extra pointers (<code>dummy</code>, <code>first</code>, <code>second</code>) are used, regardless of the list's size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n</code> is 1 (removing the last node):</strong> The logic holds. <code>first</code> advances <code>2</code> steps. When <code>first</code> reaches <code>None</code>, <code>second</code> will be at the second-to-last node, correctly allowing it to set its <code>next</code> to <code>None</code>.</li>
<li><strong><code>n</code> equals list length (removing the first node):</strong> This is where the <code>dummy</code> node is invaluable. <code>first</code> advances <code>n + 1</code> steps, placing it past the end. <code>second</code> remains at the <code>dummy</code> node. <code>second.next</code> (which is <code>dummy.next</code>, the original head) is then correctly updated to <code>second.next.next</code> (the second node in the original list), effectively removing the head.</li>
<li><strong>Single node list (<code>L=1, n=1</code>):</strong> <code>dummy -&gt; 1 -&gt; None</code>. <code>first</code> advances <code>n+1=2</code> steps, becoming <code>None</code>. <code>second</code> remains at <code>dummy</code>. <code>dummy.next</code> (which is <code>1</code>) is set to <code>dummy.next.next</code> (which is <code>None</code>). Returns <code>None</code>, correct.</li>
<li><strong>Constraints (<code>1 &lt;= n &lt;= list.length</code>):</strong> The problem typically guarantees <code>n</code> is within valid bounds. If <code>n</code> could be invalid (e.g., <code>n &gt; L</code> or <code>n &lt;= 0</code>), the code would need additional checks (e.g., <code>first</code> might become <code>None</code> prematurely, or <code>second.next</code> could be <code>None</code> when attempting <code>second.next.next</code>). Given typical constraints, the current code is correct.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is quite readable. The variable names <code>first</code> and <code>second</code> clearly indicate their roles, and the comments accurately explain the logic, especially the <code>n + 1</code> advance.</li>
<li><strong>Performance:</strong> The current approach is optimal in terms of time (single pass) and space (constant extra space). There are no significant performance improvements to be made to the core algorithm.</li>
<li><strong>Robustness (if constraints were looser):</strong><ul>
<li>Add explicit checks for <code>head is None</code> or <code>n</code> being out of bounds if the problem constraints didn't guarantee validity.</li>
</ul>
</li>
<li><strong>Alternative (Two-Pass Approach):</strong><ul>
<li><strong>First Pass:</strong> Traverse the list to count its total length, <code>L</code>.</li>
<li><strong>Second Pass:</strong> Traverse again <code>(L - n)</code> steps from the head (or <code>L - n - 1</code> steps to reach the node <em>before</em> the target) and then remove the node.</li>
<li><strong>Comparison:</strong> This approach also has <code>O(L)</code> time complexity but involves two full passes. The one-pass two-pointer method is generally preferred for its elegance and slightly better constant factors (fewer memory accesses).</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This algorithm operates purely on in-memory data structures and does not involve external input, network communication, or system resources that typically pose security risks. There are no direct security concerns.</li>
<li><strong>Performance:</strong> As noted, the <code>O(L)</code> time and <code>O(1)</code> space complexity are optimal for this problem, making it highly performant for typical linked list sizes.</li>
</ul>


### Code:
```python
# Definition for singly-linked list.
class ListNode(object):
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
class Solution(object):
    def removeNthFromEnd(self, head, n):
        """
        :type head: Optional[ListNode]
        :type n: int
        :rtype: Optional[ListNode]
        """
        dummy = ListNode(0)
        dummy.next = head

        first = dummy
        second = dummy

        # Advance first pointer n+1 steps
        # This makes first n+1 nodes ahead of second.
        # When first reaches the end, second will be at the node *before* the one to be removed.
        for _ in range(n + 1):
            first = first.next

        # Move both pointers until first reaches the end (None)
        while first is not None:
            first = first.next
            second = second.next

        # At this point, second is pointing to the node *before* the nth node from the end.
        # Remove the nth node
        second.next = second.next.next

        return dummy.next
```
