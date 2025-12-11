## Guess Number Higher or Lower
**Language:** python
**Tags:** python,oop,binary search

### Description:
This code implements a classic binary search algorithm to find a hidden number within a given range `[1, n]`. It interacts with an external `guess(num)` API which provides feedback on whether the guessed number is too high, too low, or correct.

---

### 1. Overview & Intent

*   **Problem:** Find a secret number within a range `[1, n]`.
*   **API:** An external function `guess(num)` is provided.
    *   `guess(num) == 0`: `num` is the secret number.
    *   `guess(num) == -1`: `num` is higher than the secret number.
    *   `guess(num) == 1`: `num` is lower than the secret number.
*   **Goal:** Efficiently determine the secret number using the `guess` API.

---

### 2. How It Works

The code uses an iterative binary search approach:

*   **Initialize Search Space:**
    *   `low` is set to `1` (the smallest possible number).
    *   `high` is set to `n` (the largest possible number).
*   **Iterative Search:** A `while` loop continues as long as `low` is less than or equal to `high`, meaning there's still a valid search space.
*   **Calculate Midpoint:** In each iteration, `mid` is calculated as the middle point of the current `low` to `high` range. The formula `low + (high - low) // 2` is used to prevent potential integer overflow compared to `(low + high) // 2` (though less critical in Python).
*   **Call Guess API:** The `guess(mid)` function is called to get feedback on the `mid` value.
*   **Adjust Search Space:**
    *   If `result == 0`: The `mid` is the secret number. It's returned.
    *   If `result == -1`: The guess (`mid`) was too high. The secret number must be in the lower half, so `high` is adjusted to `mid - 1`.
    *   If `result == 1`: The guess (`mid`) was too low. The secret number must be in the upper half, so `low` is adjusted to `mid + 1`.
*   **Termination:** The loop terminates when `low > high`, indicating the search space has been exhausted. If the number is guaranteed to be found within the range, this point should ideally not be reached. The `return -1` acts as a fallback.

---

### 3. Key Design Decisions

*   **Algorithm Choice: Binary Search:**
    *   This is the optimal choice because the `guess` API implicitly provides ordering information (too high/too low), allowing the search space to be halved in each step.
    *   It's highly efficient for searching in sorted or monotonically ordered data (even if implicit).
*   **Iterative Approach:** An iterative binary search is used, which avoids recursion depth limits and often has slightly better performance than recursive versions due to less function call overhead.
*   **Midpoint Calculation:** The `low + (high - low) // 2` calculation is a robust way to find the midpoint, preventing overflow for very large `low` and `high` values, common in languages with fixed-size integers (like C++/Java). In Python, integers handle arbitrary size, so `(low + high) // 2` would also work without overflow, but this form is good practice.

---

### 4. Complexity

*   **Time Complexity: O(log N)**
    *   In each step of the `while` loop, the search space (`high - low + 1`) is roughly halved.
    *   The number of steps required to reduce the search space from `N` to `1` is logarithmic.
    *   Each step involves a constant number of operations (arithmetic, comparison) and one call to the `guess` function.
*   **Space Complexity: O(1)**
    *   The algorithm only uses a few constant variables (`low`, `high`, `mid`, `result`).
    *   The space used does not grow with the input size `N`.

---

### 5. Edge Cases & Correctness

*   **Smallest `N` (`N=1`):**
    *   `low = 1`, `high = 1`.
    *   `mid = 1 + (1 - 1) // 2 = 1`.
    *   `guess(1)` is called. If `result == 0`, `1` is returned. If `result` is anything else, the problem statement (a number is always picked within `1` to `n`) implies `result` *must* be `0`. The loop condition `low <= high` correctly handles this single-element range.
*   **Secret Number at Boundaries (1 or N):**
    *   The `mid - 1` and `mid + 1` adjustments, along with the `low <= high` loop condition, correctly ensure that boundary values are included in the search and eventually checked. For example, if the secret number is `1` and `mid` is `2`, `guess(2)` returns `-1`, `high` becomes `1`, and the next iteration correctly focuses on `[1, 1]`.
*   **Guaranteed Find:** The problem statement implies the secret number is always within `[1, n]`. This ensures the `while` loop will always find the number and `return mid` will be executed, making the final `return -1` unreachable under normal circumstances.

---

### 6. Improvements & Alternatives

*   **Robustness for `guess` API failure:** If the `guess` function could potentially fail (e.g., raise an exception, return an unexpected value), the current code doesn't explicitly handle it. However, given it's an API, we usually assume its contract holds.
*   **Error Handling for Unreachable Return:** The comment `Should not reach here...` is good. For stricter code, one might replace `return -1` with `raise ValueError("Number not found within specified range, contrary to problem statement.")` to explicitly signal an unexpected state if the problem constraints were ever violated.
*   **Readability:** The code is already very readable due to clear variable names and comments. No significant readability improvements are needed.

---

### 7. Security/Performance Notes

*   **Security:** This algorithm has no inherent security vulnerabilities. It's a pure search function operating on integers. It doesn't process external untrusted inputs in a way that could lead to injection or other common vulnerabilities.
*   **Performance:** The O(log N) time complexity is exceptionally good for a search problem. It scales very well with large `N`. For example, to find a number between 1 and a billion (10^9), it would take at most `log2(10^9)` which is approximately 30 `guess` calls. This is highly efficient.

### Code:
```python
class Solution:
    def guessNumber(self, n: int) -> int:
        low = 1
        high = n

        while low <= high:
            mid = low + (high - low) // 2
            result = guess(mid)

            if result == 0:
                return mid
            elif result == -1:  # My guess is higher than the picked number
                high = mid - 1
            else:  # My guess is lower than the picked number
                low = mid + 1
        return -1 # Should not reach here if a number is always picked within 1 to n
```
