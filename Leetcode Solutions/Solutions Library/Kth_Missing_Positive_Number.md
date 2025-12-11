## Kth Missing Positive Number
**Language:** python
**Tags:** python,oop,binary search,array

### Description:
This code implements an efficient algorithm to find the Kth missing positive integer in a strictly increasing array of positive integers.

---

### 1. Overview & Intent

This Python code defines a method `findKthPositive` within a `Solution` class. Its purpose is to determine the *k*-th positive integer that is missing from the provided sorted array `arr`. The array `arr` contains positive integers and is guaranteed to be strictly increasing.

---

### 2. How It Works

The algorithm uses a binary search approach to locate the "breakpoint" in the array where the `k`-th missing number would fall.

*   **Initialization**: `low` and `high` pointers are set to the start and end of the array, respectively.
*   **Binary Search Loop**: The loop continues as long as `low` is less than or equal to `high`.
    *   **Midpoint Calculation**: `mid` is calculated using `low + (high - low) // 2` to prevent potential integer overflow, though less critical in Python.
    *   **Missing Count Calculation**: The core of the algorithm is `missing_count_at_mid = arr[mid] - (mid + 1)`.
        *   `mid + 1` represents the expected value of `arr[mid]` if there were *no* missing positive integers up to that index. For example, at `mid=0`, the expected value is `1`; at `mid=1`, it's `2`, and so on.
        *   `arr[mid] - (mid + 1)` therefore gives the *actual count* of positive integers missing *before or at* the current `arr[mid]` position.
    *   **Decision Logic**:
        *   If `missing_count_at_mid < k`: This means that the `k`-th missing number is *after* `arr[mid]`. We need to search in the right half, so `low` is updated to `mid + 1`.
        *   If `missing_count_at_mid >= k`: This implies that the `k`-th missing number is *at or before* `arr[mid]` (or more precisely, its position would be determined by numbers up to this point). We need to search in the left half, so `high` is updated to `mid - 1`.
*   **Post-Loop Result**:
    *   When the loop terminates, `low` will point to the smallest index such that `arr[low] - (low + 1)` (the count of missing numbers up to `arr[low]`) is greater than or equal to `k`.
    *   The formula `k + low` then directly calculates the `k`-th missing positive integer. This works because `low` effectively tells us how many numbers from `1` to `low` are *not* present in the array before the position where the `k`-th missing number would be found. The `k`-th missing number is simply `k` plus this offset.

---

### 3. Key Design Decisions

*   **Binary Search**: The most crucial design decision is the use of binary search. This capitalizes on the sorted nature of the input array `arr` to achieve logarithmic time complexity.
*   **Missing Count Formula**: The derivation of `arr[mid] - (mid + 1)` is an elegant way to determine the number of missing positive integers up to a given point in the array without explicitly iterating or storing them.
*   **Implicit Range Search**: Instead of searching for the *value* of the k-th missing number, the binary search searches for the *index* in the input array that "bounds" the k-th missing number.

---

### 4. Complexity

*   **Time Complexity**: O(log N)
    *   The algorithm performs a binary search on the input array of size N (`len(arr)`). Each step of the binary search halves the search space.
*   **Space Complexity**: O(1)
    *   Only a few constant extra variables (`low`, `high`, `mid`, `missing_count_at_mid`) are used, regardless of the input array size.

---

### 5. Edge Cases & Correctness

The algorithm correctly handles several edge cases:

*   **Empty Array (`arr = []`)**:
    *   `low = 0`, `high = -1`. The `while low <= high` condition `(0 <= -1)` is immediately false.
    *   The loop is skipped. `return k + low` becomes `k + 0 = k`. This is correct, as if `arr` is empty, the `k`-th missing positive integer is simply `k` (e.g., for `k=3`, the missing numbers are 1, 2, 3, ... so the 3rd missing is 3).
*   **`k` is smaller than the first missing number**:
    *   E.g., `arr = [2, 3, 4]`, `k = 1`. The 1st missing is `1`.
    *   The algorithm correctly identifies `low = 0` at the end and returns `1 + 0 = 1`.
*   **`k` is larger than any missing number within the array's range**:
    *   E.g., `arr = [1, 2, 3]`, `k = 1`. All numbers up to 3 are present. The 1st missing positive integer is `4`.
    *   The `missing_count_at_mid` will always be 0. `low` will eventually become `len(arr)` (which is 3).
    *   `return k + low` becomes `1 + 3 = 4`. Correct.
*   **`k` falls exactly between two numbers in `arr`**:
    *   E.g., `arr = [1, 5]`, `k = 2`. Missing numbers are 2, 3, 4, ...
    *   `mid=0, arr[0]=1, missing=0`. `0 < k` (0 < 2). `low=1`.
    *   `low=1, high=1`. `mid=1, arr[1]=5, missing = 5-(1+1) = 3`. `3 >= k` (3 >= 2). `high=0`.
    *   Loop ends. `low=1`. `return k + low = 2 + 1 = 3`. Correct. (The 2nd missing is 3).
*   **Large `k` values**: The formula `k + low` naturally scales with `k`, ensuring correctness even for very large `k` values.

---

### 6. Improvements & Alternatives

*   **Readability**: The core formula `arr[mid] - (mid + 1)` is concise but can be initially challenging to understand without explanation. The existing comments are helpful. For a non-competitive setting, a more verbose helper function or a different variable name for `mid + 1` (e.g., `expected_val_at_mid`) could enhance clarity.
*   **Linear Scan (Alternative)**: A simpler, but less efficient, approach would be to iterate through `arr` and count missing numbers:
    ```python
    current_num = 1
    missing_count = 0
    arr_idx = 0

    while missing_count < k:
        if arr_idx < len(arr) and arr[arr_idx] == current_num:
            arr_idx += 1
        else:
            missing_count += 1
        current_num += 1

    return current_num - 1
    ```
    This linear approach has O(N + K) time complexity in the worst case, which is worse than O(log N) if N is large.
*   **Set-based approach (Alternative for unsorted array)**: If the array were not sorted, one could put all numbers into a set and then iterate `1, 2, 3, ...` checking if each number is in the set. This would be O(N) to build the set and O(K) to find the k-th missing, so O(N+K) time and O(N) space. This is not applicable here given the problem constraints (sorted array).

---

### 7. Security/Performance Notes

*   **Security**: There are no apparent security vulnerabilities in this code. It performs numerical computation on input integers without external interactions or sensitive data handling.
*   **Performance**: The performance is optimal for the given problem constraints (sorted array) due to the O(log N) time complexity. No obvious performance bottlenecks exist. The integer arithmetic is efficient.

### Code:
```python
class Solution:
    def findKthPositive(self, arr: List[int], k: int) -> int:
        low = 0
        high = len(arr) - 1

        while low <= high:
            mid = low + (high - low) // 2
            
            # Calculate the number of missing positive integers before arr[mid]
            # If arr[mid] were the (mid+1)-th positive integer (no missing before it),
            # its value would be (mid + 1).
            # The actual value is arr[mid].
            # So, the count of missing numbers up to this point is arr[mid] - (mid + 1).
            missing_count_at_mid = arr[mid] - (mid + 1)

            if missing_count_at_mid < k:
                # The k-th missing number is after arr[mid]
                low = mid + 1
            else:
                # The k-th missing number is arr[mid] or before it
                high = mid - 1
        
        # After the loop, 'low' points to the first index where 
        # arr[low] - (low + 1) >= k.
        # This means that the k-th missing number is `k + low`.
        # This formula works for all cases, including when `low` is 0
        # (k-th missing is `k`) or when `low` is `len(arr)`
        # (k-th missing is after the last element).
        return k + low
```
