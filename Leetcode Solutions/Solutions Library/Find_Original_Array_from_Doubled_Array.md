## Find Original Array from Doubled Array
**Language:** python
**Tags:** python,oop,hashmap,sorting,greedy algorithm

### Description:

---

### 1. Overview & Intent

This code aims to reconstruct an "original" array from a "changed" array. The "changed" array is formed by taking every element `x` from an "original" array and adding both `x` and `2*x` to the "changed" array. The function `findOriginalArray` should return the `original` array if `changed` can be formed this way, or an empty list `[]` if it cannot.

### 2. How It Works

1.  **Initial Check**: It first verifies if the length of the `changed` array is odd. If so, it immediately returns an empty list, as each `original` element contributes two elements to `changed`, implying an even length for `changed`.
2.  **Frequency Counting**: It uses `collections.Counter` to efficiently store the frequency of each number in `changed`.
3.  **Sorted Iteration**: The core logic iterates through the *unique numbers* present in `changed` in *ascending order*. This sorting is crucial for the pairing strategy.
4.  **Pairing Logic (by number type)**:
    *   **Zero (`num == 0`)**: If `0` is an original element, then `0` and `2*0` (which is also `0`) must be in `changed`. This implies `0` must appear an even number of times. If its count is odd, it's invalid. Otherwise, half of its occurrences are added to `original`.
    *   **Positive Numbers (`num > 0`)**: When a positive `num` is encountered (in ascending order), it's assumed to be an `original` element. The code then attempts to find its double, `2*num`. If there aren't enough `2*num` occurrences to match all `num`s, it's invalid. Otherwise, `num` is added to `original`, and counts for both `num` and `2*num` are decremented.
    *   **Negative Numbers (`num < 0`)**: This is a bit counter-intuitive due to ascending sort. If we encounter a negative `num`, it *must* be the `doubled` part (`2*x`) of some `x` from `original`, because `2*x` (being smaller) would be processed before `x` in an ascending sort.
        *   First, `num` must be even to be a double of an integer `x`. If odd, it's invalid.
        *   Then, it calculates `half_num = num // 2` (which is `x`). It checks if there are enough `half_num` occurrences to pair with `num`. If not, invalid.
        *   Otherwise, `half_num` is added to `original`, and counts for both `num` and `half_num` are decremented.
5.  **Result**: If the loop completes without invalidating the array, the `original` array is returned.

### 3. Key Design Decisions

*   **`collections.Counter`**: This is an excellent choice for efficient frequency mapping. It provides O(1) average-time complexity for lookups and updates, which is crucial for performance. A standard dictionary (`dict`) could also work, but `Counter` is specialized and often more convenient for frequency tasks.
*   **Sorting `counts.keys()`**: Iterating through the unique numbers in ascending order is the most critical design decision. It establishes a consistent rule for how pairs are identified:
    *   For `num > 0`, `num` (smaller) is processed before `2*num` (larger). So, `num` is assumed to be an "original", and `2*num` is consumed.
    *   For `num < 0`, `2*num` (smaller) is processed before `num` (larger). So, `num` is assumed to be a "double" (`2*x`), and `x` (which is `num // 2` and is larger) is consumed.
    *   This elegant strategy allows a single sorted iteration to handle all cases by adjusting the pairing logic.
*   **Early Exit**: The `if n % 2 != 0:` and subsequent `return []` statements for invalid conditions (e.g., insufficient doubles, odd zero count) prevent unnecessary computation and ensure correctness.

### 4. Complexity

*   **Time Complexity**:
    *   `len(changed)` and `n % 2 != 0`: O(1)
    *   `collections.Counter(changed)`: O(N), where N is the length of `changed`.
    *   `sorted(counts.keys())`: If U is the number of unique elements in `changed`, this takes O(U log U). In the worst case, U can be N (all elements unique), making it O(N log N).
    *   Loop iteration: The loop runs U times. Inside the loop, `counts` operations (lookups, decrements) are O(1) on average. `original.extend` operations collectively take O(N) over the entire execution (as total elements added to `original` is N/2).
    *   **Overall Time Complexity**: O(N log N), dominated by the sorting of unique keys.
*   **Space Complexity**:
    *   `counts`: Stores at most U unique elements, so O(U) space. In the worst case, O(N).
    *   `original`: Stores N/2 elements, so O(N) space.
    *   `sorted(counts.keys())`: Creates a list of U unique keys, so O(U) space. In the worst case, O(N).
    *   **Overall Space Complexity**: O(N).

### 5. Edge Cases & Correctness

*   **Empty `changed` list (`[]`)**: `n = 0`, `n % 2 != 0` is false. `counts` will be empty. `sorted(counts.keys())` will be empty. The loop won't run. `original` remains `[]`, which is correctly returned.
*   **Odd length `changed`**: Correctly handled by the initial `if n % 2 != 0:` check, returning `[]`.
*   **Arrays with `0`**:
    *   `[0, 0, 0, 0]`: `counts[0]=4`. Loop for `num=0` proceeds, `counts[0]%2 == 0`. `original` becomes `[0, 0]`. Correct.
    *   `[0, 0, 0]`: `counts[0]=3`. Loop for `num=0` finds `counts[0]%2 != 0`. Returns `[]`. Correct.
*   **Arrays with mixed positives/negatives**: The separate logic branches for `num > 0`, `num == 0`, and `num < 0` combined with ascending sort correctly handle all combinations.
*   **Non-doubled elements**: If a number `num` or its corresponding double/half is missing or has an insufficient count, the conditions `if counts[2 * num] < counts[num]` or `if counts[half_num] < counts[num]` will catch it, returning `[]`.
*   **Numbers not forming integer pairs**: For `num < 0`, `if num % 2 != 0` handles cases like `[-3, -1.5]` (where `-1.5` is not an integer), correctly returning `[]`.
*   **Large Integers**: Python integers support arbitrary precision, so overflow is not a concern.

### 6. Improvements & Alternatives

*   **Readability**: The comments provided in the code are exceptionally good, especially for explaining the nuanced logic for positive and negative numbers in the context of ascending iteration. This significantly boosts readability.
*   **Performance (Minor)**: The `original.extend([num] * counts[num])` line within the loop can be slightly more performant than repeatedly calling `original.append(num)` in a sub-loop for the same count. This is already well-implemented.
*   **Alternative Sorting Strategy**: One could sort the `changed` array itself (or `counts.keys()`) in *descending* order.
    *   For `num > 0`, `2*num` would be processed before `num`. So, `num` would be assumed to be `2*x`, and `x` (`num/2`) would be consumed. This avoids the need for `2*num` being a "future" element.
    *   For `num < 0`, `num` would be processed before `2*num`. So `num` would be assumed to be an "original", and `2*num` would be consumed.
    This would flip the logic for positive and negative numbers but still maintain a consistent pairing approach based on processing order. The current ascending sort implementation is perfectly valid and clear.

### 7. Security/Performance Notes

*   **Performance**: The O(N log N) time complexity and O(N) space complexity are generally considered efficient for typical constraints (e.g., N up to 10^5). Python's built-in `sorted()` and `collections.Counter` are highly optimized C implementations.
*   **Security**: The code operates purely on numerical lists and does not involve external input, network communication, file system access, or complex system interactions. Therefore, there are no inherent security vulnerabilities in this specific implementation.

### Code:
```python
import collections
from typing import List

class Solution:
    def findOriginalArray(self, changed: List[int]) -> List[int]:
        n = len(changed)
        # If the length of 'changed' is odd, it cannot be a doubled array
        # because each element from 'original' contributes two elements to 'changed'.
        if n % 2 != 0:
            return []

        # Use a frequency map (Counter) to store counts of each number in 'changed'.
        counts = collections.Counter(changed)
        original = []

        # Iterate through the unique numbers in 'changed' in ascending order.
        # This order is crucial for correctly identifying original elements and their doubles.
        #
        # Logic for different types of numbers:
        # 1. Zero (num == 0):
        #    If '0' is in 'original', then '0' and '2*0' (which is also '0') must be in 'changed'.
        #    This means '0' must appear an even number of times in 'changed'.
        # 2. Positive numbers (num > 0):
        #    If 'num' is an element of 'original', then 'num' and '2*num' must be in 'changed'.
        #    In an ascending sort, 'num' will appear before '2*num'. So, we assume 'num' is an
        #    'original' element and try to find its double '2*num'.
        # 3. Negative numbers (num < 0):
        #    If 'num' is an element of 'original', then 'num' and '2*num' must be in 'changed'.
        #    However, for negative numbers, '2*num' is smaller than 'num'. In an ascending sort,
        #    '2*num' would have been processed *before* 'num'.
        #    Therefore, if we encounter a negative 'num' with a count > 0, it must be the
        #    'doubled' part (2*x) of some 'x' from 'original'. The 'original' part would be 'x = num / 2'.
        #    We then try to find 'x' (which is larger than 'num' and would be processed later).
        
        for num in sorted(counts.keys()):
            # If the current number's count is zero, it means all its occurrences
            # have already been used up as part of a pair. Skip it.
            if counts[num] == 0:
                continue

            if num == 0:
                # For zero, check if its count is even. If odd, it's not a doubled array.
                if counts[0] % 2 != 0:
                    return []
                # Add half of the zeros to the original array.
                original.extend([0] * (counts[0] // 2))
                counts[0] = 0 # All zeros are now processed.
            elif num > 0:
                # For positive numbers, 'num' is assumed to be an element from 'original'.
                # We need to find its double, '2 * num'.
                if counts[2 * num] < counts[num]:
                    # If there aren't enough '2 * num' to pair with all 'num's,
                    # then 'changed' is not a doubled array.
                    return []
                
                # Add 'num' to 'original' for each of its occurrences.
                original.extend([num] * counts[num])
                # Decrement the count of '2 * num' as they are used up.
                counts[2 * num] -= counts[num]
                counts[num] = 0 # All occurrences of 'num' are now processed.
            else: # num < 0
                # For negative numbers, 'num' is assumed to be the 'doubled' part (2*x).
                # The 'original' part would be 'x = num / 2'.
                
                # First, check if 'num' can be a double (i.e., it must be an even number).
                if num % 2 != 0:
                    return [] # Cannot form an integer 'x' such that 2*x = num.
                
                half_num = num // 2 # This is the potential 'original' element (x).
                
                # We need to find 'half_num' to pair with 'num'.
                # If there aren't enough 'half_num's to pair with all 'num's,
                # then 'changed' is not a doubled array.
                if counts[half_num] < counts[num]:
                    return []
                
                # Add 'half_num' to 'original' for each 'num' occurrence.
                original.extend([half_num] * counts[num])
                # Decrement the count of 'half_num' as they are used up.
                counts[half_num] -= counts[num]
                counts[num] = 0 # All occurrences of 'num' are now processed.
        
        # If the loop completes without returning an empty array, it means all numbers
        # were successfully paired according to the rules.
        # The 'original' array now contains the reconstructed elements.
        return original
```
