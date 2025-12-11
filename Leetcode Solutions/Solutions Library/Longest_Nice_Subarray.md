## Longest Nice Subarray
**Language:** python
**Tags:** python,oop,sliding window,bit manipulation

### Description:
This Python code efficiently finds the length of the longest "nice" subarray within a given array of integers `nums`. A subarray is considered "nice" if, for any two distinct numbers `x` and `y` within that subarray, their bitwise AND is zero (`x & y == 0`). This implies that all numbers in the nice subarray have unique set bits; no two numbers share a common set bit.

---

### 1. Overview & Intent

*   **Problem:** Find the maximum length of a subarray `nums[i:j]` such that all numbers within this subarray are "pairwise bit-disjoint".
*   **"Nice Subarray" Definition:** A subarray `[x1, x2, ..., xk]` is nice if `x_a & x_b == 0` for all distinct `a, b` in `[1, ..., k]`. This means if we take the bitwise OR of all numbers in the subarray, say `OR_sum`, then `OR_sum` would be equal to the arithmetic sum of the numbers in the subarray.
*   **Goal:** Return the length of the longest such subarray.

---

### 2. How It Works

The code uses a **sliding window** approach with two pointers, `left` and `right`, to define the current subarray `nums[left:right+1]`.

1.  **Initialization:**
    *   `left`: Starts at the beginning of the array (index 0).
    *   `max_len`: Stores the maximum nice subarray length found so far (initialized to 0).
    *   `current_OR`: A variable that stores the bitwise OR of all numbers currently in the window `nums[left...right-1]`. Because of the "nice" property, `current_OR` effectively represents the union of all set bits present across the numbers in the current valid window.

2.  **Window Expansion (Right Pointer):**
    *   The `right` pointer iterates through `nums`, attempting to extend the window.
    *   For each `nums[right]`:
        *   **Conflict Check:** It checks if `nums[right]` can be added to the current window while maintaining the "nice" property. This is done by `(current_OR & nums[right]) != 0`. If this condition is true, it means `nums[right]` shares at least one set bit with a number already in the window (represented by `current_OR`).
        *   **Window Shrink (Left Pointer):** If a conflict is detected, the `while` loop executes. It removes `nums[left]` from the window by `current_OR ^= nums[left]` and increments `left`. This process continues until `nums[right]` no longer conflicts with the (now smaller) window's `current_OR`. The XOR operation correctly removes the bits of `nums[left]` from `current_OR` *because* all numbers within the window are guaranteed to be pairwise bit-disjoint.
        *   **Add to Window:** Once `nums[right]` can be added without conflict, its bits are incorporated into `current_OR` using `current_OR |= nums[right]`.
        *   **Update Max Length:** The length of the current nice subarray (`right - left + 1`) is compared with `max_len`, and `max_len` is updated if the current subarray is longer.

3.  **Return:** After iterating through all possible `right` positions, `max_len` holds the longest nice subarray length.

---

### 3. Key Design Decisions

*   **Sliding Window:** This is a classic pattern for subarray problems, allowing for efficient (often linear time) traversal by avoiding redundant computations.
*   **Bitwise `OR` (`current_OR`):** This is the core insight. `current_OR` cleverly keeps track of the *union of all set bits* across all numbers in the current window. If a new number `N` has any bit set that is *already* set in `current_OR`, it means `N` shares a bit with at least one number already in the window, violating the "nice" property.
*   **Bitwise `XOR` (`current_OR ^= nums[left]`):** This is another critical and elegant aspect. When `nums[left]` needs to be removed from the window, XORing it with `current_OR` effectively "unsets" its bits from `current_OR`. This works precisely *because* all numbers within a "nice" subarray have pairwise disjoint bits. If `current_OR` is `A | B | C` (where A, B, C are bit-disjoint), then `(A | B | C) ^ A` correctly yields `B | C`. If they weren't disjoint, XOR would not work as a "removal" operation for an OR sum.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   Both the `right` pointer and the `left` pointer traverse the array `nums` at most once.
    *   All bitwise operations (`&`, `|`, `^`) and arithmetic operations (`+`, `max`) are O(1) operations.
    *   Therefore, the total time complexity is linear with respect to the number of elements in `nums`.

*   **Space Complexity: O(1)**
    *   Only a few variables (`left`, `max_len`, `current_OR`, `right`) are used, consuming a constant amount of memory regardless of the input size.

---

### 5. Edge Cases & Correctness

*   **Empty Input (`nums = []`):** `len(nums)` is 0, the `for` loop doesn't run, and `max_len` (initialized to 0) is returned. Correct.
*   **Single Element (`nums = [5]`):**
    *   `right=0, left=0, current_OR=0`.
    *   `while (0 & 5) != 0` is false.
    *   `current_OR |= 5` (becomes 5).
    *   `max_len = max(0, 0-0+1)` (becomes 1).
    *   Loop ends. Returns 1. Correct.
*   **All Elements Pairwise Disjoint (`nums = [1, 2, 4, 8]`):**
    *   The `while` loop condition `(current_OR & nums[right]) != 0` will always be false.
    *   `current_OR` will accumulate `1 | 2 | 4 | 8 = 15`.
    *   `max_len` will update to 1, then 2, then 3, then 4.
    *   Returns 4. Correct.
*   **All Elements Conflicting (`nums = [3, 3, 3]`):**
    *   `r=0`: `current_OR=3`, `max_len=1`.
    *   `r=1`: `nums[1]=3`. `(current_OR & nums[1]) = (3 & 3) != 0` is true.
        *   `while` loop: `current_OR ^= nums[left]` (`3^3=0`), `left=1`. Condition becomes `(0 & 3) != 0` (false). Loop exits.
    *   `current_OR |= nums[1]` (`0|3=3`). `max_len = max(1, 1-1+1) = 1`.
    *   `r=2`: `nums[2]=3`. `(current_OR & nums[2]) = (3 & 3) != 0` is true.
        *   `while` loop: `current_OR ^= nums[left]` (`3^3=0`), `left=2`. Condition becomes `(0 & 3) != 0` (false). Loop exits.
    *   `current_OR |= nums[2]` (`0|3=3`). `max_len = max(1, 2-2+1) = 1`.
    *   Returns 1. Correct.
*   The logic correctly handles the expansion and shrinking of the window, ensuring `current_OR` always represents the bitwise OR of a *nice* subarray, and `max_len` tracks the largest such subarray found.

---

### 6. Improvements & Alternatives

*   **Readability/Comments:** While the code is concise, adding a comment to explain *why* `current_OR ^= nums[left]` works (i.e., due to the pairwise disjoint nature of numbers in a nice subarray) would significantly aid understanding for someone unfamiliar with this bitwise trick.
*   **Explicit Problem Definition:** In a real-world scenario, the "nice subarray" definition would be clearly stated in the problem description or function docstring. Here, it's inferred from the bitwise logic.
*   **Alternative Data Structures (Less Efficient):** One could theoretically use a `set` to store the numbers in the current window and check for the `x & y == 0` condition for every pair. However, this would be much less efficient (O(N * W^2) or O(N * W) depending on how checks are optimized, where W is window size) compared to the bitwise trick. The bitwise approach is optimal.

---

### 7. Security/Performance Notes

*   **Performance:** The use of bitwise operations makes this solution exceptionally fast. These operations are typically executed directly by the CPU at a very low level.
*   **Integer Overflow:** `current_OR` stores a bitmask. For typical problem constraints (e.g., numbers up to 10^9 or 2^30), `current_OR` will fit within a standard 32-bit or 64-bit integer type, so overflow is not a concern. The maximum possible value for `current_OR` would be if all 30-ish bits were set, which is still well within standard integer limits.

### Code:
```python
class Solution:
    def longestNiceSubarray(self, nums: List[int]) -> int:
        left = 0
        max_len = 0
        current_OR = 0

        for right in range(len(nums)):
            while (current_OR & nums[right]) != 0:
                current_OR ^= nums[left]
                left += 1
            
            current_OR |= nums[right]
            max_len = max(max_len, right - left + 1)
            
        return max_len
```
