## Sort Array By Parity II
**Language:** python
**Tags:** python,two-pointers,array,in-place

### Description:
This Python code implements an efficient in-place algorithm to sort an array by parity, specifically ensuring that even numbers are at even indices and odd numbers are at odd indices.

---

### 1. Overview & Intent

The function `sortArrayByParityII` takes a list of integers `nums` as input. Its primary goal is to rearrange these numbers such that:
*   Every number at an even index (`0, 2, 4, ...`) is even.
*   Every number at an odd index (`1, 3, 5, ...`) is odd.

The problem statement for "Sort Array By Parity II" typically guarantees that the input array `nums` has an even length and an equal number of even and odd integers.

---

### 2. How It Works

The algorithm uses a two-pointer approach to achieve the desired sorting in-place:

1.  **Initialize Pointers**: A pointer `i` starts at index `0` and will exclusively traverse even indices. Another pointer `j` starts at index `1` and will exclusively traverse odd indices.
2.  **Scan for Misplaced Odd Number**: The main loop iterates `i` through all even indices (`0, 2, 4, ..., n-2`).
3.  **Find Misplaced Even Number**: If `nums[i]` (at an even index) is found to be an odd number (which is a violation of the rule), the inner `while` loop starts searching with `j`.
    *   It advances `j` through subsequent odd indices (`1, 3, 5, ...`) until it finds a number `nums[j]` that is even. This `nums[j]` is a misplaced even number (it's at an odd index).
4.  **Swap**: Once an odd number at an even index (`nums[i]`) and an even number at an odd index (`nums[j]`) are found, they are swapped.
5.  **Continue**: The outer loop then continues to the next even index `i`, and `j` remains at its current position, ready to search further if another swap is needed.
6.  **Return**: After `i` has traversed all even indices, the array will be sorted according to parity, and `nums` is returned.

---

### 3. Key Design Decisions

*   **Two-Pointer Approach**: This is the core design choice. Using two independent pointers, one for even indices (`i`) and one for odd indices (`j`), allows for efficient traversal and identification of misplaced elements.
*   **In-Place Modification**: The algorithm directly modifies the input `nums` list, avoiding the need for extra space to store a new array. This is a common optimization for sorting and rearrangement problems.
*   **Targeted Traversal**: `i` only iterates through even indices (`i += 2`) and `j` only searches through odd indices (`j += 2`). This ensures that `i` is always considering an even position and `j` is always considering an odd position, simplifying the parity checks.
*   **Guaranteed Swaps**: The problem's implicit guarantee (equal number of even and odd integers) ensures that for every misplaced odd number at an even index, there *must* be a misplaced even number at an odd index available for a swap, preventing `j` from going out of bounds before finding a suitable candidate.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The outer loop iterates `N/2` times (for `i` over even indices).
    *   The inner `while` loop for `j` might seem like it could lead to `O(N^2)`, but each element in the array is part of at most one swap and `j` only ever moves forward. In total, both `i` and `j` traverse the array at most once. Each element is examined a constant number of times. Therefore, the total operations are proportional to the number of elements `N`.
*   **Space Complexity: O(1)**
    *   The algorithm performs all operations in-place on the input array `nums`. No auxiliary data structures are created that scale with the input size `N`. Only a few constant-space variables (`n`, `i`, `j`) are used.

---

### 5. Edge Cases & Correctness

*   **Empty Array (`nums = []`)**:
    *   `n` becomes `0`. The `for` loop `range(0, 0, 2)` will be empty. The function correctly returns `[]`.
*   **Array of size 2 (e.g., `[4, 1]` or `[1, 4]`)**:
    *   `[4, 1]`: `i=0`, `nums[0]=4` (even). Loop finishes. Returns `[4, 1]`. Correct.
    *   `[1, 4]`: `i=0`, `nums[0]=1` (odd). `j=1`. `nums[1]=4` (even). Swap `nums[0]` and `nums[1]`. `nums` becomes `[4, 1]`. Loop finishes. Returns `[4, 1]`. Correct.
*   **Already Sorted Array (e.g., `[4, 1, 2, 3]`)**:
    *   `i=0`, `nums[0]=4` (even).
    *   `i=2`, `nums[2]=2` (even).
    *   The `if` condition `nums[i] % 2 != 0` is never met. No swaps occur. Returns `[4, 1, 2, 3]`. Correct.
*   **Correctness Guarantee**: The algorithm relies on the problem constraint that `nums` always has an equal number of even and odd integers. This ensures that:
    *   If `nums[i]` is odd (and `i` is even), there *must* exist an even number at an odd index somewhere later in the array for `j` to find and swap with.
    *   `j` will never go out of bounds before finding a suitable swap partner, as long as `i` is within bounds and has found a misplaced odd number.

---

### 6. Improvements & Alternatives

*   **Readability**: The current code is quite readable and clear. The variable names `i` and `j` are standard for pointers, and the logic is straightforward. No significant readability improvements are immediately apparent.
*   **Alternative Approaches**:
    1.  **Using a New Array**: Create a new result array. Use two pointers, one for the next available even index (`even_idx = 0`) and one for the next available odd index (`odd_idx = 1`). Iterate through the input `nums`: if a number is even, place it at `result[even_idx]` and increment `even_idx` by 2; if odd, place it at `result[odd_idx]` and increment `odd_idx` by 2. This is often simpler to reason about but uses O(N) space.
        ```python
        # Example of alternative using O(N) space
        # result = [0] * n
        # even_idx = 0
        # odd_idx = 1
        # for x in nums:
        #     if x % 2 == 0:
        #         result[even_idx] = x
        #         even_idx += 2
        #     else:
        #         result[odd_idx] = x
        #         odd_idx += 2
        # return result
        ```
    2.  **Two-Pass Approach**: One pass to place all even numbers in their correct even slots, potentially leaving odd numbers in even slots. Another pass for odd numbers. This is less efficient than the two-pointer in-place method as it would still likely involve swaps or extra space.

---

### 7. Security/Performance Notes

*   **Performance**: The current solution is optimal in terms of both time (O(N)) and space (O(1)). No further significant performance gains can be achieved for this problem using standard algorithms.
*   **Security**: This code is purely algorithmic and operates on numerical data within memory. It does not handle external input, network communication, file I/O, or sensitive data. Therefore, there are no inherent security concerns applicable to this specific snippet.

### Code:
```python
class Solution:
    def sortArrayByParityII(self, nums: List[int]) -> List[int]:
        n = len(nums)
        j = 1  # Pointer for odd indices

        for i in range(0, n, 2):  # Iterate through even indices
            if nums[i] % 2 != 0:  # If an odd number is found at an even index
                # Find an even number at an odd index to swap with
                while nums[j] % 2 != 0: # While the number at odd index j is also odd
                    j += 2 # Move to the next odd index
                
                # Now nums[j] is even and j is an odd index
                # nums[i] is odd and i is an even index
                nums[i], nums[j] = nums[j], nums[i]
                
        return nums
```
