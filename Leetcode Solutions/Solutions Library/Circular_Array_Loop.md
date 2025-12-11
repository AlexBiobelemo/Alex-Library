## Circular Array Loop
**Language:** python
**Tags:** python,cycle detection,two-pointers,graph

### Description:
This code aims to solve the "Circular Array Loop" problem. It's a well-known variation of cycle detection in graphs, specifically within an array where elements dictate jumps.

---

### 1. Overview & Intent

The primary goal of this code is to determine if a given circular array `nums` contains a "loop" (cycle) that satisfies specific conditions:
*   The loop must consist of more than one element (length `k > 1`).
*   All elements within the loop must point in the same direction (either all positive jumps or all negative jumps).
*   The array is circular, meaning movement wraps around from `n-1` to `0` and vice-versa.

The code iterates through the array, attempting to find such a loop starting from each unvisited index.

---

### 2. How It Works

The algorithm uses a modified version of Floyd's Tortoise and Hare (slow and fast pointer) cycle-detection algorithm combined with path compression.

1.  **Outer Loop (`for i in range(n)`):**
    *   It iterates through each possible starting point `i` in the array.
    *   If `nums[i]` is `0`, it means this index has already been visited and determined not to be part of a valid cycle, so it's skipped.

2.  **`get_next(current_idx, is_forward)` Helper Function:**
    *   This function calculates the next index in the sequence from `current_idx`.
    *   It performs several crucial validation checks:
        *   If `nums[current_idx]` is `0` (already marked invalid/visited), return `-1`.
        *   **Direction Consistency (current node):** Checks if the jump `nums[current_idx]` aligns with the `is_forward` direction established for the current path (all positive or all negative). If not, return `-1`.
        *   Calculates `next_idx = (current_idx + nums[current_idx]) % n`.
        *   **Self-Loop Check (`k > 1`):** If `next_idx` is the same as `current_idx`, it's a loop of length 1, which is invalid, so return `-1`.
        *   **Direction Consistency (next node):** Checks if the jump `nums[next_idx]` also aligns with `is_forward`. If not, return `-1`. (A critical refinement for robustness would be to also check `nums[next_idx] == 0` here).
    *   If all checks pass, it returns `next_idx`.

3.  **Cycle Detection (Floyd's Algorithm):**
    *   For each starting `i`, it initializes `slow = i` and `fast = i`.
    *   It determines the initial `is_forward` direction based on `nums[i]`.
    *   The `while True` loop moves `slow` one step and `fast` two steps using `get_next`.
    *   If any call to `get_next` returns `-1`, it means the path from `i` is invalid or leads to a dead end, so the loop breaks.
    *   If `slow == fast` (and neither is `-1`), a cycle has been detected, and `True` is returned.

4.  **Path Compression / Invalidation:**
    *   If the inner `while` loop completes without finding a cycle (i.e., it broke due to an invalid path or dead end), it marks all nodes visited in that path as `0`.
    *   This prevents redundant processing of these nodes in subsequent outer loop iterations. It iterates from `curr = i` along the path until it hits a node that's already `0` or encounters another invalid condition (self-loop, direction mismatch, or reaches a marked invalid node).

5.  If the outer loop finishes without finding any valid cycle, the function returns `False`.

---

### 3. Key Design Decisions

*   **Floyd's Tortoise and Hare Algorithm:** This is the standard, efficient algorithm for cycle detection in linked lists or sequences, adapted here for array indices. It offers O(N) time complexity for cycle detection.
*   **In-Place Path Compression (`nums[node] = 0`):**
    *   **Pros:** Achieves O(1) auxiliary space complexity by modifying the input array. Avoids re-processing paths that are known not to lead to a valid cycle.
    *   **Cons:** Modifies the input array, which might be undesirable in some contexts. Assumes `0` is not a valid jump value in the problem (which is true here, as jumps must be positive or negative).
*   **`get_next` Helper Function:** Encapsulates the logic for calculating the next index and applying all cycle validity constraints (direction, length > 1, visited status), making the main cycle detection loop cleaner and more readable.
*   **Direction Flag (`is_forward`):** Explicitly tracks the required direction for the current path, ensuring all jumps in a potential cycle conform to the "all positive" or "all negative" rule.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   Each index `i` in the outer loop is considered as a starting point.
    *   During cycle detection (`while True`), `slow` and `fast` pointers traverse nodes. Each node can be visited by `get_next` at most a constant number of times (as part of a slow/fast traversal or path compression).
    *   When a path doesn't form a cycle, all nodes in that path are marked `0`. Once a node is `0`, it's skipped by future outer loop iterations.
    *   Therefore, each node is effectively processed and marked `0` at most once across all iterations.
    *   `get_next` is O(1).
    *   Overall, the total work is proportional to the number of nodes, making it O(N).
*   **Space Complexity: O(1)**
    *   The algorithm modifies the input array in-place.
    *   It uses only a few constant-space variables (pointers, flags).

---

### 5. Edge Cases & Correctness

*   **Empty Array (`n=0`):** Python's `len(nums)` would be 0, and `range(n)` would not execute. The code would correctly return `False`. (Problem constraints usually guarantee `n >= 1`).
*   **Single Element Array (`n=1`):**
    *   If `nums = [1]`, `next_idx = (0 + 1) % 1 = 0`. `next_idx == current_idx` check in `get_next` returns `-1`. Correctly handles `k > 1` rule.
*   **Cycles of length 1 (`k=1`):** Explicitly caught by `if next_idx == current_idx: return -1` in `get_next`. Correct.
*   **Mixed Direction Jumps:** The `(val > 0) != is_forward` checks (for current and next nodes) ensure that all jumps in a path maintain consistent direction. If a jump breaks this, `get_next` returns `-1`, invalidating the path. Correct.
*   **Paths Leading to `0` (Invalidated Nodes):** The `if nums[current_idx] == 0: return -1` in `get_next` handles cases where a pointer encounters an already-marked invalid node. The path compression also correctly stops if it hits a `0`.
*   **No Cycles:** The outer loop finishes, and `False` is returned.

---

### 6. Improvements & Alternatives

*   **Robustness in `get_next` for `nums[next_idx] == 0`:**
    The current `get_next` has this comment:
    `# If nums[next_idx] is 0, it means it was already processed and marked invalid, so this path is invalid.`
    `# This check is implicitly handled by the `if nums[current_idx] == 0` at the beginning if `next_idx` becomes the `current_idx` in a subsequent call.`
    While true, it's more robust to explicitly check `if nums[next_idx] == 0: return -1` *before* the `(nums[next_idx] > 0) != is_forward` check. Otherwise, if `is_forward` is `False` (negative jumps), `(0 > 0) != False` evaluates to `False != False` which is `False`, allowing `0` to seemingly match the 'negative' direction. This is a subtle bug where a `0` might not immediately return `-1` if it's the *next* node and `is_forward` is `False`.

    **Proposed `get_next` refinement:**
    ```python
            # ... (previous checks) ...
            if next_idx == current_idx:
                return -1

            # Explicitly check for 0 before direction consistency for the next node
            if nums[next_idx] == 0: # If the next node is already marked invalid
                return -1
            
            if (nums[next_idx] > 0) != is_forward: # Direction mismatch for the *next* node's value
                return -1
            return next_idx
    ```

*   **Alternative Marking:** If modifying the input `nums` array is not allowed or if `0` is a valid jump value (not in this problem), a separate `visited` boolean array (O(N) space) or a specific non-`0` sentinel value could be used for marking.
*   **Readability of `while True` loop:** The multiple `if ... break` statements inside the `while True` can sometimes be slightly hard to follow. However, for a standard Floyd's algorithm with early exit conditions, this pattern is common and generally acceptable.

---

### 7. Security/Performance Notes

*   **Security:** This algorithm has no direct security implications as it's purely a computational problem on an integer array.
*   **Performance:**
    *   The O(N) time and O(1) space complexity are optimal for this problem.
    *   The use of modulo (`% n`) and constant-time array lookups are efficient.
    *   The in-place modification for path compression significantly improves practical performance by avoiding redundant calculations.

### Code:
```python
class Solution:
    def circularArrayLoop(self, nums: List[int]) -> bool:
        n = len(nums)

        def get_next(current_idx, is_forward):
            # If nums[current_idx] is 0, it means this node has been visited and
            # determined not to be part of a valid cycle, or it's a dead end.
            # So, any path going through it is invalid.
            if nums[current_idx] == 0:
                return -1

            val = nums[current_idx]
            
            # Check for direction consistency for the current node's value
            # All elements in a cycle must be either all positive or all negative.
            if (val > 0) != is_forward:
                return -1 # Direction mismatch

            next_idx = (current_idx + val) % n
            
            # Check for self-loop (k > 1)
            # If next_idx is the same as current_idx, it's a cycle of length 1, which is not allowed.
            if next_idx == current_idx:
                return -1 

            # Check for direction consistency for the *next* node's value.
            # This is crucial for the "Every nums[seq[j]] is either all positive or all negative" rule.
            # If nums[next_idx] is 0, it means it was already processed and marked invalid, so this path is invalid.
            # This check is implicitly handled by the `if nums[current_idx] == 0` at the beginning if `next_idx`
            # becomes the `current_idx` in a subsequent call.
            # However, it's safer to check it explicitly here for the *next* step.
            if (nums[next_idx] > 0) != is_forward:
                return -1 # Direction mismatch for the next step

            return next_idx

        for i in range(n):
            if nums[i] == 0: # Already visited or invalid path
                continue

            # Slow and fast pointers for cycle detection
            slow = i
            fast = i
            is_forward = nums[i] > 0 # Determine the direction for this path

            while True:
                slow = get_next(slow, is_forward)
                if slow == -1: # Invalid path or dead end
                    break

                fast = get_next(fast, is_forward)
                if fast == -1: # Invalid path or dead end
                    break
                
                fast = get_next(fast, is_forward) # Fast pointer moves twice
                if fast == -1: # Invalid path or dead end
                    break

                if slow == fast:
                    return True # Cycle found

            # If no cycle was found from this path (or it was invalid),
            # mark all nodes in this path as 0 to avoid re-processing them.
            # This is a path compression step.
            curr = i
            while True:
                # If nums[curr] is already 0, it means we've reached a node
                # that was part of a previously processed (and failed) path.
                # Or, it's the current starting node `i` which was just marked 0.
                if nums[curr] == 0:
                    break
                
                original_val_at_curr = nums[curr] # Store original value before marking
                nums[curr] = 0 # Mark current node as visited/invalidated

                next_node_temp = (curr + original_val_at_curr) % n
                
                # Conditions to stop traversing and marking:
                # 1. Self-loop (k=1)
                # 2. Next node is already marked 0 (part of another invalid path)
                # 3. Direction mismatch for the next node (path becomes invalid)
                # Note: The order of checks matters. `nums[next_node_temp] == 0` should be checked
                # before `(nums[next_node_temp] > 0) != is_forward` to avoid issues if `nums[next_node_temp]` is 0.
                if next_node_temp == curr or \
                   nums[next_node_temp] == 0 or \
                   (nums[next_node_temp] > 0) != is_forward:
                    break
                
                curr = next_node_temp
        
        return False
```
