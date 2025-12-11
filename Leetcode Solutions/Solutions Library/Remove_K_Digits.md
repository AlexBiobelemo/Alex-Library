## Remove K Digits
**Language:** python
**Tags:** python,oop,stack,greedy,string manipulation

### Description:
The provided Python code implements a solution to the "Remove K Digits" problem, aiming to find the smallest possible integer after removing `k` digits from a given non-negative integer represented as a string.

---

### 1. Overview & Intent

The `removeKdigits` function takes a string `num` representing a large non-negative integer and an integer `k`. Its goal is to return the smallest possible string representation of a number after removing exactly `k` digits from `num`.

**Example:**
*   `num = "1432219", k = 3` should result in `"1219"`.
*   `num = "10200", k = 1` should result in `"200"`.
*   `num = "10", k = 2` should result in `"0"`.

---

### 2. How It Works

The algorithm uses a greedy approach with a monotonic stack:

*   **Greedy Digit Removal (Main Loop):**
    *   It iterates through each `digit` in the input `num`.
    *   For each `digit`, it checks if the `stack` is not empty, `k` removals are available (`k > 0`), and the last digit pushed to the `stack` (`stack[-1]`) is greater than the current `digit`.
    *   If all conditions are met, it means we found a smaller digit (`digit`) that can replace a larger digit (`stack[-1]`) at a higher significant position. To make the resulting number smaller, we greedily `pop()` the larger digit from the stack and decrement `k`. This process continues until `k` is 0, the stack is empty, or the current digit is not smaller than the stack's top.
    *   After the potential pops, the current `digit` is pushed onto the `stack`. This builds a sequence of digits that is mostly non-decreasing.

*   **Remaining `k` Removals:**
    *   After processing all digits, if `k` is still greater than 0, it means we still need to remove more digits. Since the stack was built in a way that prioritizes smaller digits at the front, any remaining `k` digits to remove must come from the end of the stack (which would contain the largest digits in a mostly increasing sequence).
    *   It repeatedly `pop()`s from the end of the `stack` until `k` becomes 0.

*   **Constructing the Result:**
    *   The digits remaining in the `stack` are joined to form a `result` string.

*   **Handling Leading Zeros:**
    *   The code then explicitly removes any leading zeros from the `result` string (e.g., "0200" becomes "200"). It finds the index of the first non-zero digit and slices the string from there.

*   **Empty Result Check:**
    *   Finally, if `result` becomes an empty string (e.g., if all digits were removed), it returns "0".

---

### 3. Key Design Decisions

*   **Monotonic Stack:** The core design decision is using a stack to maintain a monotonically non-decreasing sequence of digits. This allows for efficient `O(1)` access to the last added digit and `O(1)` removal.
*   **Greedy Strategy:** The algorithm employs a greedy strategy: whenever a smaller digit is encountered, and `k` allows, it's always better to remove a larger preceding digit. This is because removing a digit at a higher place value has a greater impact on reducing the overall number.
*   **Two-Phase `k` Reduction:** The problem of removing `k` digits is handled in two phases:
    1.  Removing larger digits that are followed by smaller ones (e.g., in "42", remove "4" to get "2"). This occurs during the main loop.
    2.  If `k` is still positive, removing the `k` largest digits from the end of the now (mostly) increasing sequence in the stack (e.g., in "12345", remove "5" then "4" if `k` remains).
*   **String Representation:** Using strings for `num` allows handling arbitrarily large numbers that would exceed standard integer types.

---

### 4. Complexity

*   **Time Complexity:** `O(N)`
    *   The main `for` loop iterates `N` times (where `N` is the length of `num`).
    *   Inside the `for` loop, each digit is pushed onto the stack at most once and popped from the stack at most once across the entire loop's execution. Therefore, the `while` loop contributes `O(N)` operations in total.
    *   The second `while` loop (for remaining `k`) runs at most `k` times, which is `O(N)`.
    *   `"".join(stack)` takes `O(N)` time.
    *   The leading zeros removal `while` loop and slicing take `O(N)` time.
    *   Overall, the dominant factor is `O(N)`.

*   **Space Complexity:** `O(N)`
    *   The `stack` can store up to `N` digits in the worst case (e.g., `num = "12345", k = 0`).
    *   The `result` string also stores up to `N` digits.
    *   Therefore, the space complexity is `O(N)`.

---

### 5. Edge Cases & Correctness

*   **`k` equals `0`:** No digits are removed. The code correctly appends all digits to the stack, then joins them, and handles any potential leading zeros (though for `k=0`, no significant changes are expected).
*   **`k` is greater than or equal to `len(num)`:** All digits must be removed. The stack will either be emptied during the main loop or fully popped in the second `while` loop. The `if not result: return "0"` handles this case correctly, returning "0". (e.g., `num="123", k=3` -> `stack` will be empty, `result=""` -> "0").
*   **`num` contains leading zeros (after removal):**
    *   E.g., `num = "10200", k = 1`. The `1` is removed by `0`. Stack becomes `['0', '2', '0', '0']`. Result "0200". The leading zeros removal correctly converts it to "200".
    *   E.g., `num = "10", k = 1`. `1` pushed. `0` comes, `1` popped (`k=0`). `0` pushed. Stack `['0']`. Result "0". Leading zeros removal handles "0" correctly (it should remain "0"). The condition `first_non_zero < len(result) - 1` ensures that a single "0" doesn't become an empty string.
*   **Monotonically increasing `num`:** E.g., `num = "12345", k = 2`. The main loop will just push all digits. `k` will still be `2`. The second `while` loop will pop `5` and `4`. Result "123". Correct.
*   **Monotonically decreasing `num`:** E.g., `num = "54321", k = 2`.
    *   `'5'` pushed.
    *   `'4'` comes: `'5'` popped (`k=1`), `'4'` pushed. Stack `['4']`.
    *   `'3'` comes: `'4'` popped (`k=0`), `'3'` pushed. Stack `['3']`.
    *   `'2'` comes: `k=0`, so `'2'` pushed. Stack `['3', '2']`.
    *   `'1'` comes: `k=0`, so `'1'` pushed. Stack `['3', '2', '1']`.
    *   Result "321". This is correct.

---

### 6. Improvements & Alternatives

*   **Concise Leading Zero Handling:** The logic for removing leading zeros and handling an empty result can be made more concise using string methods:
    ```python
    result = "".join(stack).lstrip('0')
    return result if result else "0"
    ```
    This `lstrip('0')` handles all leading zeros. If the string becomes empty after stripping (e.g., original `stack` was `['0','0']`), `result` will be `""`, and `result if result else "0"` correctly returns `"0"`.

*   **Input Validation:** For a robust production system, one might add checks for:
    *   `num` containing only digit characters.
    *   `k` being a non-negative integer.

*   **No Significant Algorithmic Alternatives:** The monotonic stack approach is the standard and most efficient known solution for this problem. Other approaches (e.g., recursive backtracking) would be far less efficient due to the search space.

---

### 7. Security/Performance Notes

*   **Performance:** The `O(N)` time and space complexity is optimal for this problem, as every digit must be processed, and a significant portion might need to be stored. It handles `num` strings of considerable length efficiently.
*   **Large Number Handling:** By treating `num` as a string, the solution inherently avoids integer overflow issues that would arise if `num` were converted to a numeric type for very large inputs.
*   **Memory Usage:** For extremely long `num` strings, `O(N)` space for the stack and result string could become a factor, but typically, `N` would not be so large as to cause issues within typical memory limits.

### Code:
```python
class Solution:
    def removeKdigits(self, num: str, k: int) -> str:
        stack = []
        
        for digit in num:
            # While k > 0, stack is not empty, and the last digit in stack is greater than the current digit
            # This means we found a larger digit followed by a smaller digit, so we can remove the larger digit
            while k > 0 and stack and stack[-1] > digit:
                stack.pop()
                k -= 1
            stack.append(digit)
            
        # If k is still greater than 0, it means we need to remove more digits.
        # Since the stack is now monotonically increasing (or non-decreasing),
        # the largest digits are at the end. So, remove from the end.
        while k > 0:
            stack.pop()
            k -= 1
            
        # Join the digits to form the result string
        result = "".join(stack)
        
        # Remove leading zeros
        # Find the first non-zero digit
        first_non_zero = 0
        while first_non_zero < len(result) - 1 and result[first_non_zero] == '0':
            first_non_zero += 1
            
        result = result[first_non_zero:]
        
        # If the result is an empty string after removing all digits or leading zeros, return "0"
        if not result:
            return "0"
        
        return result
```
