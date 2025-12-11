## LRU Cache
**Language:** python
**Tags:** python,oop,lru cache,hashmap,doubly linked list

### Description:
This code implements a Least Recently Used (LRU) Cache, a common data structure designed to store a limited number of key-value pairs and evict the least recently accessed item when the cache capacity is exceeded.

---

### 1. Overview & Intent

*   **Purpose:** To create a cache that stores a fixed number of items (key-value pairs) and efficiently retrieves or updates them.
*   **Eviction Strategy:** When the cache reaches its maximum capacity and a new item is added, the item that has not been accessed (read or written) for the longest time is removed.
*   **Use Cases:** Frequently used in web servers, database systems, and operating systems to improve performance by reducing the need to re-compute or re-fetch data.

---

### 2. How It Works

The `LRUCache` combines two core data structures to achieve its O(1) time complexity for `get` and `put` operations:

1.  **Hash Map (`self.cache` - a Python dictionary):**
    *   Stores `key -> Node` mappings. This allows for O(1) average-case lookup of a node given its key.

2.  **Doubly Linked List (`self.head`, `self.tail`, and `Node` objects):**
    *   Maintains the order of item usage.
    *   The most recently used (MRU) items are positioned at the `head` of the list.
    *   The least recently used (LRU) items are positioned at the `tail` of the list.
    *   Uses dummy `head` and `tail` nodes to simplify list manipulations (avoiding special checks for empty lists or single-node lists).

**Operations Flow:**

*   **`_add_node(node)`:** Inserts a `node` right after the dummy `head`. This makes it the MRU item.
*   **`_remove_node(node)`:** Deletes a `node` from its current position in the linked list.
*   **`_move_to_front(node)`:** A utility method that first removes a `node` from its current position and then adds it back to the front (MRU position).

*   **`get(key)`:**
    *   Checks if the `key` exists in `self.cache`.
    *   If found: retrieves the corresponding `Node`, moves it to the front of the linked list (marking it as MRU), and returns its `value`.
    *   If not found: returns -1.

*   **`put(key, value)`:**
    *   **If `key` exists:**
        *   Retrieves the existing `Node`.
        *   Updates its `value`.
        *   Moves the `Node` to the front of the linked list (marking it as MRU).
    *   **If `key` is new:**
        *   Creates a `new_node`.
        *   Adds `new_node` to `self.cache` and to the front of the linked list.
        *   **Eviction Check:** If the cache size now exceeds its `capacity`:
            *   Identifies the LRU node (which is `self.tail.prev` due to the dummy nodes).
            *   Removes this LRU node from the linked list.
            *   Deletes this LRU node from `self.cache` using its key.

---

### 3. Key Design Decisions

*   **Combination of Hash Map and Doubly Linked List:**
    *   The hash map provides average O(1) lookup of a node by its key.
    *   The doubly linked list allows for O(1) removal of an arbitrary node and O(1) insertion at the front or back, crucial for updating recency and evicting the LRU item.
*   **`Node` Structure:**
    *   Each `Node` stores `key`, `value`, `prev` pointer, and `next` pointer. Storing the `key` within the `Node` is critical because when an LRU node is removed from the linked list, its `key` is needed to delete it from the `self.cache` dictionary.
*   **Dummy `head` and `tail` Nodes:**
    *   These sentinel nodes simplify list manipulation logic significantly. They ensure that `self.head.next` and `self.tail.prev` always point to valid `Node` objects (even if they point to each other in an empty cache). This avoids repetitive `None` checks in `_add_node` and `_remove_node` and handles edge cases like adding to an empty list or removing the last element seamlessly.

---

### 4. Complexity

*   **Time Complexity:**
    *   **`get(key)`:** O(1) on average. Dictionary lookup is O(1) average. Linked list operations (`_move_to_front` involving `_remove_node` and `_add_node`) are constant time as they only involve a fixed number of pointer reassignments.
    *   **`put(key, value)`:** O(1) on average. Dictionary lookup/insertion/deletion is O(1) average. Linked list operations are O(1).
*   **Space Complexity:**
    *   O(capacity). The `self.cache` dictionary stores up to `capacity` references to `Node` objects. The linked list stores up to `capacity` `Node` objects (plus two dummy nodes). Each `Node` consumes constant space for key, value, and two pointers.

---

### 5. Edge Cases & Correctness

*   **Empty Cache:**
    *   `get` correctly returns -1.
    *   `put` correctly adds the first item, handling `len(self.cache)` correctly (it won't exceed capacity immediately).
*   **Cache at Capacity:**
    *   When `put` causes `len(self.cache) > self.capacity`, the `lru_node` (`self.tail.prev`) is correctly identified and removed from both the linked list and the dictionary.
*   **`capacity = 1`:**
    *   The logic correctly handles a single item. If a new item is added, the existing (and only) item is correctly evicted.
*   **`capacity = 0`:**
    *   The `__init__` method will still create dummy nodes.
    *   `put` will add an item, then immediately evict it because `len(self.cache)` will become 1, exceeding the 0 capacity. This behavior is technically correct (a cache of capacity 0 should store nothing), though it could be made more efficient for this specific case.
*   **Updating an Existing Key:**
    *   `put` correctly updates the `value` and moves the associated node to the MRU position.
*   **Accessing an Existing Key:**
    *   `get` correctly retrieves the `value` and moves the associated node to the MRU position.

---

### 6. Improvements & Alternatives

*   **Input Validation:**
    *   Add a check in `__init__` to ensure `capacity` is a positive integer. If `capacity <= 0`, it could raise a `ValueError` or initialize an always-empty cache.
*   **Type Hinting for Internal Methods:**
    *   While `key: int` and `value: int` are hinted for `Node` and public methods, adding type hints to internal methods (`_add_node`, `_remove_node`, `_move_to_front`) would improve readability and maintainability.
*   **Docstrings:**
    *   Adding comprehensive docstrings to the `LRUCache` class, `Node` class, and all methods would greatly enhance understanding and future maintenance.
*   **Pythonic Alternatives:**
    *   **`collections.OrderedDict`:** Python's standard library provides `OrderedDict`, which maintains insertion order. It can be used as the primary data structure, utilizing its `move_to_end()` and `popitem(last=False)` methods to implement an LRU cache with significantly less code.
    *   **`functools.lru_cache`:** For caching function results, Python offers a decorator `functools.lru_cache` that provides a simple and effective way to memoize function calls with an LRU strategy, often preferable for its simplicity when applicable.

---

### 7. Security/Performance Notes

*   **Performance:** The implementation achieves the optimal O(1) average-case time complexity for both `get` and `put` operations, which is excellent for a cache.
*   **Security:** As a standalone data structure, this code presents no inherent security vulnerabilities. It processes integer keys and values as specified. Potential security considerations would arise if keys or values were derived from untrusted input and then used in ways that could lead to injection attacks or resource exhaustion in other parts of an application. Within this specific cache logic, no such vulnerabilities are apparent.

### Code:
```python
class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _add_node(self, node):
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node

    def _remove_node(self, node):
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node

    def _move_to_front(self, node):
        self._remove_node(node)
        self._add_node(node)

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._move_to_front(node)
            return node.value
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._move_to_front(node)
        else:
            new_node = Node(key, value)
            self.cache[key] = new_node
            self._add_node(new_node)

            if len(self.cache) > self.capacity:
                lru_node = self.tail.prev
                self._remove_node(lru_node)
                del self.cache[lru_node.key]
```
