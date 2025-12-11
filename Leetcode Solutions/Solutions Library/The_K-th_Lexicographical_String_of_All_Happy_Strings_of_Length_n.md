## The K-th Lexicographical String of All Happy Strings of Length n
**Language:** python
**Tags:** python,oop,lexicographical,string

### Description:
This code finds the `k`-th lexicographically smallest "happy string" of a given length `n`. A happy string is defined as a string consisting only of characters 'a', 'b', 'c', where no two adjacent characters are the same.

### 1. Overview & Intent

The primary goal of the `getHappyString` method is to generate the `k`-th lexicographically smallest happy string of length `n`. If there are fewer than `k` happy strings of length `n`, an empty string is returned. This is a combinatorial problem requiring careful enumeration of possibilities based on lexicographical order.

### 2. How It Works

The algorithm constructs the happy string character by character from left to right:

1.  **Calculate Total Count:** It first determines the total number of possible happy strings of length `n`. For `n=1`, there are 3 strings ('a', 'b', 'c'). For `n > 1`, there are 3 choices for the first character, and 2 choices for each subsequent character (as it must differ from the previous one). This results in `3 * (2**(n-1))` total happy strings.
2.  **Handle Invalid `k`:** If the requested `k` is greater than the total number of happy strings, it immediately returns an empty string.
3.  **Iterative Construction:** It iterates `n` times, once for each position in the string to be built.
4.  **Count Suffixes:** In each iteration, it calculates `count_per_prefix`, which is the number of happy strings that can be formed by *fixing* the current character and appending the remaining `n - (i+1)` characters. Since each subsequent character has 2 choices, this count is `2**(n - (i+1))`.
5.  **Determine Possible Characters:**
    *   For the very first character (position `i=0`), all 'a', 'b', 'c' are possible.
    *   For subsequent characters (position `i > 0`), the possible choices are 'a', 'b', 'c' *excluding* the `last_char` that was just appended.
6.  **Lexicographical Selection:** It iterates through the `current_possible_chars` (which are inherently sorted lexicographically).
    *   If the current `k` is less than or equal to `count_per_prefix`, it means the `k`-th happy string falls within the block of strings starting with the current `char_choice`. This `char_choice` is appended to the `result`, and the algorithm proceeds to determine the next character.
    *   If `k` is greater than `count_per_prefix`, it means all strings starting with the current `char_choice` (and any preceding ones) have been exhausted. `k` is reduced by `count_per_prefix`, and the algorithm tries the next `char_choice`.
7.  **Final Result:** Once all `n` characters are determined, the `result` list is joined to form the final string.

### 3. Key Design Decisions

*   **Pre-calculation of Total Count:** An early exit for invalid `k` values prevents unnecessary computation.
*   **Iterative String Construction:** Building the string character by character is a standard and efficient way to handle lexicographical string generation problems.
*   **Greedy Lexicographical Choice:** The core logic of decrementing `k` and comparing it with `count_per_prefix` allows for "greedily" selecting the smallest possible character at each step that keeps the `k`-th string within its branch, ensuring lexicographical order.
*   **Dynamic Character Choices:** The logic to determine `current_possible_chars` based on the `last_char` correctly enforces the "no adjacent characters are the same" rule.
*   **`result` as a list:** Appending to a list and then `"".join()` at the end is generally more efficient for string building in Python than repeated string concatenation.

### 4. Complexity

*   **Time Complexity:**
    *   Initial `total_happy_strings` calculation: `O(log n)` due to `2**(n-1)` (exponentiation by squaring).
    *   The main loop runs `n` times.
    *   Inside the loop:
        *   `count_per_prefix` calculation: `O(log n)` for `2**remaining_len_for_suffix`.
        *   `current_possible_chars` generation: `O(1)` (at most 3 iterations).
        *   Inner `char_choice` loop: `O(1)` (at most 3 iterations).
    *   `"".join(result)`: `O(n)`.
    *   Total Time Complexity: **`O(n log n)`**. (If we pre-calculated all powers of 2 up to `n`, this could be optimized to `O(n)`).

*   **Space Complexity:**
    *   `result` list stores `n` characters: `O(n)`.
    *   Other variables (`chars`, `current_possible_chars`, etc.) are `O(1)`.
    *   Total Space Complexity: **`O(n)`**.

### 5. Edge Cases & Correctness

*   **`k` larger than total happy strings:** Correctly handled by returning `""`.
*   **`n = 1`:**
    *   `total_happy_strings` is 3.
    *   `count_per_prefix` will be `2**0 = 1`.
    *   The code correctly selects 'a' for `k=1`, 'b' for `k=2`, and 'c' for `k=3`.
*   **`k = 1` (smallest string):** The algorithm will always pick 'a' for the first character, then 'b' (as 'b' is the smallest different from 'a'), then 'a' again (smallest different from 'b'), and so on. This correctly produces the lexicographically smallest happy string (e.g., "aba" for `n=3`).
*   **`k = total_happy_strings` (largest string):** The `k` value will be decremented until it lands on the last possible character choice for each position, correctly producing the lexicographically largest happy string.
*   **Character Uniqueness:** The `if char_option != last_char:` condition strictly enforces that adjacent characters are different, ensuring happy string validity.

### 6. Improvements & Alternatives

*   **Pre-calculate Powers of 2:** To optimize the `count_per_prefix` calculation, an array or dictionary could store `2^0, 2^1, ..., 2^(n-1)` once at the beginning. This would reduce the exponentiation calls from `O(log n)` to `O(1)` lookups, bringing the total time complexity to `O(n)`.
    ```python
    powers_of_2 = [1] * n
    for p in range(1, n):
        powers_of_2[p] = powers_of_2[p-1] * 2
    # Then inside loop: count_per_prefix = powers_of_2[remaining_len_for_suffix]
    ```
*   **Direct `current_possible_chars` selection:** Instead of iterating through `chars` and checking inequality, one could use a direct lookup or conditional list creation for `current_possible_chars` for a minor constant factor improvement:
    ```python
    if i == 0:
        current_possible_chars = ['a', 'b', 'c']
    else:
        last_char = result[-1]
        if last_char == 'a': current_possible_chars = ['b', 'c']
        elif last_char == 'b': current_possible_chars = ['a', 'c']
        else: current_possible_chars = ['a', 'b']
    ```
*   **Recursive/Backtracking (Conceptual Alternative):** While the current iterative approach is highly efficient for this specific problem, a general alternative for generating combinations/permutations is recursive backtracking. However, to find the *k*-th string without generating all of them, the same counting logic (how many strings can branch from here?) would need to be embedded in the recursion with pruning, making it essentially the same algorithm but expressed recursively.

### 7. Security/Performance Notes

*   **Integer Size:** Python's integers handle arbitrary precision, so `2**(n-1)` will not cause an overflow even for large `n`. However, the problem constraints for `n` are typically small (e.g., `n <= 15` or `n <= 20`) making `total_happy_strings` fit within standard integer types in other languages as well.
*   **Performance:** The `O(n log n)` or `O(n)` solution is very efficient and suitable for typical competitive programming constraints for `n`. There are no inherent security vulnerabilities in this algorithm.

### Code:
```python
class Solution:
    def getHappyString(self, n: int, k: int) -> str:
        
        # Calculate the total number of happy strings of length n.
        # For n=1, there are 3 happy strings ('a', 'b', 'c').
        # For n > 1, the first character has 3 choices, and each subsequent character has 2 choices
        # (it must be different from the previous one).
        # So, total = 3 * (2^(n-1)).
        
        total_happy_strings = 3 * (2**(n-1))
        
        # If k is greater than the total number of happy strings, return an empty string.
        if k > total_happy_strings:
            return ""
            
        result = []
        
        # Define the set of possible characters.
        chars = ['a', 'b', 'c']
        
        # Iterate through each position of the string to build it character by character.
        for i in range(n):
            
            # `remaining_len_for_suffix` is the number of characters we still need to append
            # AFTER the current character at index `i`.
            # If `i` is the current index, we need `n - (i + 1)` more characters.
            remaining_len_for_suffix = n - (i + 1)
            
            # `count_per_prefix` is the number of happy strings that can be formed
            # by fixing the current character and then appending `remaining_len_for_suffix` characters.
            # Each of these `remaining_len_for_suffix` positions has 2 choices (different from the previous).
            # So, the count is 2 raised to the power of `remaining_len_for_suffix`.
            count_per_prefix = 2**remaining_len_for_suffix
            
            # Determine the possible characters for the current position based on the previous character.
            current_possible_chars = []
            if i == 0:
                # For the first character, all 'a', 'b', 'c' are possible.
                current_possible_chars = chars
            else:
                # For subsequent characters, it must be different from the last character added.
                last_char = result[-1]
                for char_option in chars:
                    if char_option != last_char:
                        current_possible_chars.append(char_option)
            
            # Iterate through the `current_possible_chars` in lexicographical order.
            for char_choice in current_possible_chars:
                # If `k` is less than or equal to `count_per_prefix`, it means the k-th string
                # starts with `char_choice` at the current position.
                if k <= count_per_prefix:
                    result.append(char_choice)
                    break # Move to the next position (next iteration of the outer loop)
                else:
                    # If `k` is greater than `count_per_prefix`, it means all strings starting
                    # with `char_choice` (and all preceding `char_choice` options) have been exhausted.
                    # Subtract `count_per_prefix` from `k` and try the next `char_choice`.
                    k -= count_per_prefix
        
        return "".join(result)
```
