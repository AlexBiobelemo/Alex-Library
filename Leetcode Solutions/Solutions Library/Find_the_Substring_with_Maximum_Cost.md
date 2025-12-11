## Find the Substring with Maximum Cost
**Language:** python
**Tags:** python,oop,kadane's algorithm,hashmap

### Description:
---

### 1. Overview & Intent

This code defines a method `maximumCostSubstring` within a `Solution` class.
The primary intent is to calculate the maximum "cost" of any contiguous substring within a given input string `s`.

*   **Input `s`**: The main string to analyze.
*   **Input `chars`**: A string containing characters whose default cost should be overridden.
*   **Input `vals`**: A list of integers, where `vals[i]` is the new cost for `chars[i]`.
*   **Default Cost**: By default, each lowercase English letter 'a' through 'z' has a cost equal to its 1-indexed position in the alphabet (e.g., 'a' = 1, 'b' = 2, ..., 'z' = 26).
*   **Output**: An integer representing the highest possible sum of character costs for any contiguous substring of `s`. If all possible substring sums are negative, the result should be 0 (representing an empty substring).

---

### 2. How It Works

The method operates in three distinct phases:

1.  **Initialize Default Character Values**:
    *   A dictionary `char_val_map` is created.
    *   It's populated with default values for all lowercase English letters 'a' through 'z'. 'a' gets a value of 1, 'b' gets 2, and so on, up to 'z' getting 26.

2.  **Override Custom Character Values**:
    *   The code iterates through the `chars` string and `vals` list.
    *   For each character `chars[i]`, its value in `char_val_map` is updated to `vals[i]`, overriding any default value.

3.  **Calculate Maximum Substring Cost (Kadane's Algorithm)**:
    *   The core logic uses Kadane's algorithm, a dynamic programming approach, to find the maximum sum of a contiguous subarray (in this case, a substring).
    *   It maintains two variables:
        *   `max_so_far`: Stores the highest sum encountered across all substrings ending so far. Initialized to 0.
        *   `current_max`: Stores the sum of the current substring being evaluated. Initialized to 0.
    *   It iterates through each character `char_s` in the input string `s`:
        *   It retrieves the `value` of `char_s` from `char_val_map`.
        *   `value` is added to `current_max`.
        *   `max_so_far` is updated to be the maximum of `max_so_far` and `current_max`.
        *   **Crucially**: If `current_max` ever becomes negative, it's reset to 0. This is because a negative prefix will only decrease the sum of any subsequent subarray, so it's better to start a new subarray from the next character.
    *   Finally, `max_so_far` is returned.

---

### 3. Key Design Decisions

*   **`char_val_map` (Dictionary/Hash Map)**:
    *   **Decision**: Using a dictionary to store character-to-value mappings.
    *   **Trade-off**: Provides average O(1) time complexity for value lookups, which is highly efficient. The space overhead is constant (26 entries for the alphabet).
*   **Kadane's Algorithm**:
    *   **Decision**: Employing Kadane's algorithm for finding the maximum contiguous subarray sum.
    *   **Trade-off**: It's a well-known, optimal (linear time) solution for this specific problem type. It efficiently handles negative numbers and ensures the maximum possible sum, including the case where an empty substring (cost 0) is the best option.
*   **Default Value Initialization**:
    *   **Decision**: Pre-populating the map with all lowercase letters and their default values.
    *   **Trade-off**: Ensures that every character in the input string `s` will have a defined cost, even if it's not explicitly mentioned in `chars`. This simplifies the main Kadane's loop as it doesn't need to handle missing keys.

---

### 4. Complexity

Let `N` be the length of the input string `s`.
Let `M` be the length of the `chars` string (and `vals` list).

*   **Time Complexity**:
    *   Populating default `char_val_map` (26 iterations): O(1) constant time.
    *   Overriding custom values (`M` iterations): O(M).
    *   Kadane's algorithm (iterating through `s`, `N` times, with O(1) map lookups): O(N).
    *   **Total Time Complexity**: O(M + N). This is optimal as every custom character and every character in `s` must be processed at least once.

*   **Space Complexity**:
    *   `char_val_map`: Stores 26 entries (for 'a' through 'z'). This is a constant amount of memory, regardless of `N` or `M`.
    *   **Total Space Complexity**: O(1).

---

### 5. Edge Cases & Correctness

*   **Empty input string `s`**:
    *   `max_so_far` remains 0. The loops for calculating `current_max` are not entered.
    *   **Correctness**: Returns 0, which is correct as an empty substring has a cost of 0.
*   **All characters in `s` lead to negative costs**:
    *   `current_max` will continually decrease and be reset to 0 whenever it drops below 0.
    *   `max_so_far` will remain 0 (its initial value), because `max(0, current_max)` will always pick 0 if `current_max` is negative.
    *   **Correctness**: This is the correct behavior for Kadane's algorithm when an empty subarray/substring is allowed and has a value of 0. If one *must* select at least one character, the problem definition would need to specify handling for this, but the current implementation correctly implies the empty substring is an option.
*   **Empty `chars` or `vals`**:
    *   The loop for overriding custom values (`for i in range(len(chars))`) will not execute.
    *   **Correctness**: The code will correctly proceed using only the default alphabet values, which is the desired behavior.
*   **`chars` and `vals` length mismatch**:
    *   The code implicitly assumes `len(chars) == len(vals)`. If they mismatch, `IndexError` would occur.
    *   **Correctness**: Assuming standard problem constraints where these lengths match.
*   **Characters in `s` not in `chars`**:
    *   These characters will correctly use their default 1-indexed alphabet values, as they are pre-populated in `char_val_map` and not overridden.
    *   **Correctness**: Handled by the initialization step.
*   **Characters in `s` that are not lowercase English letters**:
    *   The problem constraints typically guarantee that `s` only contains lowercase English letters. If not, `char_val_map[char_s]` would raise a `KeyError`.
    *   **Correctness**: Assumes valid input string `s`.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   The code is already quite clear and well-structured.
    *   Adding type hints to `char_val_map` (e.g., `char_val_map: Dict[str, int] = {}`) could slightly enhance clarity, though it's obvious from context.
*   **Robustness**:
    *   If `len(chars)` is not guaranteed to equal `len(vals)`, an explicit check could be added:
        ```python
        if len(chars) != len(vals):
            raise ValueError("Lengths of 'chars' and 'vals' must be equal.")
        ```
    *   If `s` could contain characters not in 'a'-'z', a default handling (e.g., `char_val_map.get(char_s, 0)`) or an error (`KeyError`) would be needed based on requirements.
*   **Performance**:
    *   The current solution is already optimal in terms of time and space complexity for this problem. No significant performance improvements are possible without altering the fundamental problem requirements.

---

### 7. Security/Performance Notes

*   **Security**: There are no apparent security vulnerabilities in this code. It processes string and list inputs without external interactions or complex parsing that might lead to injection, overflow, or other common security flaws.
*   **Performance**: As detailed in section 4, the algorithm is highly efficient (linear time, constant space). For typical competitive programming constraints (e.g., `N` up to 10^5 or 10^6), this solution would perform well within time limits. Python's dictionary lookups are very fast for small key sets like 26 characters.

### Code:
```python
from typing import List

class Solution:
    def maximumCostSubstring(self, s: str, chars: str, vals: List[int]) -> int:
        char_val_map = {}

        # 1. Populate default alphabet values (1-indexed)
        for i in range(26):
            char = chr(ord('a') + i)
            char_val_map[char] = i + 1

        # 2. Override with custom values from chars and vals
        for i in range(len(chars)):
            char_val_map[chars[i]] = vals[i]

        # 3. Apply Kadane's algorithm to find the maximum subarray sum
        max_so_far = 0
        current_max = 0

        for char_s in s:
            value = char_val_map[char_s]
            current_max += value
            max_so_far = max(max_so_far, current_max)
            # If current_max becomes negative, it's better to start a new subarray
            # from the next element, as a negative prefix would only decrease future sums.
            if current_max < 0:
                current_max = 0
        
        return max_so_far
```
