## Swap Nodes in Pairs
**Language:** python
**Tags:** python,linked list,recursion,list manipulation

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a recursive solution to swap every two adjacent nodes in a singly linked list. For example, a list <code>1-&gt;2-&gt;3-&gt;4</code> would become <code>2-&gt;1-&gt;4-&gt;3</code>. The function is designed to return the head of the modified list.</p>
<h3>2. How It Works</h3>
<p>The function employs a recursive approach to solve the problem:</p>
<ul>
<li><strong>Base Case:</strong><ul>
<li>If the list is empty (<code>head is None</code>) or has only one node (<code>head.next is None</code>), no swapping is needed, so the function simply returns the <code>head</code> as is. This is the termination condition for the recursion.</li>
</ul>
</li>
<li><strong>Recursive Step:</strong><ul>
<li>It identifies the <code>first_node</code> (current <code>head</code>) and <code>second_node</code> (the node right after <code>head</code>). These are the two nodes to be swapped in the current pair.</li>
<li><strong>Recursive Call:</strong> <code>first_node.next = self.swapPairs(second_node.next)</code> is the core of the recursion. It recursively calls <code>swapPairs</code> on the rest of the list, starting from the node <em>after</em> the <code>second_node</code> (i.e., <code>second_node.next</code>). This call will return the head of the <em>already swapped</em> sublist. The <code>first_node</code> is then made to point to this new head.</li>
<li><strong>Current Pair Swap:</strong> <code>second_node.next = first_node</code> completes the swap of the current pair. The <code>second_node</code> (which was originally second) now points to the <code>first_node</code> (which was originally first).</li>
<li><strong>Return Value:</strong> The function returns <code>second_node</code>. This is because <code>second_node</code> has become the new head of the <em>currently swapped pair</em>, and thus the new head of the sublist starting at this pair.</li>
</ul>
</li>
</ul>
<p><strong>Example Trace (<code>1 -&gt; 2 -&gt; 3 -&gt; 4 -&gt; None</code>):</strong></p>
<ol>
<li><code>swapPairs(1)</code><ul>
<li><code>first_node = 1</code>, <code>second_node = 2</code></li>
<li><code>1.next = swapPairs(3)</code> (recursive call)<ul>
<li><code>swapPairs(3)</code><ul>
<li><code>first_node = 3</code>, <code>second_node = 4</code></li>
<li><code>3.next = swapPairs(None)</code> (recursive call)<ul>
<li><code>swapPairs(None)</code> -&gt; Base case, returns <code>None</code></li>
</ul>
</li>
<li><code>3.next</code> becomes <code>None</code></li>
<li><code>4.next = 3</code></li>
<li>Returns <code>4</code> (now <code>4 -&gt; 3 -&gt; None</code>)</li>
</ul>
</li>
</ul>
</li>
<li><code>1.next</code> becomes <code>4</code> (list fragment: <code>1 -&gt; 4 -&gt; 3 -&gt; None</code>)</li>
<li><code>2.next = 1</code> (list fragment: <code>2 -&gt; 1 -&gt; 4 -&gt; 3 -&gt; None</code>)</li>
<li>Returns <code>2</code></li>
</ul>
</li>
</ol>
<p>The final result is <code>2 -&gt; 1 -&gt; 4 -&gt; 3 -&gt; None</code>.</p>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Recursion:</strong> The primary design choice is recursion. This allows for an elegant solution where the problem of swapping pairs in a list is broken down into swapping the first pair and then recursively solving the problem for the rest of the list.</li>
<li><strong>Pointer Manipulation:</strong> The solution relies heavily on careful manipulation of <code>next</code> pointers to rewire the list nodes without creating new nodes.</li>
<li><strong>In-Place Modification:</strong> The linked list is modified in-place, meaning no new list or extensive node copying is performed.</li>
</ul>
<p><strong>Trade-offs:</strong></p>
<ul>
<li><strong>Elegance vs. Performance/Memory:</strong> Recursive solutions can be very concise and elegant but often come with a trade-off in terms of stack space usage and potential for hitting recursion depth limits (especially in Python).</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Each node in the list is visited exactly once as the recursion unwinds. For each node, a constant number of pointer reassignments are performed. Therefore, the time complexity is directly proportional to the number of nodes <code>N</code> in the list.</li>
</ul>
</li>
<li><strong>Space Complexity: O(N)</strong><ul>
<li>Due to the recursive calls, the call stack will grow. In the worst case (a list with <code>N</code> nodes), the maximum recursion depth will be <code>N/2</code> (for pairs), or effectively <code>N</code> if each call is counted for each node processing. This results in <code>O(N)</code> space complexity for the call stack.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The code handles critical edge cases correctly:</p>
<ul>
<li><strong>Empty List (<code>head = None</code>):</strong> The base case <code>if not head</code> catches this and returns <code>None</code>, which is correct.</li>
<li><strong>Single-Node List (<code>head = 1 -&gt; None</code>):</strong> The base case <code>if not head.next</code> catches this and returns the <code>head</code> (the single node), which is correct as no swap is possible.</li>
<li><strong>Two-Node List (<code>1 -&gt; 2 -&gt; None</code>):</strong><ul>
<li><code>first = 1</code>, <code>second = 2</code></li>
<li><code>1.next = swapPairs(None)</code> -&gt; returns <code>None</code></li>
<li><code>1.next</code> becomes <code>None</code></li>
<li><code>2.next = 1</code></li>
<li>Returns <code>2</code> (result: <code>2 -&gt; 1 -&gt; None</code>). Correct.</li>
</ul>
</li>
<li><strong>Odd Number of Nodes (<code>1 -&gt; 2 -&gt; 3 -&gt; None</code>):</strong> The last node (<code>3</code>) will be handled by the base case (<code>swapPairs(3)</code>) which returns <code>3</code>, correctly leaving it unswapped and attached to the preceding swapped pair.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Iterative Approach:</strong> An iterative solution using a dummy head node and two or three pointers (e.g., <code>prev</code>, <code>curr</code>, <code>next_node</code>) would be a strong alternative.<ul>
<li><strong>Advantages:</strong> Avoids the recursion stack overhead, making it more efficient in terms of space complexity (O(1)) and preventing <code>RecursionError</code> for very long lists.</li>
<li><strong>Disadvantage:</strong> Can sometimes be slightly more complex to write and debug due to manual pointer management.</li>
<li><strong>Example Structure:</strong><pre><code class="language-python">dummy = ListNode(0)
dummy.next = head
prev = dummy
while prev.next and prev.next.next:
    first = prev.next
    second = prev.next.next

    # Swap logic
    first.next = second.next
    second.next = first
    prev.next = second

    # Move pointers for next pair
    prev = first
return dummy.next
</code></pre>
</li>
</ul>
</li>
<li><strong>Readability:</strong> The current recursive solution is quite readable and well-commented for its complexity. No significant readability improvements are immediately necessary for this specific implementation.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance (Recursion Depth):</strong> Python has a default recursion limit (often around 1000-3000 calls). For extremely long linked lists (e.g., tens of thousands of nodes), this recursive solution could hit the <code>RecursionError</code> limit. An iterative approach is generally preferred in production environments for very large inputs in languages like Python due to this constraint.</li>
<li><strong>Memory Usage:</strong> The <code>O(N)</code> space complexity for the call stack, while not excessively high, is a consideration compared to the <code>O(1)</code> space of an iterative solution.</li>
</ul>


### Code:
```python
class Solution(object):
    def swapPairs(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        # Base case: if the list is empty or has only one node, no swap is needed.
        if not head or not head.next:
            return head

        # 'first_node' is the current head (e.g., 1)
        # 'second_node' is the node after the head (e.g., 2)
        first_node = head
        second_node = head.next

        # Recursively call swapPairs for the rest of the list, starting from 'second_node.next' (e.g., 3).
        # The 'first_node' (1) will now point to the head of the recursively swapped sublist (4).
        first_node.next = self.swapPairs(second_node.next)

        # Now, link 'second_node' (2) to 'first_node' (1) to complete the current pair swap.
        second_node.next = first_node

        # 'second_node' (2) is the new head of this swapped pair.
        return second_node
```
