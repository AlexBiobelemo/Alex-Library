# Exported Snippets from Project Sophia

---

## Check Distances Between Same Letters
**Language:** python
**Tags:** python,oop,array,string manipulation
**Collection:** Easy
**Created At:** 2025-12-13 22:28:30

### Description:
### 1. Overview & Intent

This code snippet checks if the "distance" between the first and second occurrences of each character in a given string `s` matches a predefined set of expected distances.

*   **Input:**
    *   `s`: A string, assumed to contain lowercase English letters.
    *   `distance`: An array (or list) of 26 integers, where `distance[i]` represents the *expected* number of characters between the first and second occurrence of the `i`-th letter of the alphabet (e.g., `distance[0]` for 'a', `distance[1]` for 'b', etc.).
*   **Output:** `True` if all characters appearing at least twice in `s` have their first-to-second occurrence distance matching the `distance` array; `False` otherwise. Characters appearing only once are implicitly considered valid.

### 2. How It Works

The algorithm iterates through the input string `s` character by character, keeping track of the first index where each character is encountered.

1.  **Initialization:** An array `first_occurrence` of size 26 is created, initialized with `-1`. Each index corresponds to a lowercase letter (0 for 'a', 1 for 'b', etc.). A value of `-1` signifies that the character at that index has not yet been seen.
2.  **Iteration:**
    *   For each character `char_s` at index `i` in the string `s`:
        *   It converts `char_s` into its corresponding alphabet index (`0-25`) by subtracting the ASCII value of 'a'.
        *   **First Encounter:** If `first_occurrence[char_idx]` is `-1`, it means this is the first time we've seen this character. The current index `i` is stored in `first_occurrence[char_idx]`.
        *   **Second (or later) Encounter:** If `first_occurrence[char_idx]` is not `-1`, it means we've seen this character before. This is the *second* (or later) occurrence that we are interested in.
            *   The `actual_distance` is calculated as `i - first_occurrence[char_idx] - 1`. This formula correctly counts the number of characters *between* the first and current occurrence.
            *   This `actual_distance` is then compared with `distance[char_idx]`.
            *   If they do not match, the function immediately returns `False`.
3.  **Completion:** If the loop finishes without returning `False`, it means all characters that appeared at least twice had their distances matched, so the function returns `True`.

### 3. Key Design Decisions

*   **`first_occurrence` Array (`[-1] * 26`):**
    *   **Purpose:** To efficiently store the index of the first appearance of each lowercase English letter.
    *   **Data Structure:** An array (list in Python) of fixed size 26 is chosen because the character set (lowercase English letters) is fixed, small, and maps directly to array indices (0-25). This provides `O(1)` access time.
    *   **Initialization (`-1`):** Using `-1` as a sentinel value is a common and effective way to indicate "not seen yet" for index-based storage, as valid indices are non-negative.
*   **Character to Index Mapping (`ord(char_s) - ord('a')`):** This is a standard and efficient way to convert a lowercase character to its 0-25 alphabet index.
*   **Early Exit (`return False`):** If a mismatch is found, there's no need to continue processing the rest of the string, improving average-case performance.
*   **Distance Calculation (`i - first_occurrence[char_idx] - 1`):** The `-1` is crucial here. If character 'a' is at index 0 and then again at index 2, the total span is `2 - 0 = 2`. The characters *between* them are `2 - 0 - 1 = 1`. This correctly calculates the number of intermediate characters.

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The code iterates through the input string `s` exactly once, where `N` is the length of `s`.
    *   All operations inside the loop (array access, arithmetic, character conversion) are constant time operations, `O(1)`.
    *   Therefore, the overall time complexity is linear with respect to the length of the string.
*   **Space Complexity: O(1)**
    *   The `first_occurrence` array has a fixed size of 26, independent of the input string's length `N`.
    *   Thus, the space complexity is constant.

### 5. Edge Cases & Correctness

The code handles several edge cases correctly:

*   **Empty String `s`:** The `for` loop will not execute, and the function will return `True`. This is correct, as an empty string has no characters to violate the distance condition.
*   **String with All Unique Characters:** The `if first_occurrence[char_idx] == -1` block will always execute, recording the first occurrence. The `else` block (for checking distances) will never be reached. The function correctly returns `True`.
*   **Characters Appearing Only Once:** Similar to the above, their distances are never checked, which is the expected behavior. There's no "second" occurrence to define a distance.
*   **Characters Appearing More Than Twice:** The code only considers the *first* and *second* occurrences for distance calculation. Subsequent occurrences of the same character (`third`, `fourth`, etc.) will still enter the `else` block, but the comparison will always be `i - first_occurrence[char_idx] - 1` against `distance[char_idx]`. This implicitly means we are checking the distance between the *first* and *second* occurrence relative to its *actual* first occurrence position. This is typically the desired interpretation for such problems unless explicitly stated otherwise.
*   **String Containing Only One Type of Character (e.g., "aaaaa"):**
    *   First 'a': `first_occurrence['a']` becomes 0.
    *   Second 'a' (at index 1): `actual_distance` is `1 - 0 - 1 = 0`. This is compared to `distance['a']`.
    *   Subsequent 'a's (at index 2, 3, etc.): `actual_distance` will be `2 - 0 - 1 = 1`, `3 - 0 - 1 = 2`, etc. These will be compared against `distance['a']`. *This implies the logic might be slightly different than strictly "first and second"* -- it's checking `i - first_occurrence[char_idx] - 1` for *every* occurrence after the first. However, the problem statement "distance between first and second" usually means *only* that pair. The current code checks `current_occurrence_index - first_occurrence_index - 1` for *every* occurrence after the first. If the problem indeed means "distance between first and *second*", the `else` block could be refined to `elif first_occurrence[char_idx] != -1 and second_occurrence[char_idx] == -1` and then update `second_occurrence[char_idx] = i`. But given the standard phrasing, the current code's interpretation is often what's intended: "if a character appears, and it's not its first appearance, then the distance from its *first* appearance to *this* appearance must match".
    *   *Correction to my thought process*: The common interpretation is that `distance[char_idx]` refers to the separation between the *first* and *any subsequent* occurrence that follows the `distance` rule. The code checks `i - first_occurrence[char_idx] - 1` *every time* it sees a character again. This is perfectly valid under the interpretation that the "distance" rule must hold for the *first valid pair* (which means the second occurrence relative to the first). If it's violated by the second occurrence, `False` is returned. If not, any *further* occurrences are implicitly ignored because the "distance" rule has already been satisfied (or failed) by the second occurrence. If the rule was meant to hold for *all* occurrences (e.g., first-to-second, second-to-third, etc.), the logic would be much more complex. So, for "first and second occurrence", the current code effectively checks only the distance between the *absolute first* and the *absolute second* occurrence, because if the *second* one fails, it returns `False`. If it passes, any subsequent ones are technically comparing against the *first* too, which isn't the "second" anymore, but this doesn't break the logic for the specific "first and second" rule.

### 6. Improvements & Alternatives

*   **Clarity on `distance` array's contents:** It would be good to explicitly state in problem comments what `distance[char_idx]` represents (e.g., "number of characters *between* the first and second occurrences").
*   **Robustness (Character Set):** If the input string `s` could contain characters other than lowercase English letters, the code would raise an `IndexError`. For broader applicability, one might:
    *   Add validation for `char_s` to ensure it's in 'a' through 'z'.
    *   Use a `collections.defaultdict(lambda: -1)` or a regular `dict` for `first_occurrence` if the character set is unknown or very sparse, though for fixed 26 chars, the array is more performant.
    *   Example using `dict`:
        ```python
        first_occurrence_map = {} # Stores char -> first_index

        for i, char_s in enumerate(s):
            if char_s not in first_occurrence_map:
                first_occurrence_map[char_s] = i
            else:
                actual_distance = i - first_occurrence_map[char_s] - 1
                char_idx = ord(char_s) - ord('a') # Still need this for distance array access
                if actual_distance != distance[char_idx]:
                    return False
        return True
        ```
        (Note: this alternative still needs `char_idx` for `distance` array access, so `dict` here doesn't entirely remove `ord` usage unless `distance` was also a `dict`). Given the fixed `distance` array, the original array approach for `first_occurrence` is optimal.
*   **Docstrings/Comments:** A brief docstring explaining the function's purpose, parameters, and return value would enhance readability, especially for a complete function.

### 7. Security/Performance Notes

*   **Performance:** The code is highly performant with O(N) time and O(1) space complexity. No obvious performance bottlenecks exist. Python's list and string operations are well-optimized.
*   **Security:** This particular code snippet does not deal with external input directly, file I/O, network communication, or sensitive data handling. Thus, there are no immediate security concerns related to typical vulnerabilities like injection, data exposure, or authentication flaws. It's a pure algorithmic logic.

### Code:
```python
class Solution:
    def checkDistances(self, s: str, distance: List[int]) -> bool:
        first_occurrence = [-1] * 26

        for i, char_s in enumerate(s):
            char_idx = ord(char_s) - ord('a') 

            if first_occurrence[char_idx] == -1:
                first_occurrence[char_idx] = i
            else:
                actual_distance = i - first_occurrence[char_idx] - 1
                
                if actual_distance != distance[char_idx]:
                    return False 
        
        return True
```

---