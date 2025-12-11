## Add Two Numbers
**Language:** python
**Tags:** python,linked list,algorithm,addition

### Description:
<p>Here's a review of the provided Python code for adding two numbers represented as linked lists.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a solution to add two non-negative integers represented as linked lists. Each node in the linked list stores a single digit, and the digits are stored in <em>reverse order</em>. For example, the number 342 would be represented as a linked list <code>2 -&gt; 4 -&gt; 3</code>. The function's goal is to return a new linked list representing the sum of the two input numbers, also in reverse digit order.</p>
<h3>2. How It Works</h3>
<p>The algorithm simulates the manual process of adding numbers column by column, starting from the least significant digit:</p>
<ul>
<li><strong>Initialization</strong>:<ul>
<li>A <code>dummy_head</code> node (with value 0) is created to simplify the process of building the result list, especially handling the head node and empty sums.</li>
<li>A <code>current</code> pointer points to <code>dummy_head</code> and will be used to append new nodes to the result list.</li>
<li>A <code>carry</code> variable is initialized to 0, storing any carry-over from previous digit sums.</li>
</ul>
</li>
<li><strong>Iteration</strong>: The code enters a <code>while</code> loop that continues as long as there are digits remaining in either <code>l1</code> or <code>l2</code>, or if there's a <code>carry</code> from a previous sum.<ul>
<li><strong>Digit Retrieval</strong>: For each iteration, it retrieves the <code>val</code> from <code>l1</code> and <code>l2</code>. If a list has been exhausted (<code>l1 is None</code> or <code>l2 is None</code>), its value is treated as 0.</li>
<li><strong>Sum Calculation</strong>: It calculates <code>sum_digits</code> by adding <code>val1</code>, <code>val2</code>, and the current <code>carry</code>.</li>
<li><strong>Update Carry &amp; New Digit</strong>:<ul>
<li><code>carry</code> is updated to <code>sum_digits // 10</code> (integer division to get the tens digit).</li>
<li><code>new_digit</code> is calculated as <code>sum_digits % 10</code> (modulo to get the units digit).</li>
</ul>
</li>
<li><strong>Append to Result</strong>: A new <code>ListNode</code> with <code>new_digit</code> is created and appended to the <code>current.next</code> of the result list.</li>
<li><strong>Advance Pointers</strong>: The <code>current</code> pointer is moved to the newly added node. The <code>l1</code> and <code>l2</code> pointers are also advanced to their <code>next</code> nodes, if they exist.</li>
</ul>
</li>
<li><strong>Return Result</strong>: Once the loop finishes, <code>dummy_head.next</code> is returned, effectively skipping the initial dummy node and giving the actual head of the sum list.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Dummy Head Node</strong>: This is a crucial pattern for building new linked lists. It simplifies the logic by always having a node to append to, avoiding special checks for when the result list is empty. The actual head is <code>dummy_head.next</code>.</li>
<li><strong>Iterative Approach</strong>: The solution uses an iterative loop, which is generally preferred over recursion for linked list problems to avoid potential stack overflow issues with very long lists and generally has less overhead.</li>
<li><strong><code>carry</code> Variable</strong>: Standard arithmetic logic dictates using a <code>carry</code> variable to propagate values between digit positions.</li>
<li><strong>Loop Condition <code>l1 or l2 or carry</code></strong>: This condition is robust. It ensures that the loop continues as long as there are digits left in either input list <em>or</em> if there's a final carry to be added (e.g., <code>5 + 5 = 10</code> requires an extra node for <code>1</code>).</li>
<li><strong>In-place Pointer Modification</strong>: The <code>l1</code> and <code>l2</code> pointers are advanced within the function. This is standard practice when inputs are consumed for calculation.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>: O(max(N, M))<ul>
<li>Where N is the length of <code>l1</code> and M is the length of <code>l2</code>.</li>
<li>The algorithm iterates through each list at most once, performing constant-time operations for each digit.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: O(max(N, M))<ul>
<li>A new linked list is constructed to store the sum. In the worst case (e.g., <code>999 + 1 = 1000</code>), the sum list can be one node longer than the maximum input list length.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code handles several edge cases gracefully:</p>
<ul>
<li><strong>Lists of Different Lengths</strong>: Handled by checking <code>l1 is not None</code> and <code>l2 is not None</code>. If one list is exhausted, its <code>val</code> is treated as 0, allowing the addition to continue with the remaining digits of the longer list and the <code>carry</code>.</li>
<li><strong>Sum Exceeding Digits</strong>: If the sum results in an extra digit (e.g., <code>[5]</code> + <code>[5]</code> = <code>[0, 1]</code>), the <code>carry != 0</code> in the loop condition ensures an additional node is created for the final carry.</li>
<li><strong>Empty Input Lists</strong>: While typical problem constraints might state non-empty lists, if <code>l1</code> or <code>l2</code> (or both) were initially <code>None</code>, the <code>val1 = l1.val if l1 is not None else 0</code> correctly assigns 0. If both are <code>None</code> and <code>carry</code> is 0, the loop won't execute, and <code>dummy_head.next</code> (which is <code>None</code>) will be returned, representing a sum of 0.</li>
<li><strong>Inputs with Zero</strong>: <code>[0]</code> + <code>[0]</code> results in <code>[0]</code>, which is correct.</li>
<li><strong>Large Numbers</strong>: Python's integers handle arbitrary precision, so <code>carry</code> and <code>sum_digits</code> won't overflow themselves. The linked list structure correctly handles the arbitrary length of the resulting number.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The current code is very readable and follows common conventions for this problem. No significant readability improvements are immediately apparent.</li>
<li><strong>Recursion (Alternative)</strong>: This problem <em>could</em> be solved recursively, where the base cases would be when both lists and the carry are 0. The recursive step would involve adding digits, calculating carry, and then making a recursive call for <code>l1.next</code>, <code>l2.next</code>, and the new carry. However, for potentially very long linked lists, an iterative solution is generally safer to avoid Python's recursion depth limit.</li>
<li><strong>Input Preservation</strong>: The current solution modifies the <code>l1</code> and <code>l2</code> pointers locally. If the original input linked lists needed to be preserved for external use after the function call (uncommon for this type of problem), deep copies would be required, but that adds complexity and is usually not expected.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The iterative solution is optimal in terms of Big-O time and space complexity. The constant overhead per node operation is minimal.</li>
<li><strong>Security</strong>: There are no inherent security concerns as this code performs a pure computational task on abstract data structures without external interactions, file I/O, network communication, or handling sensitive user input.</li>
</ul>


### Code:
```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: Optional[ListNode]
        :type l2: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        dummy_head = ListNode(0)
        current = dummy_head
        carry = 0

        while l1 is not None or l2 is not None or carry != 0:
            val1 = l1.val if l1 is not None else 0
            val2 = l2.val if l2 is not None else 0

            sum_digits = val1 + val2 + carry
            carry = sum_digits // 10
            new_digit = sum_digits % 10

            current.next = ListNode(new_digit)
            current = current.next

            if l1 is not None:
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        
        return dummy_head.next
```
