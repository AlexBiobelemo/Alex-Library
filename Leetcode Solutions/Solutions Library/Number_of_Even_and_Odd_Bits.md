## Number of Even and Odd Bits
**Language:** python
**Tags:** python,oop,bit manipulation,iteration

### Description:
This code implements a solution to count set bits (bits with value 1) at even and odd 0-based indices within a given non-negative integer `n`.

---

### 1. Overview & Intent

This function, `evenOddBit`, takes a non-negative integer `n` and returns a list containing two integers:
1.  The count of '1' bits found at even 0-based bit positions (0, 2, 4, ...).
2.  The count of '1' bits found at odd 0-based bit positions (1, 3, 5, ...).

The intent is to categorize the set bits of a number based on their positional parity.

---

### 2. How It Works

The algorithm processes the input integer `n` bit by bit, starting from the Least Significant Bit (LSB) at index 0.

1.  **Initialization**:
    *   `even_count` and `odd_count` are initialized to 0 to store the respective counts.
    *   `index` is initialized to 0 to track the current 0-based bit position.

2.  **Bit Iteration**:
    *   A `while` loop continues as long as `n` is greater than 0, meaning there are still bits left to process.
    *   **Check Current Bit**: `(n & 1) == 1` checks if the rightmost bit of `n` (which corresponds to the current `index`) is a '1'.
        *   The bitwise AND operator (`&`) with 1 isolates the LSB. If the LSB is 1, the result is 1; otherwise, it's 0.
    *   **Categorize and Count**: If the current bit is 1:
        *   `index % 2 == 0` checks if the current bit position is even. If so, `even_count` is incremented.
        *   Otherwise (if `index` is odd), `odd_count` is incremented.
    *   **Shift and Advance**:
        *   `n >>= 1` performs a right bit shift on `n`. This effectively discards the LSB that was just processed and moves the next bit into the LSB position for the next iteration.
        *   `index += 1` increments the bit position counter.

3.  **Return Result**: Once `n` becomes 0 (all bits have been processed), the loop terminates, and the function returns the `[even_count, odd_count]` list.

---

### 3. Key Design Decisions

*   **Iterative Bit Processing**: The core design uses a `while` loop and bitwise operations (`&`, `>>=`) to efficiently extract and process each bit from right to left (LSB to MSB). This is a standard and highly optimized approach for bit manipulation.
*   **0-Based Indexing**: The problem inherently assumes 0-based indexing for bit positions, starting from the rightmost bit. The `index` variable correctly reflects this.
*   **Simple Counters**: Two integer variables are sufficient to store the counts, requiring minimal memory.

---

### 4. Complexity

*   **Time Complexity**: O(log N) or O(B)
    *   The `while` loop runs once for each bit in the input integer `n`.
    *   The number of bits required to represent `n` is proportional to `log2(n)`. For a fixed-size integer (e.g., 32-bit or 64-bit), this is a constant number of iterations (B).
    *   Each operation inside the loop (bitwise AND, comparison, modulo, addition, bit shift) takes O(1) time.
    *   Therefore, the total time complexity is proportional to the number of bits in `n`.
*   **Space Complexity**: O(1)
    *   The function uses a fixed number of variables (`even_count`, `odd_count`, `index`, `n`) regardless of the size of the input `n`. These variables occupy constant memory space.

---

### 5. Edge Cases & Correctness

*   **`n = 0`**:
    *   The `while n > 0` condition is immediately false.
    *   The function correctly returns `[0, 0]`, as 0 has no set bits.
*   **`n = 1` (binary `...001`)**:
    *   `index = 0`. `n & 1` is 1. `index % 2 == 0` is true. `even_count` becomes 1.
    *   `n` becomes 0. `index` becomes 1.
    *   Loop terminates. Returns `[1, 0]`. Correct.
*   **`n = 2` (binary `...010`)**:
    *   Loop 1 (`index = 0`): `n & 1` is 0. No count. `n` becomes 1, `index` becomes 1.
    *   Loop 2 (`index = 1`): `n & 1` is 1. `index % 2 == 0` is false (`1 % 2` is 1). `odd_count` becomes 1.
    *   `n` becomes 0. `index` becomes 2.
    *   Loop terminates. Returns `[0, 1]`. Correct.
*   **Large `n`**: Python integers handle arbitrary precision. The algorithm correctly processes all bits of `n` until it reduces to 0, ensuring correctness for any positive integer input.

---

### 6. Improvements & Alternatives

*   **Readability/Conciseness**: The current code is already very readable and idiomatic for bit manipulation.
    *   A minor stylistic alternative for updating counts could be to use a list `counts = [0, 0]` and update `counts[index % 2] += 1` instead of separate `if/else` for `even_count`/`odd_count`. This is a small refactor and doesn't change logic or complexity.

    ```python
    class Solution:
        def evenOddBit(self, n: int) -> List[int]:
            counts = [0, 0] # counts[0] for even, counts[1] for odd
            index = 0

            while n > 0:
                if (n & 1) == 1:
                    counts[index % 2] += 1
                n >>= 1
                index += 1
            return counts
    ```

*   **Performance**: For the specific problem of counting bits based on parity, this bitwise approach is generally the most performant and direct method. Alternatives involving string conversions (e.g., `bin(n)`) would incur significant overhead.

---

### 7. Security/Performance Notes

*   **Performance**: The use of bitwise operations (`&`, `>>=`) is highly optimized at the CPU level, making this solution very efficient for its purpose. For fixed-width integer types (like `int` in C++/Java), this algorithm executes in a constant number of cycles. Python's arbitrary-precision integers mean the performance scales with the actual number of bits required by `n`, which is appropriate and efficient for the language.
*   **Security**: There are no inherent security concerns with this code. It operates solely on an integer input, performs mathematical operations, and returns a simple list of integers. It does not interact with external systems, files, or user input in a way that could introduce vulnerabilities.

### Code:
```python
class Solution:
    def evenOddBit(self, n: int) -> List[int]:
        even_count = 0
        odd_count = 0
        index = 0

        while n > 0:
            if (n & 1) == 1:  # Check if the current rightmost bit is 1
                if index % 2 == 0:  # Even index
                    even_count += 1
                else:  # Odd index
                    odd_count += 1
            n >>= 1  # Right shift n to process the next bit
            index += 1  # Increment index

        return [even_count, odd_count]
```
