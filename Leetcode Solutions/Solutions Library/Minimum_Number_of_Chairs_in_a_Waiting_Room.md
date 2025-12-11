## Minimum Number of Chairs in a Waiting Room
**Language:** python
**Tags:** python,oop,simulation,string

### Description:
This code snippet efficiently calculates the minimum number of chairs required to accommodate all people based on a sequence of entry ('E') and leave ('L') events.

### 1. Overview & Intent

*   **What it does:** The function `minimumChairs` determines the maximum number of people who are simultaneously present in a room given a string `s` representing a timeline of events. This peak number directly corresponds to the minimum chairs needed.
*   **Why:** To solve a common resource allocation problem where you need to provision just enough resources (chairs) to handle peak demand, without over-provisioning.

### 2. How It Works

1.  **Initialization:**
    *   `current_occupancy`: Starts at 0, representing an empty room.
    *   `max_chairs_needed`: Starts at 0, as no chairs are needed initially.
2.  **Event Processing:**
    *   It iterates through each `event` character in the input string `s`.
    *   If an 'E' (Entry) event occurs, `current_occupancy` is incremented.
    *   If an 'L' (Leave) event occurs, `current_occupancy` is decremented.
3.  **Tracking Peak Demand:**
    *   After processing each event, `max_chairs_needed` is updated to be the maximum of its current value and the `current_occupancy`. This ensures `max_chairs_needed` always holds the highest number of people observed in the room at any single point in time.
4.  **Result:**
    *   Once all events in `s` have been processed, `max_chairs_needed` contains the overall peak occupancy, which is returned as the minimum chairs required.

### 3. Key Design Decisions

*   **Data Structures:**
    *   **Input `s` (string):** Represents the sequence of events.
    *   **`current_occupancy` (integer):** A simple counter, chosen for its efficiency in tracking real-time changes.
    *   **`max_chairs_needed` (integer):** Another simple counter, chosen for its efficiency in tracking the highest value observed.
*   **Algorithm:**
    *   **Single-Pass Iteration:** The core design is a single pass through the input string. This is chosen for its optimal time complexity, as each event needs to be considered exactly once.
*   **Trade-offs:**
    *   **Simplicity vs. Complexity:** The chosen approach is extremely simple, readable, and directly maps to the problem's logic, making it highly maintainable.
    *   **Efficiency:** It prioritizes efficiency in both time and space by using minimal variables and avoiding complex operations.

### 4. Complexity

*   **Time Complexity: O(N)**
    *   Where N is the length of the input string `s`.
    *   The algorithm iterates through the string exactly once. Each operation inside the loop (character comparison, arithmetic operation, `max` function) takes constant time, O(1).
*   **Space Complexity: O(1)**
    *   The algorithm uses a fixed number of variables (`current_occupancy`, `max_chairs_needed`, and the loop variable `event`), regardless of the input string's length.

### 5. Edge Cases & Correctness

*   **Empty String (`s = ""`)**:
    *   The loop won't execute. `current_occupancy` and `max_chairs_needed` remain 0.
    *   Returns 0. **Correct** (no events means no chairs needed).
*   **String with only 'E' events (`s = "EEE"`)**:
    *   `current_occupancy` will sequentially become 1, 2, 3.
    *   `max_chairs_needed` will track this, becoming 1, then 2, then 3.
    *   Returns 3. **Correct**.
*   **String with only 'L' events (`s = "LLL"`)**:
    *   `current_occupancy` will sequentially become -1, -2, -3.
    *   `max_chairs_needed` will remain 0 because `max(0, -1)`, `max(0, -2)`, etc., will always result in 0 (you can't need negative chairs).
    *   Returns 0. **Correct** (no entries mean no chairs required).
*   **More 'L' events than 'E' events, leading to negative `current_occupancy` (`s = "LE"`)**:
    *   'L': `current_occupancy` becomes -1, `max_chairs_needed` remains 0.
    *   'E': `current_occupancy` becomes 0, `max_chairs_needed` remains 0.
    *   Returns 0. **Correct** (no positive peak occupancy occurred).
*   **Mix of 'E' and 'L' events (`s = "EELLE"`)**:
    *   E: occ=1, max=1
    *   E: occ=2, max=2
    *   L: occ=1, max=2
    *   L: occ=0, max=2
    *   E: occ=1, max=2
    *   Returns 2. **Correct**.

### 6. Improvements & Alternatives

*   **Input Validation (Robustness):**
    *   The current code implicitly ignores any character in `s` that is not 'E' or 'L'. While often acceptable in competitive programming, in a production setting, it's safer to explicitly handle unexpected characters (e.g., raise an error, log a warning, or define specific ignore behavior).
    ```python
    for event in s:
        if event == 'E':
            current_occupancy += 1
        elif event == 'L':
            current_occupancy -= 1
        else:
            # Raise an error for invalid input, or log/ignore based on spec
            raise ValueError(f"Invalid event type encountered: '{event}'. Expected 'E' or 'L'.")
    ```
*   **Readability:** The code is already highly readable and uses clear variable names. No significant readability improvements are necessary.
*   **Alternative Approaches:** For this specific problem, the current single-pass, counter-based approach is optimal in both time and space complexity. More complex alternatives (e.g., using a stack to track pending entries/exits) would be less efficient and add unnecessary overhead.

### 7. Security/Performance Notes

*   **Security:** Not applicable. This code does not handle sensitive data, external inputs affecting system integrity, or network communications.
*   **Performance:** The code is exceptionally performant. It operates in linear time with constant space, which is the best possible complexity for this problem, as every event must be examined at least once. It avoids any performance pitfalls like redundant computations, excessive memory allocations, or inefficient data structures.

### Code:
```python
class Solution:
    def minimumChairs(self, s: str) -> int:
        current_occupancy = 0
        max_chairs_needed = 0

        for event in s:
            if event == 'E':
                current_occupancy += 1
            elif event == 'L':
                current_occupancy -= 1
            
            max_chairs_needed = max(max_chairs_needed, current_occupancy)
            
        return max_chairs_needed
```
