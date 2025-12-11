## Word Break II
**Language:** python
**Tags:** python,backtracking,dynamic programming,set

### Description:
This code implements a solution for the "Word Break II" problem, which asks to return all possible sentences that can be formed by segmenting a given string `s` using words from a provided dictionary `wordDict`.

---

### 1. Overview & Intent

*   **Problem**: Given a string `s` and a dictionary of words `wordDict`, find all possible ways to insert spaces in `s` to construct a sentence where each word is a valid dictionary word.
*   **Goal**: The `wordBreak` method aims to return a `List[str]` containing all such valid sentences.
*   **Approach**: It uses a recursive backtracking strategy combined with memoization (dynamic programming) to efficiently find all segmentations.

---

### 2. How It Works

1.  **Initialization**:
    *   `word_set = set(wordDict)`: Converts the input list of words into a hash set for efficient `O(1)` average-case lookup time.
    *   `memo = {}`: Initializes a dictionary to store the results of subproblems. This is the memoization table.

2.  **`backtrack(current_s: str)` Function**:
    *   **Memoization Check**: `if current_s in memo: return memo[current_s]`
        *   If the result for `current_s` has already been computed and stored in `memo`, it's immediately returned, preventing redundant computations.
    *   **Base Case - Empty String**: `if not current_s: return [""]`
        *   If `current_s` is an empty string, it signifies that a valid segmentation has been found for the preceding part of the string. Returning `[""]` allows for correct concatenation of the last word without an extra leading space.
    *   **Iterate and Segment**:
        *   `res = []`: Initializes a list to store all valid segmentations found for `current_s`.
        *   `for i in range(1, len(current_s) + 1)`: This loop iterates through all possible prefixes of `current_s`.
        *   `word = current_s[:i]`: Extracts a prefix `word`.
        *   `if word in word_set:`: Checks if the extracted `word` is present in the dictionary.
            *   **Recursive Call**: `sub_sentences = backtrack(current_s[i:])`
                *   If `word` is valid, it recursively calls `backtrack` on the remainder of the string (`current_s[i:]`) to find all possible segmentations for it.
            *   **Combine Results**:
                *   `for sub_sentence in sub_sentences:`: Iterates through each segmentation found for the remainder.
                *   `if sub_sentence: res.append(word + " " + sub_sentence)`: If `sub_sentence` is not empty (i.e., it contains actual words), the current `word` is combined with a space and the `sub_sentence`.
                *   `else: res.append(word)`: If `sub_sentence` is empty (this happens when `backtrack` returns `[""]` for the last word), the current `word` is appended directly, effectively forming the end of a sentence.
    *   **Store and Return**:
        *   `memo[current_s] = res`: The computed list of segmentations `res` for `current_s` is stored in `memo` before being returned.

3.  **Initial Call**:
    *   `return backtrack(s)`: The process starts by calling `backtrack` on the original input string `s`.

---

### 3. Key Design Decisions

*   **Backtracking (Recursion)**: This allows for exploring all possible ways to break the string into dictionary words. Each valid word forms a branching point in the search space.
*   **Memoization (Dynamic Programming)**: This is crucial for efficiency. By storing results for already processed substrings (`memo`), the algorithm avoids redundant computations of overlapping subproblems, transforming an exponential complexity (without memoization) into a more manageable one.
*   **Hash Set for Dictionary (`word_set`)**: Using a `set` for `wordDict` ensures that checking `if word in word_set` takes `O(1)` average time complexity, which is significantly faster than `O(N)` for a list (where `N` is the number of words in the dictionary).
*   **Base Case `[""]` for Empty String**: This is a clever and elegant way to handle the concatenation logic. Returning an empty string literal for a successfully segmented empty suffix simplifies the conditional logic when combining `word` with `sub_sentence`, avoiding trailing spaces or complex edge case checks for the last word.

---

### 4. Complexity

*   **Time Complexity**: `O(N^2 * L + S)`
    *   `N`: Length of the input string `s`.
    *   `L`: Maximum length of a word in `wordDict`.
    *   `S`: Total length of all possible sentences generated.
    *   Explanation:
        *   There are `N` possible substrings `current_s` that can be stored in `memo`. Each `current_s` is processed once.
        *   Inside `backtrack(current_s)`, there's a loop that runs up to `N` times (`range(1, len(current_s) + 1)`).
        *   In each iteration:
            *   `current_s[:i]` (string slicing) takes `O(L)` time in Python.
            *   `word in word_set` (set lookup with string hashing) takes `O(L)` on average for comparing strings of length `L`.
            *   Combining results: The `for sub_sentence in sub_sentences` loop iterates through `M` sub-sentences. Each `res.append(word + " " + sub_sentence)` involves string concatenation, which can take `O(L_current_word + L_sub_sentence)` time. This part can dominate, as `S` (total length of all output sentences) can be exponential in the worst case (e.g., `s = "aaaaa..."`, `wordDict = ["a", "aa", "aaa"]`).
    *   Therefore, the overall complexity is bounded by the sum of work for each state (roughly `N * (N*L)`) plus the cost of constructing all valid output strings.

*   **Space Complexity**: `O(N * L_max + S)`
    *   `N`: Length of the input string `s`.
    *   `L_max`: Maximum length of any word in `wordDict`.
    *   `S`: Total length of all possible sentences generated.
    *   Explanation:
        *   `word_set`: Stores copies of words from `wordDict`, `O(sum of lengths of words in dict)`.
        *   `memo`: Stores results for up to `N` unique substrings. Each result is a list of strings. In the worst case, the total length of all strings stored across all `memo` entries can contribute significantly, up to `O(S)`.
        *   Recursion stack: The depth of the recursion can go up to `N` levels in the worst case (e.g., `s = "aaaaa"`, `wordDict = ["a"]`), storing call frames.

---

### 5. Edge Cases & Correctness

*   **Empty input string `s`**:
    *   `backtrack("")` correctly returns `[""]`. This is a valid base case.
*   **Empty `wordDict`**:
    *   `word_set` will be empty. `word in word_set` will always be `False`. `res` will always be `[]`. Correctly returns `[]`.
*   **No possible segmentation**:
    *   If no prefix matches a dictionary word or no valid continuation is found, `res` for `s` will remain `[]`. Correctly returns `[]`.
*   **Multiple segmentations**:
    *   The nested loops and recursive calls correctly explore all branches and combine `sub_sentences` to find every possible segmentation.
*   **Single word dictionary**:
    *   E.g., `s = "applepie"`, `wordDict = ["applepie"]`. `backtrack("applepie")` finds "applepie", `backtrack("")` returns `[""]`, resulting in `["applepie"]`. Correct.
*   **Duplicate words in `wordDict`**:
    *   Converting `wordDict` to a `set` automatically handles duplicates, ensuring they don't affect logic or performance negatively.
*   **String with no valid prefix**:
    *   E.g., `s = "xyz"`, `wordDict = ["abc"]`. The `if word in word_set` condition will never be met, and `res` will be `[]`. Correct.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   Add docstrings to the `wordBreak` method and the nested `backtrack` function explaining their purpose, arguments, and return values.
*   **Performance (Trie)**:
    *   For very large `wordDict` or long words, using a [Trie (prefix tree)](https://en.wikipedia.org/wiki/Trie) for `word_set` could offer potential optimizations. Instead of repeatedly slicing `current_s[:i]` and performing `O(L)` set lookups, a Trie allows traversing the string `s` and checking for dictionary word prefixes in a more integrated `O(L)` way. This might reduce the constant factor overhead associated with string slicing and hashing, especially if many words share common prefixes.
*   **Iterative Dynamic Programming**:
    *   While the current recursive solution with memoization is effectively dynamic programming, an explicit iterative DP approach could be used. This would typically involve building up solutions for substrings from smaller lengths to larger lengths (e.g., `dp[i]` stores all segmentations for `s[:i]`). This avoids potential recursion depth limits for extremely long strings (though `N` typically maxes out at ~300 for these problems, so it's usually not an issue).

---

### 7. Security/Performance Notes

*   **Performance Bottleneck**: The primary performance bottleneck is the potential for an extremely large number of possible valid sentences. The total length of the `List[str]` returned (`S` in complexity analysis) can grow exponentially with `N`. While the algorithm efficiently finds *how* to break the string, the cost of *constructing and storing* all these individual result strings can be very high. If only the *count* of segmentations were needed, the complexity would be much lower (closer to `O(N^2 * L)`).
*   **No direct security concerns**: The code operates on internal string data and a provided dictionary. There are no external inputs executed, no file I/O, or network operations, so no immediate security vulnerabilities related to code execution or data exposure. The worst-case performance could lead to a denial-of-service if malicious input causes an exponential number of results to be generated, consuming excessive memory and CPU.

### Code:
```python
from typing import List

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> List[str]:
        word_set = set(wordDict)
        memo = {}

        def backtrack(current_s: str) -> List[str]:
            if current_s in memo:
                return memo[current_s]

            if not current_s:
                return [""] # Base case: an empty string means a valid segmentation ending

            res = []
            for i in range(1, len(current_s) + 1):
                word = current_s[:i]
                if word in word_set:
                    # Recursively find solutions for the rest of the string
                    sub_sentences = backtrack(current_s[i:])
                    for sub_sentence in sub_sentences:
                        if sub_sentence:
                            res.append(word + " " + sub_sentence)
                        else:
                            res.append(word) # If sub_sentence is "", just append the word
            
            memo[current_s] = res
            return res

        return backtrack(s)
```
