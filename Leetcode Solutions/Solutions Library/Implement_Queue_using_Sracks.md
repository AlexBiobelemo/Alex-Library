## Implement Queue using Sracks
**Language:** python
**Tags:** python,oop,queue,stack

### Description:
This `MyQueue` class provides an implementation of a standard Queue (First-In, First-Out) data structure using two LIFO (Last-In, First-Out) stacks. This is a common and insightful interview problem demonstrating understanding of data structure principles.

---

### 1. Overview & Intent

*   **Intent**: To implement a Queue data structure (FIFO) using only the operations of a Stack (LIFO).
*   **Purpose**: The class `MyQueue` mimics the behavior of a queue, allowing elements to be added to the back (`push`), removed from the front (`pop`), viewed at the front (`peek`), and checked for emptiness (`empty`).
*   **Context**: This pattern is often used in situations where only stack-like primitives are available or as a fundamental computer science exercise to demonstrate data structure transformations.

---

### 2. How It Works

The core idea is to use two internal stacks: `input_stack` and `output_stack`.

*   **`__init__`**:
    *   Initializes two empty Python lists, `self.input_stack` and `self.output_stack`, which will serve as our underlying stacks.
*   **`push(x)`**:
    *   Elements are added directly to the `input_stack`. This stack effectively holds elements in the order they were enqueued, but "reversed" from a queue's front perspective (i.e., the *oldest* elements are at the bottom of this stack).
*   **`_transfer_elements()` (Helper Method)**:
    *   This private method is crucial for maintaining FIFO order.
    *   It is called whenever an element needs to be dequeued (`pop`) or peeked (`peek`) from the front.
    *   If `output_stack` is empty, it means we need to "reorient" the elements to get the oldest one.
    *   It repeatedly pops all elements from `input_stack` and appends them to `output_stack`. This process effectively reverses the order of elements from `input_stack`, such that the *first* element ever pushed into `input_stack` becomes the *top* element of `output_stack`.
*   **`pop()`**:
    *   First, it calls `_transfer_elements()` to ensure `output_stack` is populated with elements in the correct FIFO order (oldest at the top).
    *   Then, it `pop()`s and returns the top element from `output_stack`, which is guaranteed to be the front of the queue.
*   **`peek()`**:
    *   Similar to `pop()`, it calls `_transfer_elements()` to prepare `output_stack`.
    *   It then returns the top element of `output_stack` (`self.output_stack[-1]`) without removing it.
*   **`empty()`**:
    *   The queue is considered empty only if *both* `input_stack` and `output_stack` are empty.

---

### 3. Key Design Decisions

*   **Two Stacks for FIFO**: The primary design decision is the use of two LIFO stacks to achieve FIFO behavior. `input_stack` collects new elements, while `output_stack` serves elements in FIFO order.
*   **Lazy Transfer**: Elements are only transferred from `input_stack` to `output_stack` when a `pop` or `peek` operation requires elements and `output_stack` is currently empty. This "lazy" approach optimizes the common case of multiple `push` operations followed by multiple `pop` operations, as the transfer only happens once.
*   **Python Lists as Stacks**: Python's `list` type is used, leveraging `append()` for pushing elements (O(1) amortized) and `pop()` for popping elements from the end (O(1)). This is an efficient way to simulate a stack.
*   **Private Helper Method (`_transfer_elements`)**: Encapsulates the core reordering logic, making `pop` and `peek` cleaner and avoiding code duplication.

---

### 4. Complexity

*   **Time Complexity**:
    *   **`push(x)`**: O(1) amortized. Python list `append` operation is typically O(1) amortized.
    *   **`pop()` / `peek()`**: O(1) amortized.
        *   **Worst Case**: If `output_stack` is empty and `input_stack` contains N elements, `_transfer_elements` will perform N pops from `input_stack` and N appends to `output_stack`, resulting in O(N) for that specific call.
        *   **Amortized Analysis**: Each element is pushed onto `input_stack` once, popped from `input_stack` once, pushed onto `output_stack` once, and popped from `output_stack` once over its entire lifecycle. Therefore, a sequence of M operations (pushes and pops) will take O(M) time in total, leading to an average (amortized) cost of O(1) per operation.
    *   **`empty()`**: O(1), as it only checks the length of two lists.
*   **Space Complexity**:
    *   **O(N)**, where N is the total number of elements currently stored in the queue. All elements are stored across `input_stack` and `output_stack`.

---

### 5. Edge Cases & Correctness

*   **Empty Queue**:
    *   `empty()` correctly returns `True` if both stacks are empty.
    *   The problem statement assumes `pop` and `peek` are not called on an empty queue, thus avoiding `IndexError`.
*   **Queue with Single Element**:
    *   `push(x)`: `input_stack = [x]`, `output_stack = []`
    *   `pop()`: `_transfer_elements` moves `x` to `output_stack`. `pop()` returns `x`. Correct.
    *   `peek()`: `_transfer_elements` moves `x` to `output_stack`. `peek()` returns `x`. Correct.
*   **Alternating Push/Pop**: The lazy transfer mechanism ensures correctness. For example: `push(1)`, `push(2)`, `pop()` (returns 1), `push(3)`, `pop()` (returns 2). This sequence works correctly because `_transfer_elements` only recharges `output_stack` when it's depleted.
*   **Many Pushes, then Many Pops**: The transfer will occur only once when the first `pop` after all pushes is called (if `output_stack` was empty), or when `output_stack` becomes empty. This aligns with the O(1) amortized behavior.

---

### 6. Improvements & Alternatives

*   **Error Handling for `pop()`/`peek()`**: The current implementation assumes the queue is not empty when `pop()` or `peek()` are called. In a robust production system, these methods should either:
    *   Raise an `IndexError` or a custom `EmptyQueueError` if the queue is empty.
    *   Return a special value (e.g., `None`), though this would require changing the return type signature.
*   **Clarity on `_transfer_elements`**: While the docstring is good, adding a comment to `pop()` and `peek()` explaining *why* `_transfer_elements` is called (to ensure FIFO order when dequeuing) could be beneficial for beginners.
*   **Type Hinting for `_transfer_elements`**: Although it's a private method, consistency could suggest adding `-> None` to its signature.
*   **Alternative Implementations**:
    *   **Using `collections.deque`**: For a true production-grade queue in Python, `collections.deque` offers O(1) appends and pops from both ends and is generally preferred over a custom stack-based implementation unless the "use only stacks" constraint is explicit.
    *   **Single Stack (Recursive Call Stack)**: Some queue implementations using a single stack rely on recursion to effectively reverse elements, but this can lead to stack overflow for very deep queues.

---

### 7. Security/Performance Notes

*   **Amortized Performance**: The O(1) amortized time complexity for all critical operations (`push`, `pop`, `peek`) is excellent and efficient for most use cases. The occasional O(N) cost for `pop`/`peek` is balanced out over many operations.
*   **Memory Footprint**: Standard Python list memory usage. No significant memory-related security concerns or excessive overhead beyond typical object storage.
*   **Thread Safety**: This implementation is not inherently thread-safe. If multiple threads were to access and modify the `MyQueue` instance concurrently, race conditions could occur (e.g., during `_transfer_elements` or `pop`/`push`). For concurrent access, a `threading.Lock` or similar synchronization primitive would be required around critical sections.

### Code:
```python
class MyQueue:

    def __init__(self):
        self.input_stack = []
        self.output_stack = []

    def _transfer_elements(self):
        """
        Transfers elements from input_stack to output_stack if output_stack is empty.
        This reverses the order, making the oldest element from input_stack
        the top of output_stack.
        """
        if not self.output_stack:
            while self.input_stack:
                self.output_stack.append(self.input_stack.pop())

    def push(self, x: int) -> None:
        """
        Pushes element x to the back of the queue.
        """
        self.input_stack.append(x)

    def pop(self) -> int:
        """
        Removes the element from the front of the queue and returns it.
        Assumes the queue is not empty when pop is called.
        """
        self._transfer_elements()
        return self.output_stack.pop()

    def peek(self) -> int:
        """
        Returns the element at the front of the queue.
        Assumes the queue is not empty when peek is called.
        """
        self._transfer_elements()
        return self.output_stack[-1] # Peek at the top element

    def empty(self) -> bool:
        """
        Returns true if the queue is empty, false otherwise.
        """
        return not self.input_stack and not self.output_stack


# Your MyQueue object will be instantiated and called as such:
# obj = MyQueue()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.peek()
# param_4 = obj.empty()
```
