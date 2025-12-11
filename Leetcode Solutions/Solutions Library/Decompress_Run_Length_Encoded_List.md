## Decompress Run Length Encoded List
**Language:** python
**Tags:** python,oop,list

### Description:
This Python code implements a function to decompress a Run-Length Encoded (RLE) list.

### 1. Overview & Intent

The primary goal of this code is to take a list of numbers representing a Run-Length Encoded sequence and decompress it into its original, expanded form. In RLE, pairs of numbers `[freq, val]` indicate that `val` should appear `freq` times consecutively.

**Example:**
*   Input: `[1, 2, 3, 4]`
*   Meaning: `1` time '2', followed by `3` times '4'.
*   Output: `[2, 4, 4, 4]`

### 2. How It Works

The function processes the input list `nums` in pairs:
1.  It initializes an empty list, `decompressed_list`, to store the result.
2.  It iterates through the input list `nums` using a `for` loop with a step of 2 (`range(0, len(nums), 2)`). This ensures that `i` always points to the start of a `[freq, val]` pair.
3.  In each iteration:
    *   `freq` is assigned the value at `nums[i]`.
    *   `val` is assigned the value at `nums[i+1]`.
    *   A temporary list `[val] * freq` is created, which contains `val` repeated `freq` times.
    *   This temporary list's contents are appended (extended) to `decompressed_list`.
4.  After iterating through all pairs, the `decompressed_list` containing the fully decompressed sequence is returned.

### 3. Key Design Decisions

*   **Direct List Construction:** The code directly builds the `decompressed_list` by extending it in each iteration. This is a common and straightforward approach for dynamic list growth.
*   **Iterating by Pairs:** The `range(0, len(nums), 2)` step is crucial for correctly processing the RLE format, ensuring `freq` and `val` are always read together.
*   **`list.extend` with List Multiplication:** Using `decompressed_list.extend([val] * freq)` is an efficient way to add multiple identical elements.
    *   `[val] * freq` creates a new temporary list containing `freq` copies of `val`.
    *   `extend()` then efficiently appends all elements from this temporary list to `decompressed_list`. This is generally more performant than repeatedly calling `append()` in a nested loop.

### 4. Complexity

Let `N` be the length of the input list `nums` and `M` be the total length of the decompressed list (i.e., the sum of all `freq` values).

*   **Time Complexity: O(N + M)**
    *   The `for` loop iterates `N/2` times.
    *   Inside the loop, `[val] * freq` takes `O(freq)` time to create the temporary list.
    *   `decompressed_list.extend(...)` takes `O(freq)` time to copy elements from the temporary list to the `decompressed_list`.
    *   Since these `freq` values sum up to `M` over all iterations, the total time spent creating temporary lists and extending the main list is `O(M)`.
    *   The loop overhead itself is `O(N)`.
    *   Thus, the overall time complexity is dominated by `O(N + M)`.
*   **Space Complexity: O(M)**
    *   `decompressed_list` stores `M` elements, so it requires `O(M)` space.
    *   The temporary lists `[val] * freq` created in each iteration require `O(freq)` space. However, this space is reused in subsequent iterations and is not cumulative for the overall result.

### 5. Edge Cases & Correctness

*   **Empty Input List (`[]`):**
    *   The `range` loop will not execute. `decompressed_list` remains empty.
    *   Returns `[]`. Correct.
*   **Single RLE Pair (e.g., `[3, 5]`):**
    *   Loop runs once. `freq = 3`, `val = 5`.
    *   `decompressed_list.extend([5, 5, 5])`.
    *   Returns `[5, 5, 5]`. Correct.
*   **Zero Frequency (e.g., `[0, 5, 2, 3]`):**
    *   If `freq` is 0, `[val] * 0` results in an empty list `[]`.
    *   `decompressed_list.extend([])` has no effect.
    *   Correctly handles cases where a value should not appear.
*   **`len(nums)` is Odd:**
    *   The problem constraints for RLE typically guarantee that `len(nums)` is even. If it *could* be odd, accessing `nums[i+1]` in the last iteration where `i` is `len(nums) - 1` would result in an `IndexError`. Based on common problem statements for this specific task (e.g., LeetCode), this is usually not an issue.

### 6. Improvements & Alternatives

*   **Pre-allocating List Size (Minor):** If the total `M` (decompressed length) was known upfront, one *could* create a list of that size and fill it. However, calculating `M` would require an initial pass (O(N)), and Python's `list.extend` is already highly optimized for dynamic growth, so this is unlikely to offer significant performance benefits and might even be less readable.
    ```python
    # Example (less readable and potentially not faster for typical use)
    # total_len = sum(nums[i] for i in range(0, len(nums), 2))
    # decompressed_list = [0] * total_len # Pre-allocate
    # current_idx = 0
    # for i in range(0, len(nums), 2):
    #     freq = nums[i]
    #     val = nums[i+1]
    #     for _ in range(freq):
    #         decompressed_list[current_idx] = val
    #         current_idx += 1
    # return decompressed_list
    ```
*   **Generator Function (Memory Efficiency):** If the decompressed list `M` could be extremely large and the consumer only needs to process elements one by one (rather than needing the full list in memory), a generator would be more memory-efficient.
    ```python
    def decompressRLE_generator(self, nums: List[int]):
        for i in range(0, len(nums), 2):
            freq = nums[i]
            val = nums[i+1]
            for _ in range(freq):
                yield val
    # Usage: list(decompressRLE_generator(nums)) or iterate directly
    ```
    This would change the function signature and return type, so it's not a direct drop-in replacement for a function expected to return `List[int]`.

### 7. Security/Performance Notes

*   **Performance:** The current implementation is efficient for its purpose, achieving optimal O(N + M) time and O(M) space complexity. Python's internal list operations are implemented in C, making `extend` and list multiplication `[val] * freq` very fast.
*   **Security:** There are no apparent security vulnerabilities in this code. It performs a direct transformation of numerical data based on simple arithmetic and list operations. No external inputs or complex data processing that could lead to injection or overflow issues are present, assuming the input `nums` are standard integers within Python's capabilities.

### Code:
```python
class Solution:
    def decompressRLElist(self, nums: List[int]) -> List[int]:
        decompressed_list = []
        for i in range(0, len(nums), 2):
            freq = nums[i]
            val = nums[i+1]
            decompressed_list.extend([val] * freq)
        return decompressed_list
```
