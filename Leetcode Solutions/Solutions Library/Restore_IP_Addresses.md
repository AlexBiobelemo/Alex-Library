## Restore IP Addresses
**Language:** python
**Tags:** python,backtracking,recursion,string manipulation

### Description:


---

### 1. Overview & Intent

The code aims to find all possible valid IPv4 addresses that can be formed by inserting three dots into a given string `s` containing only digits. An IPv4 address consists of four segments, each of which must be an integer between 0 and 255, inclusive, and must not have leading zeros (e.g., "01" is invalid, but "0" is valid).

### 2. How It Works

The solution employs a **backtracking** algorithm to explore all possible ways to partition the input string `s` into four valid segments.

*   **Initialization**: It first performs a quick check for valid input string length (`n` must be between 4 and 12, inclusive, to form a 4-part IP). It initializes an empty `result` list to store valid IP addresses and `current_ip_parts` to build an IP during recursion.
*   **`is_valid_segment(segment_str)`**: A helper function checks if a given string segment is valid:
    *   It prevents leading zeros (e.g., "01") unless the segment itself is "0".
    *   It converts the segment to an integer and checks if it falls within the range `0 <= num <= 255`.
*   **`backtrack(index, parts_count)`**: This recursive function explores partitions:
    *   `index`: The starting index in the original string `s` for the current segment.
    *   `parts_count`: The number of IP segments already found.
    *   **Base Cases**:
        *   If `parts_count == 4`: If `index == n` (the entire string has been consumed), a valid IP is formed, so it's joined by dots and added to `result`. Otherwise, this path is invalid.
        *   If `index == n`: The string is fully consumed, but not all 4 parts were found, so this path is invalid.
    *   **Pruning (Optimization)**: Before trying to form segments, it checks if it's even possible to form the remaining `(4 - parts_count)` segments using the `(n - index)` remaining characters. Each segment must be 1 to 3 characters long. If `remaining_chars` is less than `remaining_parts` or greater than `remaining_parts * 3`, this path cannot lead to a valid IP, so it returns early.
    *   **Recursive Step**: It iterates through possible segment lengths (1, 2, or 3 characters). For each `segment`:
        1.  It extracts the `segment` from `s`.
        2.  If `is_valid_segment(segment)` is true:
            *   The `segment` is added to `current_ip_parts`.
            *   `backtrack` is called recursively with the new `end_index` and `parts_count + 1`.
            *   The `segment` is removed (`pop()`) from `current_ip_parts` (backtracking step) to explore other possibilities.

### 3. Key Design Decisions

*   **Backtracking**: This is a classic approach for combinatorial problems where all possible solutions need to be found by systematically trying and undoing choices. It's well-suited for partitioning a string.
*   **Helper Function `is_valid_segment`**: Encapsulating the segment validation logic into a separate function improves readability and modularity.
*   **`current_ip_parts` List**: Using a list to build the current IP address parts simplifies appending and popping elements during the recursive process.
*   **Early Pruning**: The checks `if n < 4 or n > 12` and `if remaining_chars < remaining_parts or remaining_chars > remaining_parts * 3` significantly reduce the search space by eliminating branches that cannot possibly lead to a valid solution.

### 4. Complexity

*   **Time Complexity**:
    *   The depth of the recursion is fixed at 4 (for the four IP segments).
    *   At each level, we try up to 3 possible segment lengths (1, 2, or 3 characters).
    *   String slicing (`s[index : end_index]`) takes O(1) as the segment length is max 3.
    *   `is_valid_segment` also takes O(1) as it operates on a max 3-char string.
    *   Appending/popping from `current_ip_parts` is O(1).
    *   The final `result.append(".".join(current_ip_parts))` involves joining 4 small strings, which takes O(N) where N is the length of `s`.
    *   In the worst case, without pruning, there are roughly `3^4 = 81` paths to explore. With pruning and given `N <= 12`, the actual number of paths explored is much smaller.
    *   Since N is small (max 12), the overall complexity is dominated by the constant factor of recursive calls and string joining. We can consider it to be effectively **O(N)**, or more precisely **O(k * N)** where `k` is a small constant representing the number of valid paths explored, which for `N=12` is practically constant.
*   **Space Complexity**:
    *   The recursion depth is 4, leading to **O(1)** stack space.
    *   `current_ip_parts` stores at most 4 string segments, also **O(1)**.
    *   The `result` list stores all valid IP addresses. In the worst case, for `N=12`, there can be up to around 20-30 valid IP addresses. Each IP string has a length of `N + 3` (for dots). So, the space for `result` is **O(M * N)**, where `M` is the number of valid IP addresses found. Given `N <= 12`, this is practically **O(1)**.

### 5. Edge Cases & Correctness

*   **Input string length**: Correctly handles `n < 4` or `n > 12` by returning an empty list, as no valid IP can be formed.
*   **Leading zeros**: `is_valid_segment` correctly identifies "01", "00", "012" as invalid segments but "0" as valid.
*   **Numbers out of range**: `is_valid_segment` correctly validates `0 <= num <= 255`.
*   **String not fully consumed**: The check `if index == n` within the `parts_count == 4` block ensures that an IP is only added if all characters from the input string `s` have been used.
*   **String fully consumed but not enough parts**: The `if index == n` check immediately after the `parts_count == 4` block handles cases where the string ends prematurely before 4 segments are formed.
*   **Segment length constraints**: The `for length in range(1, 4)` loop ensures segments are 1 to 3 characters long. The pruning `remaining_chars` also helps manage this.
*   **Empty string**: Handled by `n < 4`.

The logic appears robust for all specified constraints and edge cases.

### 6. Improvements & Alternatives

*   **Readability**: The code is already quite clear and well-structured. No significant readability improvements are immediately necessary. Adding a comment to the pruning condition might be helpful for some:
    ```python
    # Pruning: Check if remaining characters can form the remaining parts (each 1-3 chars)
    if remaining_chars < remaining_parts or remaining_chars > remaining_parts * 3:
        return
    ```
*   **Alternative Approaches**:
    *   **Iterative Solution**: While possible using multiple nested loops, a recursive backtracking approach is generally more intuitive and easier to implement for this type of problem.
    *   **Dynamic Programming**: Could potentially be formulated, but the fixed small number of parts (4) makes simple backtracking very efficient and less complex than DP.

### 7. Security/Performance Notes

*   **Security**: No direct security vulnerabilities are apparent in this code. It deals purely with string manipulation and numerical validation. It does not interact with external systems or sensitive data.
*   **Performance**: For the given constraints (input string length `N` up to 12), the current backtracking solution with its effective pruning is highly performant. The theoretical worst-case constant factor is small, leading to near-instantaneous execution. There are no obvious performance bottlenecks that would warrant significant optimization for typical use cases.

### Code:
```python
from typing import List

class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        n = len(s)
        if n < 4 or n > 12:
            return []

        result = []
        current_ip_parts = []

        def is_valid_segment(segment_str: str) -> bool:
            if len(segment_str) > 1 and segment_str[0] == '0':
                return False
            
            num = int(segment_str)
            return 0 <= num <= 255

        def backtrack(index: int, parts_count: int):
            if parts_count == 4:
                if index == n:
                    result.append(".".join(current_ip_parts))
                return

            if index == n:
                return

            remaining_chars = n - index
            remaining_parts = 4 - parts_count
            if remaining_chars < remaining_parts or remaining_chars > remaining_parts * 3:
                return

            for length in range(1, 4):
                end_index = index + length
                if end_index > n:
                    break

                segment = s[index : end_index]
                if is_valid_segment(segment):
                    current_ip_parts.append(segment)
                    backtrack(end_index, parts_count + 1)
                    current_ip_parts.pop()

        backtrack(0, 0)
        return result
```
