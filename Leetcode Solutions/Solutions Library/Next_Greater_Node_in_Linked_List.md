## Next Greater Node in Linked List
**Language:** python
**Tags:** python,oop,stack,linked list

### Description:
This code effectively solves the "Next Greater Node in Linked List" problem using a well-known algorithmic pattern.

---

### 1. Overview & Intent

This code aims to find, for each node in a singly-linked list, the value of the first node that appears later in the list and has a strictly larger value. If no such node exists for a given node, the result for that node is `0`. The function `nextLargerNodes` takes the head of a linked list and returns a list of integers representing these "next larger" values, corresponding to the order of nodes in the input list.

### 2. How It Works

The algorithm proceeds in two main steps:

*   **Flattening the Linked List**:
    *   It first traverses the entire input linked list, extracting the `val` attribute from each `ListNode`.
    *   These values are sequentially stored in a standard Python list called `nodes_values`. This converts the linked list structure into an easily indexable array, simplifying subsequent processing.
*   **Finding Next Greater Elements using a Monotonic Stack**:
    *   An `answer` list is initialized with `0`s, with the same length as `nodes_values`. This list will store the final results.
    *   A stack is used, which stores tuples of `(index, value)`. This stack is maintained such that the `value` components of the tuples are in *decreasing* order from bottom to top (a monotonic decreasing stack).
    *   The code iterates through `nodes_values` from left to right (index `i` from `0` to `n-1`).
    *   For each `current_value` at index `i`:
        *   It repeatedly checks the top element of the stack. If the `value` on the stack's top is *less* than `current_value`, it means `current_value` is the "next larger element" for the node represented by the popped stack element.
        *   The top element `(prev_index, _)` is popped from the stack, and `answer[prev_index]` is updated with `current_value`. This continues until the stack is empty or the top element's value is greater than or equal to `current_value`.
        *   After this, `(i, current_value)` is pushed onto the stack.
    *   Any elements remaining on the stack at the end of the loop indicate nodes for which no "next larger element" was found to their right, and their corresponding `answer` entries correctly remain `0`.

### 3. Key Design Decisions

*   **Linked List to Array Conversion**:
    *   **Decision**: Convert the linked list into a Python list (`nodes_values`) upfront.
    *   **Rationale**: Directly operating on a linked list for "next greater element" problems can be cumbersome, potentially requiring nested loops or reverse traversals. Converting to an array provides efficient random access (`O(1)` by index) which simplifies the monotonic stack logic and its ability to update results at arbitrary previous indices.
    *   **Trade-offs**: This consumes `O(N)` additional space and `O(N)` time for the initial traversal. However, given Python's list efficiency and the clarity it provides, this is often a worthwhile trade-off.
*   **Monotonic Decreasing Stack**:
    *   **Decision**: Utilize a stack that maintains its elements in strictly decreasing order of their values from bottom to top.
    *   **Rationale**: This is a standard and highly efficient pattern for "next greater element" type problems. By processing elements from left to right, when a new `current_value` is encountered, it can "resolve" all preceding elements on the stack that are smaller than it, as `current_value` is indeed their first larger element to the right.
    *   **Data Stored**: Each stack element is a `(index, value)` tuple. The `index` is critical for correctly placing the "next larger" value into the `answer` array once found, while `value` is used for comparison.
*   **Pre-initialization of `answer` array**:
    *   **Decision**: Initialize `answer` with `[0] * n`.
    *   **Rationale**: This elegantly handles the default case where a node has no "next larger" element to its right. If an index remains in the stack (or is never pushed because the list is empty), its corresponding `answer` value will naturally be `0` without explicit post-processing.

### 4. Complexity

Let `N` be the number of nodes in the linked list.

*   **Time Complexity**: O(N)
    *   The initial traversal to build `nodes_values` takes O(N) time.
    *   The `for` loop iterates `N` times. Each node value is pushed onto the stack at most once and popped from the stack at most once. Stack operations (`append`, `pop`, `[-1]`) are O(1).
    *   Therefore, the overall time complexity is dominated by these linear passes, resulting in O(N).
*   **Space Complexity**: O(N)
    *   `nodes_values` list: Stores `N` integers, requiring O(N) space.
    *   `answer` list: Stores `N` integers, requiring O(N) space.
    *   `stack`: In the worst-case scenario (e.g., a linked list with values in strictly decreasing order like `[5, 4, 3, 2, 1]`), all `N` elements might be pushed onto the stack before any are popped, requiring O(N) space.
    *   Thus, the total space complexity is O(N).

### 5. Edge Cases & Correctness

*   **Empty Linked List (`head` is `None`)**: The `nodes_values` list will be empty, `n` will be 0. The `if n == 0:` check correctly returns `[]`.
*   **Single Node Linked List (`[5]`)**: `nodes_values` will be `[5]`. `answer` will be `[0]`. The stack will become `[(0, 5)]`. No values will be popped. The result `[0]` is correct as there's no node to the right.
*   **All Nodes in Decreasing Order (`[5, 4, 3, 2, 1]`)**: All elements are pushed onto the stack. None are popped because no `current_value` is ever greater than `stack[-1][1]`. The `answer` remains `[0, 0, 0, 0, 0]`, which is correct.
*   **All Nodes in Increasing Order (`[1, 2, 3, 4, 5]`)**: Each `current_value` will pop all preceding elements from the stack (as they are all smaller) and update their `answer` entries. This correctly results in `[2, 3, 4, 5, 0]`.
*   **Duplicate Values (`[2, 1, 2]`)**:
    *   `i=0, val=2`: `stack = [(0, 2)]`
    *   `i=1, val=1`: `stack = [(0, 2), (1, 1)]`
    *   `i=2, val=2`: `1` is popped, `answer[1] = 2`. `stack` becomes `[(0, 2)]`. `2` (top) is not `<` `2` (current), so loop stops. `stack.append((2, 2))` making `stack = [(0, 2), (2, 2)]`.
    *   Final `answer = [0, 2, 0]`, which is correct.
The algorithm is robust across these cases due to the principled behavior of the monotonic stack.

### 6. Improvements & Alternatives

*   **Readability**: The code is quite clear and well-commented. Adding a comprehensive docstring would further enhance clarity for external users or large codebases.
*   **Alternative without Array Conversion**: While possible to solve directly on the linked list, it's significantly more complex. One common alternative involves reversing the linked list, applying the monotonic stack approach, and then either reversing the result list or reversing the linked list back. This avoids the O(N) temporary `nodes_values` array but adds complexity in linked list manipulation. The current array-based approach is generally preferred in Python due to `list` efficiency and simplified logic.
*   **Pythonic Stack**: Python lists inherently function as stacks using `append()` for push and `pop()` for pop from the end. This is efficiently implemented in C under the hood, so no alternative stack data structure is needed for performance reasons.

### 7. Security/Performance Notes

*   **Performance**: The O(N) time and space complexity is optimal for this problem, as every node must be visited, and results for all nodes might need to be stored. The use of Python's built-in list operations for stack management is highly optimized, contributing to good practical performance.
*   **Security**: There are no apparent security concerns with this code. It processes numerical data internally and does not interact with external systems, user input, or sensitive resources in a way that could introduce vulnerabilities.

### Code:
```python
class Solution:
    def nextLargerNodes(self, head: Optional[ListNode]) -> List[int]:
        nodes_values = []
        current = head
        while current:
            nodes_values.append(current.val)
            current = current.next
        
        n = len(nodes_values)
        if n == 0:
            return []
            
        answer = [0] * n
        
        # Stack stores tuples of (index, value)
        # We maintain a decreasing stack of values
        stack = [] # (index, value)
        
        for i in range(n):
            current_value = nodes_values[i]
            
            # While the stack is not empty and the value at the top of the stack
            # is less than the current node's value, it means we found the next
            # greater element for the node(s) on the stack.
            while stack and stack[-1][1] < current_value:
                prev_index, _ = stack.pop()
                answer[prev_index] = current_value
            
            # Push the current node's index and value onto the stack
            stack.append((i, current_value))
            
        return answer
```
