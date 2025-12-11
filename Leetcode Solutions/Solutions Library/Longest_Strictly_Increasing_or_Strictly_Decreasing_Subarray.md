## Longest Strictly Increasing or Strictly Decreasing Subarray
**Language:** python
**Tags:** python,dynamic programming,list,imperative

### Description:


---

### 1. Overview & Intent

*   **Problem**: The function `longestMonotonicSubarray` aims to find the maximum length of a *strictly* monotonic subarray within a given array of integers (`nums`).
*   **Monotonicity**: A subarray is strictly monotonic if its elements are either strictly increasing (each element is greater than the previous) or strictly decreasing (each element is less than the previous).
*   **Goal**: Return the length of the longest such subarray.

### 2. How It Works

The code uses a single-pass iterative approach to track the lengths of current strictly increasing and strictly decreasing subarrays.

1.  **Initialization**:
    *   `n`: Stores the length of the input array `nums`.
    *   **Base Case**: If `n` is 0 or 1, the entire array is considered monotonic, so its length `n` is returned directly.
    *   `max_len`: Initialized to 1, as a single element is always a monotonic subarray of length 1.
    *   `current_inc_len`: Tracks the length of the strictly increasing subarray ending at the current position, initialized to 1.
    *   `current_dec_len`: Tracks the length of the strictly decreasing subarray ending at the current position, initialized to 1.

2.  **Iteration**:
    *   The code iterates through the `nums` array starting from the second element (`i` from 1 to `n-1`).
    *   **Checking Increasing Subarray**:
        *   If `nums[i]` is strictly greater than `nums[i-1]`, it extends the current increasing sequence. `current_inc_len` is incremented.
        *   Otherwise (if `nums[i] <= nums[i-1]`), the increasing sequence is broken, so `current_inc_len` is reset to 1 (starting a new potential sequence with `nums[i]`).
    *   **Checking Decreasing Subarray**:
        *   If `nums[i]` is strictly less than `nums[i-1]`, it extends the current decreasing sequence. `current_dec_len` is incremented.
        *   Otherwise (if `nums[i] >= nums[i-1]`), the decreasing sequence is broken, so `current_dec_len` is reset to 1.
    *   **Updating Maximum**: After updating both current lengths, `max_len` is updated to be the maximum of `max_len`, `current_inc_len`, and `current_dec_len`.

3.  **Result**: After the loop completes, `max_len` holds the maximum length found for any strictly monotonic subarray, which is then returned.

### 3. Key Design Decisions

*   **Algorithm**: Greedy, single-pass traversal. This approach processes each element once, updating state variables based on the current and previous element.
*   **Data Structures**: Uses only primitive integer variables (`n`, `max_len`, `current_inc_len`, `current_dec_len`). No complex data structures like stacks, queues, or hash maps are required.
*   **Trade-offs**:
    *   **Pros**: Extremely efficient in terms of both time and space complexity due to its direct, iterative nature. Simple to understand and implement.
    *   **Cons**: No significant downsides for this problem. The solution is optimal.

### 4. Complexity

*   **Time Complexity**: O(n)
    *   The code iterates through the `nums` array once, from the second element to the end. The loop runs `n-1` times.
    *   Inside the loop, all operations (comparisons, additions, `max` calls) are constant time.
    *   Therefore, the overall time complexity is linear with respect to the number of elements in the input array.
*   **Space Complexity**: O(1)
    *   Only a fixed number of auxiliary variables are used (`n`, `max_len`, `current_inc_len`, `current_dec_len`, `i`), regardless of the input array size.
    *   No additional data structures that scale with `n` are allocated.

### 5. Edge Cases & Correctness

*   **Empty list (`[]`)**: `n = 0`. The base case `if n <= 1: return n` correctly returns `0`.
*   **Single element list (`[5]`)**: `n = 1`. The base case `if n <= 1: return n` correctly returns `1`.
*   **Two elements (`[1, 2]`, `[2, 1]`, `[1, 1]`)**:
    *   `[1, 2]`: `current_inc_len` becomes 2, `max_len` becomes 2. Correct.
    *   `[2, 1]`: `current_dec_len` becomes 2, `max_len` becomes 2. Correct.
    *   `[1, 1]`: Both `current_inc_len` and `current_dec_len` reset to 1 because `nums[i]` is neither strictly greater nor strictly less. `max_len` remains 1. Correct.
*   **All identical elements (`[7, 7, 7, 7]`)**: Both `current_inc_len` and `current_dec_len` will be reset to 1 in each iteration because elements are not *strictly* increasing or decreasing. `max_len` will remain `1`. Correct.
*   **Strictly increasing (`[1, 2, 3, 4, 5]`)**: `current_inc_len` will grow up to 5, `current_dec_len` will stay 1. `max_len` will correctly become 5.
*   **Strictly decreasing (`[5, 4, 3, 2, 1]`)**: `current_dec_len` will grow up to 5, `current_inc_len` will stay 1. `max_len` will correctly become 5.
*   **Mixed sequence with plateaus (`[1, 2, 2, 3, 1, 0]`)**:
    *   `[1, 2]`: inc=2, dec=1. max=2.
    *   `[2, 2]`: inc=1, dec=1 (resets). max=2.
    *   `[2, 3]`: inc=2, dec=1. max=2.
    *   `[3, 1]`: inc=1, dec=2. max=2.
    *   `[1, 0]`: inc=1, dec=3. max=3. (Final answer 3 from `[3,1,0]`) Correct.

The logic handles all these cases correctly due to the clear reset conditions for `current_inc_len` and `current_dec_len`.

### 6. Improvements & Alternatives

*   **Readability**: The variable names (`current_inc_len`, `current_dec_len`) are descriptive and clear. The code is already quite readable. No significant readability improvements are needed.
*   **Alternative Approaches**:
    *   While dynamic programming could technically be framed for this problem, the current greedy approach essentially *is* the most optimized DP solution by maintaining only the current state variables. A formal DP table would consume O(N) space without providing any algorithmic benefit here.
    *   No other fundamentally different and more efficient algorithms exist for this problem, as every element must be inspected at least once.

### 7. Security/Performance Notes

*   **Security**: No security concerns. The code operates purely on numerical input, without external calls, file I/O, network access, or complex data manipulations that might introduce vulnerabilities.
*   **Performance**: The solution is optimally performant with O(N) time complexity and O(1) space complexity. It's not possible to solve this problem faster than O(N) because, at minimum, every element in the array must be examined to determine the longest monotonic subarray.

### Code:
```python
class Solution:
    def longestMonotonicSubarray(self, nums: List[int]) -> int:
        n = len(nums)
        if n <= 1:
            return n

        max_len = 1
        current_inc_len = 1
        current_dec_len = 1

        for i in range(1, n):
            if nums[i] > nums[i-1]:
                current_inc_len += 1
            else:
                current_inc_len = 1

            if nums[i] < nums[i-1]:
                current_dec_len += 1
            else:
                current_dec_len = 1
            
            max_len = max(max_len, current_inc_len, current_dec_len)
        
        return max_len
```
