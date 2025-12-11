## Minimum Number of Operation to Make Array Empty
**Language:** python
**Tags:** python,oop,hash map,greedy,counting

### Description:
This code addresses a problem where you need to find the minimum number of operations to remove all elements from a list. The allowed operations are to remove either 2 or 3 elements of the *same value* in one step.

---

### 1. Overview & Intent

*   **Problem:** Calculate the minimum operations required to clear a list of integers.
*   **Operation Rules:** In one operation, you can remove either two or three identical elements.
*   **Goal:** Determine the fewest total operations across all distinct numbers in the list. If it's impossible to remove all elements, return -1.
*   **Approach:** The solution efficiently counts the frequency of each number and then calculates the minimum operations for each count individually, summing them up.

---

### 2. How It Works

1.  **Frequency Counting:**
    *   It first uses `collections.Counter(nums)` to create a frequency map (hash map) of all numbers in the input list `nums`. This tells us how many times each unique number appears.
2.  **Iterate Counts:**
    *   It then iterates through the *counts* (frequencies) of each unique number found in `freq_map`.
3.  **Handle Impossible Case:**
    *   If any number appears exactly once (`count == 1`), it's impossible to remove it (as we can only remove 2 or 3 at a time). In this scenario, the function immediately returns -1.
4.  **Calculate Operations for Each Count:**
    *   For any `count > 1`, the minimum operations are calculated using the formula `(count + 2) // 3`. This is a clever way to perform ceiling division (`ceil(count / 3)`) using integer arithmetic.
    *   This formula implicitly prioritizes removing groups of 3. If a remainder of 1 or 2 elements is left, it cleverly uses operations of 2 to clear them.
        *   `count % 3 == 0`: `count // 3` operations (e.g., 6 -> 3+3, `(6+2)//3 = 2`)
        *   `count % 3 == 1`: `(count // 3) + 1` operations (e.g., 4 -> 2+2, `(4+2)//3 = 2`. Note: 4 = 3+1 isn't ideal, better 2+2; 7 = 3+2+2, `(7+2)//3 = 3`)
        *   `count % 3 == 2`: `(count // 3) + 1` operations (e.g., 2 -> 2, `(2+2)//3 = 1`; 5 -> 3+2, `(5+2)//3 = 2`)
5.  **Sum Total Operations:**
    *   The operations calculated for each `count` are added to `total_operations`.
6.  **Return Result:**
    *   Finally, the accumulated `total_operations` is returned.

---

### 3. Key Design Decisions

*   **`collections.Counter`:** An excellent choice for efficiently counting element frequencies. It's built for this purpose and provides clear, concise code.
*   **Early Exit for `count == 1`:** Immediately returning -1 as soon as an impossible count is encountered is efficient and prevents unnecessary calculations.
*   **The `(count + 2) // 3` Formula:** This is the core algorithmic insight. It elegantly handles all `count > 1` cases (mod 3 remainders of 0, 1, or 2) using simple integer arithmetic, effectively implementing `ceil(count / 3)`. This avoids floating-point numbers and conditional branching for different modulo results, leading to cleaner code.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   `collections.Counter(nums)` takes O(N) time, where N is the number of elements in `nums`, as it iterates through the input list once.
    *   The loop iterates over the `freq_map.values()`. If there are D distinct elements, this loop runs D times. In the worst case, D can be N (all elements are distinct), but D is always <= N.
    *   The operations inside the loop are constant time.
    *   Therefore, the total time complexity is O(N) + O(D) which simplifies to **O(N)**.
*   **Space Complexity: O(D)**
    *   The `freq_map` stores the counts for each distinct element. In the worst case, all elements are distinct, so it stores N entries.
    *   Therefore, the space complexity is **O(D)**, where D is the number of distinct elements in `nums`.

---

### 5. Edge Cases & Correctness

*   **Empty Input `nums=[]`:**
    *   `freq_map` will be empty. The `for` loop won't execute. `total_operations` remains 0, which is correct as no operations are needed for an empty list.
*   **Single Element with Count 1 `nums=[1]`:**
    *   `freq_map = {1: 1}`. The loop encounters `count = 1`, and correctly returns -1.
*   **Mixed Counts, some impossible `nums=[1, 2, 2]`:**
    *   `freq_map = {1: 1, 2: 2}`. The loop first processes `count = 1` (for `1`), and correctly returns -1.
*   **All Counts Divisible by 3 `nums=[1,1,1, 2,2,2]`:**
    *   `freq_map = {1: 3, 2: 3}`. For `count=3`: `(3+2)//3 = 1`. Total operations = 1 + 1 = 2. Correct.
*   **Counts with Remainder 1 (e.g., 4, 7) `nums=[1,1,1,1]`:**
    *   `freq_map = {1: 4}`. For `count=4`: `(4+2)//3 = 2`. Correct (4 can be removed as 2+2).
*   **Counts with Remainder 2 (e.g., 2, 5) `nums=[1,1]`:**
    *   `freq_map = {1: 2}`. For `count=2`: `(2+2)//3 = 1`. Correct (2 can be removed as 2).
*   The logic holds true for all `count > 1` due to the mathematical property of `(x + k - 1) // k` being equivalent to `ceil(x / k)` for positive integers `x` and `k`. Here `k=3`.

---

### 6. Improvements & Alternatives

*   **Readability of `(count + 2) // 3`:** The inline comment explaining this specific formula is excellent and vital for understanding. Without it, this concise piece of code might be a "magic number" for many. No further readability improvements are strictly necessary given the comment.
*   **Alternative `ceil` implementation:**
    *   You could use `import math` and `math.ceil(count / 3)` directly. However, the current integer arithmetic version is slightly more performant (avoids float conversion) and avoids potential (though unlikely in this context) floating-point precision issues. The current implementation is generally preferred for competitive programming or performance-sensitive code where `ceil` is needed for integers.
*   **Explicit Conditional Logic (Less Optimal):**
    *   One could use `if/elif/else` based on `count % 3`:
        ```python
        if count % 3 == 0:
            total_operations += count // 3
        elif count % 3 == 1:
            # e.g., for 4: (4-4)//3 + 2 = 2 ops (2+2)
            # e.g., for 7: (7-4)//3 + 2 = 1 + 2 = 3 ops (3+2+2)
            total_operations += (count - 4) // 3 + 2 
        else: # count % 3 == 2
            # e.g., for 2: 2//3 + 1 = 1 op (2)
            # e.g., for 5: 5//3 + 1 = 1 + 1 = 2 ops (3+2)
            total_operations += count // 3 + 1
        ```
    *   This is more verbose and less elegant than `(count + 2) // 3` but explicitly shows the logic for each remainder. The current solution is superior in terms of conciseness and performance.

---

### 7. Security/Performance Notes

*   **Performance:** The solution is optimal in terms of both time (O(N)) and space (O(D)) complexity, which is excellent for the given problem constraints. No significant performance bottlenecks are present.
*   **Security:** There are no apparent security concerns. The code performs standard arithmetic and data structure operations on integer inputs, without processing external untrusted input in a way that could lead to vulnerabilities like injection, buffer overflows, or denial-of-service attacks.

### Code:
```python
import collections
from typing import List

class Solution:
    def minOperations(self, nums: List[int]) -> int:
        freq_map = collections.Counter(nums)
        
        total_operations = 0
        
        for count in freq_map.values():
            if count == 1:
                return -1
            
            # For any count > 1, we can always remove elements.
            # The minimum operations for a given count can be found using ceiling division:
            # ops = ceil(count / 3)
            # This is because we prioritize operations of 3.
            # If count % 3 == 0, ops = count // 3
            # If count % 3 == 1, ops = (count // 3) - 1 + 2 = (count // 3) + 1 (e.g., 4 = 2+2, 7 = 3+2+2)
            # If count % 3 == 2, ops = (count // 3) + 1 (e.g., 2 = 2, 5 = 3+2)
            # All these cases are covered by (count + 2) // 3 using integer division.
            
            total_operations += (count + 2) // 3
            
        return total_operations
```
