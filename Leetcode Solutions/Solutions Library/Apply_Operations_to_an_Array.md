## Apply Operations to an Array
**Language:** python
**Tags:** python,oop,two-pointers,arrays

### Description:
### 1. Overview & Intent

This code defines a method `applyOperations` that transforms a list of integers `nums` in two distinct phases:

*   **Phase 1: Apply Doubling Operations**: It iterates through the list, and if two adjacent elements are equal, it doubles the first one and sets the second one to zero. This process is sequential, meaning a modified value can participate in subsequent comparisons.
*   **Phase 2: Shift Zeros to End**: After all doubling operations, it rearranges the list such that all non-zero elements appear first, maintaining their relative order, followed by all zero elements at the end.

The overall intent is to perform these specific transformations efficiently and in-place on the input array.

### 2. How It Works

The method executes in two main parts:

*   **Part 1: Applying Operations (`for i in range(n - 1):`)**
    *   A single loop iterates from the first element up to the second-to-last element (`i` from `0` to `n-2`).
    *   In each iteration, it checks if `nums[i]` is equal to `nums[i+1]`.
    *   If they are equal, `nums[i]` is doubled (`nums[i] *= 2`), and `nums[i+1]` is set to `0`.
    *   This sequential application means that if `nums[i]` becomes `0` due to a previous operation (e.g., `nums[i-1]` matched `nums[i]`), it will not match `nums[i+1]` in the current step unless `nums[i+1]` is also `0`. Similarly, if `nums[i]` doubles, that new value is used for comparison with `nums[i+1]` if the loop were structured differently (but here it's `i` vs `i+1`, so `nums[i+1]` is not yet processed). The current `nums[i]` is modified and then `nums[i+1]` is zeroed out.

*   **Part 2: Shifting Zeros to the End (Two-Pointer Approach)**
    *   This part uses two pointers: `read_idx` and `write_idx`.
    *   `write_idx` starts at `0` and points to the next available position for a non-zero element.
    *   `read_idx` iterates through the entire array from `0` to `n-1`.
    *   If `nums[read_idx]` is **not** zero, it means it's a valid non-zero element that needs to be preserved. This element is copied to `nums[write_idx]`, and `write_idx` is then incremented.
    *   If `nums[read_idx]` **is** zero, it is skipped. This effectively means non-zero elements are "compacted" to the front of the array.
    *   After the `read_idx` loop finishes, all non-zero elements are placed in `nums[0]` up to `nums[write_idx - 1]`, maintaining their original relative order.
    *   Finally, a `while` loop fills the remaining positions from `write_idx` to `n-1` with zeros, ensuring all trailing elements are indeed zeros.

### 3. Key Design Decisions

*   **In-Place Modification**: The code directly modifies the input list `nums`. This is a common requirement in competitive programming to save memory and is achieved through careful indexing and swapping logic.
*   **Two-Pass Approach**: Separating the operations into two distinct passes (doubling/zeroing, then shifting) simplifies the logic considerably. Trying to do both in a single pass would be much more complex and error-prone.
*   **Two-Pointer for Zero Shifting**: The "read" and "write" pointer technique is a classic and efficient algorithm for in-place array manipulation, particularly for moving specific elements (like zeros) to one end while preserving the order of others.
*   **Sequential Application of Operations**: The problem's phrasing implies that operations on `nums[i]` and `nums[i+1]` are performed in sequence, and the modified `nums[i]` might affect future operations *if* the next comparison involved `nums[i]` again (which it doesn't in this `i` vs `i+1` structure). This ensures deterministic behavior.

### 4. Complexity

*   **Time Complexity: O(N)**
    *   **Part 1 (Doubling Operations)**: The `for` loop iterates `n - 1` times. Each iteration involves a constant number of comparisons and assignments. Thus, this part is O(N).
    *   **Part 2 (Shifting Zeros)**: The first `for` loop (for `read_idx`) iterates `n` times. The subsequent `while` loop (for filling zeros) iterates at most `n` times. Both loops contribute linearly to the total time. Thus, this part is O(N).
    *   **Total**: The sum of linear operations results in an overall O(N) time complexity.
*   **Space Complexity: O(1)**
    *   The code performs all operations in-place on the input `nums` list.
    *   A constant number of auxiliary variables (`n`, `i`, `write_idx`, `read_idx`) are used, irrespective of the input size.
    *   Therefore, the auxiliary space complexity is O(1).

### 5. Edge Cases & Correctness

The code handles several edge cases correctly:

*   **Empty Array (`[]`)**: `n` will be 0. Both `for` loops and the `while` loop for filling zeros will not execute. The original empty list `nums` is returned, which is correct.
*   **Single Element Array (`[5]`)**: `n` will be 1. The first `for` loop (`range(n-1)` which is `range(0)`) won't run. In Part 2, `write_idx` will become 1, and the `while write_idx < n` loop (`while 1 < 1`) won't run. The original `[5]` is returned, which is correct.
*   **Array with No Operations (`[1, 2, 3, 4]`)**: Part 1 will find no matching adjacent elements. Part 2 will copy all elements to their original positions (`write_idx` will reach `n`), and no zeros will be filled. Correctly returns `[1, 2, 3, 4]`.
*   **Array with All Zeros (`[0, 0, 0]`)**: Part 1 does nothing. Part 2 will find no non-zero elements, `write_idx` remains 0. The `while` loop fills the entire array with zeros. Correctly returns `[0, 0, 0]`.
*   **Array with All Same Non-Zero Elements (`[2, 2, 2, 2]`)**:
    *   `i=0`: `[4, 0, 2, 2]`
    *   `i=1`: `[4, 0, 4, 0]` (Note: `nums[1]` is now `0`, so `nums[1] == nums[2]` is false, but `nums[2]` was `2`, now `nums[2]` becomes `4` if `nums[1]` was still `2`) - *Correction*: `nums[1]` is `0`, so `nums[1] == nums[2]` (`0 == 2`) is false. The operation only occurs once.
    *   Let's trace `[2, 2, 2, 2]` carefully:
        *   `i = 0`: `nums[0] == nums[1]` (2 == 2) -> `nums` becomes `[4, 0, 2, 2]`
        *   `i = 1`: `nums[1] == nums[2]` (0 == 2) is false.
        *   `i = 2`: `nums[2] == nums[3]` (2 == 2) -> `nums` becomes `[4, 0, 4, 0]`
    *   After Part 1: `[4, 0, 4, 0]`
    *   Part 2 shifts: `[4, 4, 0, 0]`. This is correct according to the problem statement.

The sequential nature of Part 1 (iterating `i` from left to right) is critical. If `nums[i]` is changed to `0`, it won't participate in a future `nums[i] == nums[i+1]` match, ensuring each original pair is considered only once for the doubling/zeroing rule.

### 6. Improvements & Alternatives

*   **Combined Zero-Filling Loop (Minor)**: The final `while write_idx < n:` loop to fill remaining elements with zeros is functionally correct and clear. It could potentially be merged into the `for read_idx` loop by initializing the entire array with zeros first if one wanted to guarantee all unwritten spots are zero from the start, but the current explicit filling is good for robustness, ensuring no stale data remains in the tail.
    ```python
    # Current approach: clear and correct
    # After the read_idx loop
    while write_idx < n:
        nums[write_idx] = 0
        write_idx += 1
    ```
    This approach is already quite optimal and clear for its purpose.
*   **Functional Style (Alternative)**: For situations where immutability is preferred or side effects are to be avoided, one could create a new list. This would come at the cost of O(N) additional space.
    ```python
    # Example of a functional approach (not for this problem's typical constraints)
    # result = []
    # for x in processed_nums_after_part1:
    #     if x != 0:
    #         result.append(x)
    # result.extend([0] * (n - len(result)))
    # return result
    ```
    However, given the common patterns in problems like these, the in-place solution is usually expected and preferred for efficiency.

### 7. Security/Performance Notes

*   **Security**: There are no apparent security concerns. The code operates purely on an integer list, involves no external interactions, file I/O, network communication, or user input that could lead to vulnerabilities.
*   **Performance**: The code achieves optimal O(N) time complexity and O(1) auxiliary space complexity. This is the best possible performance for this problem, as every element must be inspected at least once. There are no obvious bottlenecks or inefficiencies.

### Code:
```python
class Solution:
    def applyOperations(self, nums: List[int]) -> List[int]:
        n = len(nums)

        # Part 1: Apply operations sequentially
        for i in range(n - 1):
            if nums[i] == nums[i + 1]:
                nums[i] *= 2
                nums[i + 1] = 0

        # Part 2: Shift all zeros to the end of the array
        # Use a two-pointer approach for in-place shifting
        write_idx = 0  # Pointer for placing non-zero elements
        for read_idx in range(n):
            if nums[read_idx] != 0:
                nums[write_idx] = nums[read_idx]
                write_idx += 1
        
        # Fill the remaining elements from write_idx to n-1 with zeros
        while write_idx < n:
            nums[write_idx] = 0
            write_idx += 1
            
        return nums
```
