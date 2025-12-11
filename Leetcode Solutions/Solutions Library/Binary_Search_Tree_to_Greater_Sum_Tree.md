## Binary Search Tree to Greater Sum Tree
**Language:** python
**Tags:** python,binary search tree,tree traversal,recursion

### Description:
<p>This code converts a Binary Search Tree (BST) into a Greater Sum Tree (GST). In a GST, the value of each node is replaced with the sum of its original value and the original values of all nodes in the BST that are greater than or equal to it.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose:</strong> To transform a given Binary Search Tree (BST) into a Greater Sum Tree (GST).</li>
<li><strong>GST Definition:</strong> Each node's new value becomes the sum of its original value and all original node values in the BST that are greater than its original value.</li>
<li><strong>In-Place Modification:</strong> The transformation happens by modifying the node values of the existing tree directly, without creating a new tree structure.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution employs a recursive helper function <code>_traverse</code> and a <code>self.current_sum</code> variable to achieve the transformation:</p>
<ul>
<li><strong>Initialization:</strong> A <code>self.current_sum</code> variable is initialized to 0. This variable accumulates the sum of original node values as the tree is traversed.</li>
<li><strong>Reverse In-Order Traversal:</strong> The <code>_traverse</code> function performs a reverse in-order traversal of the BST. This means it visits nodes in the following order:<ol>
<li>Right subtree.</li>
<li>Current node.</li>
<li>Left subtree.
This traversal order naturally processes nodes from largest value to smallest value.</li>
</ol>
</li>
<li><strong>Node Value Update:</strong> When a node is visited:<ol>
<li>It first recursively processes its <strong>right subtree</strong>. This ensures that <code>self.current_sum</code> will contain the sum of all nodes whose original values are <em>greater</em> than the current node's original value.</li>
<li>It stores the current node's <code>original_node_val</code>.</li>
<li>It updates the current node's value (<code>node.val</code>) by adding <code>self.current_sum</code> to <code>node.val</code>. At this point, <code>self.current_sum</code> holds the sum of all original values <em>greater</em> than the current node's value.</li>
<li>It adds the <code>original_node_val</code> (of the current node) to <code>self.current_sum</code>. This updates the running sum to include the current node's value, which will be used for subsequent (smaller) nodes in the left subtree.</li>
<li>It then recursively processes its <strong>left subtree</strong>.</li>
</ol>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Reverse In-Order Traversal:</strong> This is the core algorithmic decision. By visiting nodes in descending order of their values (Right -&gt; Current -&gt; Left), the <code>current_sum</code> variable correctly accumulates the sum of all <em>greater</em> nodes before the current node's value is modified.</li>
<li><strong>In-Place Modification:</strong> Modifying <code>node.val</code> directly avoids the overhead of creating new <code>TreeNode</code> objects and restructuring the tree, making it memory-efficient.</li>
<li><strong>Class/Instance Variable (<code>self.current_sum</code>):</strong> Using <code>self.current_sum</code> (an instance variable) allows the sum to be maintained and updated across all recursive calls without needing to pass it explicitly as an argument or return it from each call. For a nested function, <code>nonlocal</code> could also be used if <code>current_sum</code> were defined in the outer scope.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: O(N)</strong></p>
<ul>
<li>Every node in the BST is visited exactly once during the traversal.</li>
<li>Each visit involves a constant number of operations (comparisons, arithmetic, assignment, recursive calls).</li>
<li>Therefore, the total time complexity is directly proportional to the number of nodes (N).</li>
</ul>
</li>
<li><p><strong>Space Complexity: O(H)</strong></p>
<ul>
<li>This is determined by the maximum depth of the recursion stack.</li>
<li><code>H</code> represents the height of the BST.</li>
<li>In the worst case (a skewed tree, resembling a linked list), <code>H</code> can be <code>N</code>, leading to <code>O(N)</code> space complexity.</li>
<li>In the best/average case (a balanced tree), <code>H</code> is <code>log N</code>, leading to <code>O(log N)</code> space complexity.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Tree (<code>root = None</code>):</strong><ul>
<li>The base case <code>if not node: return</code> handles this gracefully. <code>_traverse(None)</code> is called and immediately returns. The original <code>root</code> (which is <code>None</code>) is returned. Correct.</li>
</ul>
</li>
<li><strong>Single Node Tree:</strong><ul>
<li><code>_traverse(node)</code> will call <code>_traverse(None)</code> for its right child, which returns.</li>
<li><code>node.val</code> is updated to <code>original_node_val + 0</code> (since <code>self.current_sum</code> is initially 0).</li>
<li><code>self.current_sum</code> is updated to include <code>original_node_val</code>.</li>
<li><code>_traverse(None)</code> for its left child returns.</li>
<li>The single node's value remains unchanged, which is correct as there are no greater nodes.</li>
</ul>
</li>
<li><strong>Skewed Trees (e.g., all left children or all right children):</strong><ul>
<li>The reverse in-order traversal correctly navigates even skewed trees, processing nodes in descending order of value, ensuring the <code>current_sum</code> is correctly calculated for each node.</li>
</ul>
</li>
<li><strong>Duplicate Values:</strong> If the BST allows duplicate values, the algorithm treats them as distinct entities in the sum, correctly including them in <code>self.current_sum</code> as they are encountered.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Iterative Approach:</strong> An iterative solution using an explicit stack can achieve the same result without relying on recursion. This would eliminate concerns about Python's recursion depth limit for very deep trees, although the asymptotic space complexity remains <code>O(H)</code>.<ul>
<li>This typically involves pushing nodes onto a stack and processing them in a similar reverse in-order fashion.</li>
</ul>
</li>
<li><strong>Clearer Scope for Accumulator:</strong> While <code>self.current_sum</code> works, some might prefer to pass the <code>current_sum</code> explicitly as an argument to the recursive helper function and have the helper return the updated sum. This makes the data flow more explicit but can sometimes make the signature more cumbersome. Alternatively, using <code>nonlocal current_sum</code> if <code>_traverse</code> were defined directly inside <code>bstToGst</code> but not as a method of <code>self</code>.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Recursion Depth Limit:</strong> For extremely deep BSTs (e.g., 1000+ nodes in a single path), Python's default recursion limit might be exceeded, leading to a <code>RecursionError</code>. An iterative approach would be more robust against this. This is typically not an issue for balanced trees or trees with moderate depth.</li>
<li><strong>Memory Efficiency:</strong> The in-place modification is highly memory-efficient as it avoids creating new tree structures or nodes.</li>
</ul>


### Code:
```python
class Solution(object):
    def bstToGst(self, root):
        """
        :type root: Optional[TreeNode]
        :rtype: Optional[TreeNode]
        """
        self.current_sum = 0

        def _traverse(node):
            if not node:
                return

            # Traverse the right subtree first (contains values greater than current node)
            _traverse(node.right)

            # Store the original value of the current node before modifying it
            original_node_val = node.val
            
            # Update the current node's value: original value + sum of all greater values encountered so far
            node.val += self.current_sum
            
            # Add the original value of the current node to the running sum
            # This sum will be carried over to the left subtree (for nodes smaller than current node)
            self.current_sum += original_node_val

            # Traverse the left subtree (contains values smaller than current node)
            _traverse(node.left)

        _traverse(root)
        return root
```
