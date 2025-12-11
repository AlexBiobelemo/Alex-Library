## Number of Adjacent Elements with the Same Color
**Language:** python
**Tags:** python,oop,array,simulation

### Description:
This code snippet defines a method `colorTheArray` that processes a series of color updates on an array and tracks the number of adjacent elements with identical non-zero colors.

## 1. Overview & Intent

*   **What it does:** The function takes an initial array size `n` and a list of `queries`. Each query specifies an `index` and a new `color` for that position in the array. After each color update, it calculates and records the total number of adjacent pairs in the array that have the same (non-zero) color.
*   **Why it's needed:** This pattern is common in problems requiring dynamic state updates and real-time tracking of a derived property. Instead of recalculating the entire property from scratch after each change, it efficiently updates the count based only on the immediate impact of the change.

## 2. How It Works

The algorithm maintains an array `colors` representing the current state and a counter `current_pairs` for adjacent identical-color pairs.

1.  **Initialization:**
    *   An array `colors` of size `n` is created, initialized with zeros. Zero typically represents an uncolored state or a color that doesn't form pairs.
    *   `current_pairs` is set to `0`.
    *   An `answer` list is initialized to store the `current_pairs` count after each query.

2.  **Processing Queries:**
    For each query `(index, color)`:
    *   **Store `old_color`:** The color currently at `colors[index]` is stored as `old_color`.
    *   **Decrement for `old_color`:**
        *   It checks the left neighbor (`index - 1`): If `colors[index - 1]` was equal to `old_color` AND `old_color` was not `0`, it means the pair `(index - 1, index)` is about to be broken. `current_pairs` is decremented.
        *   It checks the right neighbor (`index + 1`): Similarly, if `colors[index + 1]` was equal to `old_color` AND `old_color` was not `0`, the pair `(index, index + 1)` is about to be broken. `current_pairs` is decremented.
    *   **Update `colors[index]`:** The element at `index` is updated to the new `color`.
    *   **Increment for `new_color`:**
        *   It checks the left neighbor (`index - 1`): If `colors[index - 1]` (after update) is now equal to the new `color` AND `color` is not `0`, a new pair `(index - 1, index)` is formed. `current_pairs` is incremented.
        *   It checks the right neighbor (`index + 1`): Similarly, if `colors[index + 1]` (after update) is now equal to the new `color` AND `color` is not `0`, a new pair `(index, index + 1)` is formed. `current_pairs` is incremented.
    *   **Record Result:** The updated `current_pairs` value is appended to the `answer` list.

3.  **Return:** The `answer` list containing the pair counts after each query.

## 3. Key Design Decisions

*   **Data Structures:**
    *   `colors`: A Python list is used to store the colors of `n` elements. This provides `O(1)` access time by index, which is crucial for efficient neighbor checks.
    *   `current_pairs`: An integer counter is used to keep track of the total pairs. This allows for `O(1)` updates.
    *   `answer`: Another Python list stores the results for each query. Appending to a list is amortized `O(1)`.
*   **Algorithm (Incremental Updates):**
    *   The core design decision is to use an incremental update strategy rather than recalculating the entire `current_pairs` count from scratch after each query.
    *   By only considering the immediate left and right neighbors of the updated `index`, the algorithm performs a constant amount of work per query. This is highly efficient for a large number of queries.
*   **Conditions for Pair Formation:** The explicit checks `old_color != 0` and `color != 0` are vital. They ensure that only non-zero colors contribute to forming valid pairs, aligning with typical problem definitions where '0' might signify an 'empty' or 'uncolored' state.
*   **Boundary Checks:** The `index > 0` and `index < n - 1` conditions correctly handle array boundaries, preventing `IndexError` when checking neighbors at the ends of the array.

## 4. Complexity

Let `N` be the size of the array and `Q` be the number of queries.

*   **Time Complexity:**
    *   **Initialization:** Creating `colors` array takes `O(N)` time. Other initializations are `O(1)`.
    *   **Per Query:** Each query involves a fixed number of array accesses, comparisons, and arithmetic operations (at most 4 neighbor checks, 1 color update, 1 list append). This is `O(1)` time per query.
    *   **Total Time Complexity:** `O(N + Q)`.
*   **Space Complexity:**
    *   `colors` array: `O(N)` space.
    *   `answer` list: `O(Q)` space to store the results of all queries.
    *   **Total Space Complexity:** `O(N + Q)`.

## 5. Edge Cases & Correctness

The code handles several critical edge cases correctly:

*   **Single Element Array (`n = 1`):**
    *   `index > 0` and `index < n - 1` conditions correctly evaluate to `False`, meaning no neighbor checks are performed. `current_pairs` will always remain `0`, which is correct as a single element cannot form an adjacent pair.
*   **Updating at Array Boundaries (`index = 0` or `index = n - 1`):**
    *   The `index > 0` and `index < n - 1` checks correctly ensure that only valid neighbors are accessed. For `index = 0`, only the right neighbor is considered. For `index = n - 1`, only the left neighbor is considered.
*   **Color `0`:**
    *   The explicit checks `old_color != 0` and `color != 0` are crucial.
    *   If a position changes from a non-zero color to `0`, `current_pairs` is correctly decremented for broken pairs, but not incremented for any new pairs (as `0` doesn't form pairs).
    *   If a position changes from `0` to a non-zero color, `current_pairs` is correctly incremented for new pairs, but not decremented for any (non-existent) broken pairs.
    *   If a position changes from `0` to `0`, `current_pairs` remains unchanged.
*   **No Change in Color (`old_color == color`):**
    *   If the new color is the same as the old non-zero color, the count will be decremented for the `old_color` and then immediately incremented for the `new_color`, resulting in no net change to `current_pairs`, which is correct.

## 6. Improvements & Alternatives

*   **Readability with Helper Function:**
    The logic for checking neighbors and adjusting `current_pairs` is repeated four times. A helper function could encapsulate this, improving readability and reducing potential for errors.

    ```python
    class Solution:
        def colorTheArray(self, n: int, queries: List[List[int]]) -> List[int]:
            colors = [0] * n
            current_pairs = 0
            answer = []

            # Helper function to calculate contribution of a potential pair
            def get_pair_contribution(arr, idx1, idx2):
                if not (0 <= idx1 < n and 0 <= idx2 < n):
                    return 0
                if arr[idx1] == arr[idx2] and arr[idx1] != 0:
                    return 1
                return 0

            for index, color in queries:
                old_color = colors[index]

                # Remove contributions from old color
                current_pairs -= get_pair_contribution(colors, index, index - 1)
                current_pairs -= get_pair_contribution(colors, index, index + 1)
                
                # Update the color
                colors[index] = color
                
                # Add contributions from new color
                current_pairs += get_pair_contribution(colors, index, index - 1)
                current_pairs += get_pair_contribution(colors, index, index + 1)
                
                answer.append(current_pairs)
            
            return answer
    ```
    This refactoring makes the main loop cleaner and the logic for pair checking more robust and reusable.
*   **Alternative for `get_pair_contribution`:** Instead of passing the whole array, you could pass specific colors or just the `index` and the neighbor `index` and access the global `colors` list inside the helper. The example above is a good balance.
*   **Performance:** The current solution is already optimal in terms of time complexity per query (`O(1)`). No significant performance improvements are possible within the current problem structure.

## 7. Security/Performance Notes

*   **Performance:** The code is highly efficient, achieving `O(1)` work per query after initial setup. This makes it suitable for scenarios with many queries (`Q`) on a potentially large array (`N`).
*   **Security:** This code is purely algorithmic and operates on internal data structures. It does not interact with external systems, files, databases, or user-provided input in a way that would introduce common security vulnerabilities (e.g., injection, cross-site scripting, authentication issues). As long as `n` and `queries` conform to expected types and bounds (which is typically guaranteed by problem statements), there are no inherent security risks. Invalid indices in `queries` (outside `0` to `n-1`) would cause an `IndexError`, which is a robustness issue rather than a security flaw.

### Code:
```python
class Solution:
    def colorTheArray(self, n: int, queries: List[List[int]]) -> List[int]:
        colors = [0] * n
        current_pairs = 0
        answer = []

        for index, color in queries:
            old_color = colors[index]

            # Decrement count for pairs involving old_color at index
            # Check left neighbor
            if index > 0:
                # If the left neighbor formed a pair with the old color at `index`
                # and the old color was a valid (non-zero) color, decrement count.
                if colors[index - 1] == old_color and old_color != 0:
                    current_pairs -= 1
            # Check right neighbor
            if index < n - 1:
                # If the right neighbor formed a pair with the old color at `index`
                # and the old color was a valid (non-zero) color, decrement count.
                if colors[index + 1] == old_color and old_color != 0:
                    current_pairs -= 1
            
            # Update the color at the given index
            colors[index] = color

            # Increment count for pairs involving new_color at index
            # Check left neighbor
            if index > 0:
                # If the left neighbor now forms a pair with the new color at `index`
                # and the new color is a valid (non-zero) color, increment count.
                if colors[index - 1] == color and color != 0:
                    current_pairs += 1
            # Check right neighbor
            if index < n - 1:
                # If the right neighbor now forms a pair with the new color at `index`
                # and the new color is a valid (non-zero) color, increment count.
                if colors[index + 1] == color and color != 0:
                    current_pairs += 1
            
            # Record the current number of adjacent pairs
            answer.append(current_pairs)
        
        return answer
```
