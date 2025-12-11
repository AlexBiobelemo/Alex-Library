## Count Words Obtained After Adding A Letter
**Language:** python
**Tags:** python,bit manipulation,set,oop

### Description:
This code solves a problem where you need to count how many "target words" can be formed from "start words" by adding exactly one letter. The key insight is that the order of letters doesn't matter, only their presence, making bitmasks an ideal solution.

---

### 1. Overview & Intent

*   **Goal:** Count how many words in `targetWords` can be derived from a word in `startWords` by adding exactly one character.
*   **Definition of "derived":** A `targetWord` is derived from a `startWord` if the `targetWord` contains all the unique characters of the `startWord` plus exactly one additional unique character. The order of characters does not matter.

---

### 2. How It Works

1.  **`get_mask(word: str) -> int` function:**
    *   This helper function converts a word into a unique integer bitmask.
    *   Each bit in the mask corresponds to a letter of the alphabet (e.g., bit 0 for 'a', bit 1 for 'b', ..., bit 25 for 'z').
    *   For each character in the input `word`, it sets the corresponding bit in the mask to 1 using bitwise OR (`|=`). This ensures that duplicate characters in a word only set their bit once.
    *   Example: "abc" -> `1 | (1<<1) | (1<<2)` (binary `0b111`)

2.  **Preprocessing `startWords`:**
    *   It iterates through all `startWords`.
    *   For each `startWord`, it calculates its bitmask using `get_mask()`.
    *   These masks are stored in a `set` called `start_word_masks` for efficient `O(1)` average-case lookup later.

3.  **Processing `targetWords`:**
    *   It initializes a `count` variable to 0.
    *   For each `targetWord`:
        *   It calculates its bitmask, `target_mask`.
        *   It then iterates 26 times (once for each possible letter 'a' through 'z').
        *   In each iteration `i`:
            *   It checks if the `i`-th bit is set in `target_mask` (`(target_mask >> i) & 1`). This means the character corresponding to `i` is present in the `targetWord`.
            *   If the bit is set, it creates a `potential_start_mask` by XORing (`^`) `target_mask` with `(1 << i)`. This effectively *removes* the `i`-th bit from `target_mask`.
            *   It then checks if this `potential_start_mask` exists in the `start_word_masks` set.
            *   If found, it means `targetWord` *could* be formed by adding the `i`-th character to a `startWord`. The `count` is incremented, and the inner loop breaks because a single match is sufficient for the `targetWord` to be counted.

4.  **Return `count`:** The total number of `targetWords` that meet the criteria.

---

### 3. Key Design Decisions

*   **Bitmasks for Word Representation:**
    *   **Rationale:** Crucial for handling words where character order doesn't matter and characters can be duplicated. A bitmask uniquely represents the *set* of characters present in a word.
    *   **Benefit:** Enables efficient character set operations (like adding/removing a character) using bitwise logic, and compact storage.
*   **`set` for `start_word_masks`:**
    *   **Rationale:** Allows for `O(1)` average-case time complexity when checking if a `potential_start_mask` exists.
    *   **Alternative (and why it's worse):** Using a `list` would lead to `O(N)` lookup time, significantly impacting overall performance.
*   **Early Exit (`break`):**
    *   **Rationale:** Once a `targetWord` is found to be derivable from *any* `startWord`, there's no need to check other possible letter removals. This optimizes the inner loop.

---

### 4. Complexity

Let `N` be `len(startWords)` and `M` be `len(targetWords)`.
Let `L_s_max` be the maximum length of a word in `startWords`, and `L_t_max` for `targetWords`.
Note: Word lengths are effectively capped at 26 unique characters, so `L_s_max` and `L_t_max` are usually small constants relative to `N` and `M`.

*   **Time Complexity:**
    *   **`get_mask` function:** `O(L)` where `L` is the length of the word.
    *   **Preprocessing `start_word_masks`:** `N` calls to `get_mask` + `N` set insertions. Each set insertion is `O(1)` average. Total: `O(N * L_s_max)`.
    *   **Processing `targetWords`:**
        *   `M` iterations.
        *   Inside each iteration: `O(L_t_max)` for `get_mask`, followed by 26 iterations (constant) for checking each bit. Inside the 26-iteration loop, bitwise operations are `O(1)`, and set lookup is `O(1)` average.
        *   Total: `O(M * (L_t_max + 26)) = O(M * L_t_max)`.
    *   **Overall Time Complexity:** `O(N * L_s_max + M * L_t_max)`.
        *   Considering `L_s_max` and `L_t_max` as small constants (max 26), this is effectively `O(N + M)`.

*   **Space Complexity:**
    *   `start_word_masks` set: Stores up to `N` integer masks. Each integer takes constant space. Total: `O(N)`.
    *   Other variables: `O(1)`.
    *   **Overall Space Complexity:** `O(N)`.

---

### 5. Edge Cases & Correctness

*   **Empty input lists (`startWords` or `targetWords`):** The `count` will correctly remain `0`.
*   **Words with duplicate characters:** `get_mask` correctly handles this by only setting the bit once. E.g., "banana" will produce the same mask as "ban". This aligns with the "set of characters" interpretation of the problem.
*   **Words with non-lowercase English letters:** The code assumes inputs contain only lowercase English letters ('a'-'z') because it relies on `char_code - ord('a')` to map to bit positions 0-25. If other characters were allowed, `get_mask` would need modification (e.g., filtering, or using a larger mask). For typical competitive programming problems, this assumption holds.
*   **`targetWord` is an exact rearrangement of a `startWord`:** The algorithm correctly handles this. For a `target_mask` to be derived from a `start_mask`, the `target_mask` must have *exactly one more* bit set. If a `targetWord` (e.g., "a") came from a `startWord` (e.g., "a"), the `potential_start_mask` (`target_mask ^ (1<<i)`) would be `0` (for an empty string). If an empty string was in `startWords`, it would match. If not, it won't. This accurately reflects "adding exactly one letter."
*   **Duplicate `targetWords`:** Each occurrence of a duplicate `targetWord` in the input list will be processed and potentially counted, which is usually the desired behavior for list inputs.

---

### 6. Improvements & Alternatives

*   **Readability (Constants):** Define `ALPHABET_SIZE = 26` and `ORD_A = ord('a')` as constants.
    *   Using `ALPHABET_SIZE` in `range(26)` and `ORD_A` in `get_mask` makes the code intent clearer and easier to modify if the character set ever changed.
*   **Pythonic Set Comprehension:**
    *   The creation of `start_word_masks` can be made more concise:
        ```python
        start_word_masks = {get_mask(s_word) for s_word in startWords}
        ```
*   **Input Validation:** For production code, consider adding checks:
    *   Verify `startWords` and `targetWords` are lists.
    *   Verify elements within lists are strings.
    *   Verify strings contain only lowercase English letters (if strict adherence to `a-z` is required).
*   **Alternative for `get_mask`:** Python's `frozenset` could be used to represent character sets directly, but then the set operations (`difference` for removing a character) would be slightly less performant than bitwise operations, and storage might be heavier. Bitmasks are generally superior for fixed, small alphabet sizes.

---

### 7. Security/Performance Notes

*   **Performance:** The bitmask approach is highly performant. It leverages fast integer bitwise operations and `O(1)` average-case hash set lookups. This avoids the overhead of string comparisons, sorting, or more complex data structures. The overall time complexity is near-optimal for this type of problem.
*   **Security:** There are no inherent security vulnerabilities in this algorithm. It performs computations on string inputs but does not execute them, interact with external systems, or process sensitive information in a way that would introduce common security flaws. The fixed size of the bitmask (26 bits) also prevents issues with excessively long or complex inputs causing arbitrary memory allocation or processing time (beyond the initial `get_mask` call for distinct characters).

### Code:
```python
from typing import List

class Solution:
    def wordCount(self, startWords: List[str], targetWords: List[str]) -> int:
        
        def get_mask(word: str) -> int:
            mask = 0
            for char_code in map(ord, word):
                mask |= (1 << (char_code - ord('a')))
            return mask

        start_word_masks = set()
        for s_word in startWords:
            start_word_masks.add(get_mask(s_word))
        
        count = 0
        for t_word in targetWords:
            target_mask = get_mask(t_word)
            
            for i in range(26):
                if (target_mask >> i) & 1: 
                    potential_start_mask = target_mask ^ (1 << i)
                    
                    if potential_start_mask in start_word_masks:
                        count += 1
                        break 
                        
        return count
```
