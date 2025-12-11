## Minimum Swaps to Arrange a Binary Grid
**Language:** python
**Tags:** python,oop,greedy,list

### Description:
This code solves the "Minimum Swaps to Make Grid Valid" problem.

---

### 1. Overview & Intent

The primary goal of the `minSwaps` function is to transform a given `n x n` binary grid into a specific valid state using the minimum number of row swaps.

A grid is considered "valid" if, for every row `i` (0-indexed):
*   Row `0` needs at least `n-1` trailing zeros (i.e., `grid[0][1]` through `grid[0][n-1]` must be zero).
*   Row `1` needs at least `n-2` trailing zeros.
*   ...
*   Row `i` needs at least `n-1-i` trailing zeros.
*   ...
*   Row `n-1` needs at least `0` trailing zeros.

The function returns the minimum number of swaps required to achieve this state. If it's impossible, it returns `-1`.

---

### 2. How It Works

The solution employs a greedy strategy:

1.  **Pre-calculate Trailing Zeros**:
    *   It first iterates through each row of the input `grid`.
    *   For each row, it counts the number of consecutive zeros starting from the rightmost column. This count is stored in a list called `trailing_zeros_counts`.
    *   Example: `[1,0,0]` has 2 trailing zeros; `[0,1,0]` has 1; `[1,1,1]` has 0.

2.  **Greedy Row Placement**:
    *   The code then iterates from `i = 0` to `n-1` (representing the target position for each row).
    *   For each target position `i`, it determines the `required_trailing_zeros` (which is `n - 1 - i`).
    *   It searches through the *remaining* rows (from index `i` onwards in the `trailing_zeros_counts` list) to find the *first* row `j` that satisfies the requirement (`trailing_zeros_counts[j] >= required_trailing_zeros`).
    *   **If no suitable row is found**: It means the grid cannot be made valid, so the function returns `-1`.
    *   **If a suitable row `j` is found**:
        *   The number of swaps needed to bring this row `j` to position `i` is `j - i`. This value is added to the total `ans`.
        *   To simulate the actual swap and ensure subsequent searches are correct, the `trailing_zeros_counts` list is updated: the value at `found_idx` is `pop`ped and then `insert`ed at position `i`. This effectively shifts all rows between `i` and `found_idx-1` one position down.

3.  **Return Result**:
    *   After iterating through all target positions, the accumulated `ans` represents the minimum total swaps.

---

### 3. Key Design Decisions

*   **Greedy Approach**: The core decision is to use a greedy strategy. For each target row `i`, it finds the *first* available row (starting from `i`) that satisfies the current trailing zero requirement. This choice is optimal because moving a row from `j` to `i` (where `j > i`) minimizes the number of adjacent swaps for that step (`j - i`) and only shifts rows `i` through `j-1` by one position, preserving their relative order. This doesn't negatively impact future decisions for rows `k > i`.
*   **Abstraction with `trailing_zeros_counts`**: Instead of manipulating the actual `grid` (which would be complex and slow due to deep copying or managing row references), the solution extracts the essential property (number of trailing zeros) into a simple list. This simplifies the logic significantly.
*   **`list.pop()` and `list.insert()` for Simulation**: These Python list operations are used to simulate the row swaps on the `trailing_zeros_counts` list. While `pop(idx)` and `insert(idx, val)` are `O(N)` operations in Python lists, they are crucial for correctly updating the list's state for subsequent iterations of the greedy algorithm. This correctly models the shifting of rows that occurs during a swap.

---

### 4. Complexity

Let `n` be the number of rows (and columns) in the grid.

*   **Time Complexity**: `O(n^2)`
    *   **Step 1 (Calculate trailing zeros)**: There are `n` rows. For each row, we iterate up to `n` columns. This takes `O(n * n) = O(n^2)` time.
    *   **Step 2 (Greedy placement)**:
        *   The outer loop runs `n` times (for `i` from `0` to `n-1`).
        *   Inside the outer loop:
            *   The inner loop (finding `found_idx`) iterates up to `n` times.
            *   The `trailing_zeros_counts.pop(found_idx)` and `trailing_zeros_counts.insert(i, val_to_move)` operations each take `O(n)` time because they involve shifting elements in a list.
        *   Therefore, the second step is `n * (O(n) + O(n)) = O(n^2)` time.
    *   **Overall**: `O(n^2) + O(n^2) = O(n^2)`.

*   **Space Complexity**: `O(n)`
    *   The `trailing_zeros_counts` list stores `n` integers. This requires `O(n)` space.
    *   No other data structures consume significant space relative to `n`.

---

### 5. Edge Cases & Correctness

*   **`n = 1` (1x1 grid)**:
    *   `grid = [[0]]`: `trailing_zeros_counts = [1]`. `i=0`, `required=0`. `found_idx=0`. `ans = 0`. Correct.
    *   `grid = [[1]]`: `trailing_zeros_counts = [0]`. `i=0`, `required=0`. `found_idx=0`. `ans = 0`. Correct.
*   **Impossible Case**: If at any step `i`, no row from `i` to `n-1` satisfies the `required_trailing_zeros`, the function correctly returns `-1`. This implies it's impossible to form the valid grid.
*   **Already Valid Grid**: If the grid is already valid, `found_idx` will always be `i`, and `ans` will remain `0`. Correct.
*   **Correctness of Greedy Choice**: The greedy strategy works because the requirement for row `i` (having `n-1-i` trailing zeros) only depends on `i`, not on the specific content of other rows. By picking the first available valid row, we minimize the swaps for that particular step without jeopardizing the ability to satisfy requirements for subsequent rows. The `pop`/`insert` correctly maintains the relative order of the remaining rows.

---

### 6. Improvements & Alternatives

*   **Optimization for List Operations**: The `O(n)` cost of `pop()` and `insert()` dominates the inner loop. While `O(n^2)` is often acceptable for competitive programming constraints (N up to 1000-2000), for larger `N`, this could be a bottleneck.
    *   **Alternative Data Structure**: Instead of a Python list, if we were working with a language that offers linked lists with `O(1)` removal/insertion at arbitrary points (after finding the node), this could be faster. However, finding the `found_idx` would still be `O(N)`.
    *   **Fenwick Tree (BIT) / Segment Tree**: If the problem was slightly different (e.g., only needed to count the number of elements moved, and not their actual values/properties), a Fenwick tree could find the number of "inversions" or swaps more efficiently. However, here we need to find *and remove* a specific element to update future searches. The current `pop`/`insert` directly models the physical swap of rows.
*   **Readability**: The code is well-structured and commented. No major readability improvements are necessary.

---

### 7. Security/Performance Notes

*   **Security**: There are no apparent security concerns. The code processes in-memory data, does not handle external input beyond the grid structure, and performs no file I/O, network communication, or sensitive operations.
*   **Performance**: The `O(N^2)` time complexity is generally considered efficient for `N` up to a few thousand. For example, if `N=2000`, `N^2` is `4 * 10^6` operations, which typically completes within a few seconds on modern CPUs. For very large grids (`N > 10^4`), an `O(N^2)` solution would be too slow, and a more advanced data structure (like those mentioned in improvements) would be needed if one could be adapted to this problem's specific requirements.

### Code:
```python
import collections
from typing import List

class Solution:
    def minSwaps(self, grid: List[List[int]]) -> int:
        n = len(grid)
        
        # Step 1: Calculate the number of trailing zeros for each row
        # A trailing zero is a zero at the end of the row.
        # For example, [1,0,0] has 2 trailing zeros. [0,1,0] has 1. [1,1,1] has 0.
        trailing_zeros_counts = []
        for r in range(n):
            count = 0
            # Iterate from the rightmost column to the left
            for c in range(n - 1, -1, -1):
                if grid[r][c] == 0:
                    count += 1
                else:
                    break # Stop counting if we encounter a '1'
            trailing_zeros_counts.append(count)
            
        ans = 0
        
        # Step 2: Greedily place rows into their correct positions
        # For each target row index 'i' (from 0 to n-1)
        for i in range(n):
            # The i-th row (0-indexed) needs to have at least (n - 1 - i) trailing zeros
            # This is because the cells grid[i][i+1], ..., grid[i][n-1] must be zero.
            required_trailing_zeros = n - 1 - i
            
            # Find the first available row (starting from the current position 'i')
            # that satisfies the required number of trailing zeros.
            found_idx = -1
            for j in range(i, n):
                if trailing_zeros_counts[j] >= required_trailing_zeros:
                    found_idx = j
                    break
            
            # If no suitable row is found among the remaining rows, it's impossible
            # to make the grid valid.
            if found_idx == -1:
                return -1
            
            # The number of swaps needed to bring the found row from 'found_idx'
            # to the target position 'i' is the difference in their indices.
            # This is because each adjacent swap moves the row one position.
            ans += (found_idx - i)
            
            # Simulate the swap: remove the found row's trailing zero count
            # from its current position and insert it at the target position 'i'.
            # This operation correctly models the shifting of rows between 'i' and 'found_idx-1'
            # downwards by one position.
            val_to_move = trailing_zeros_counts.pop(found_idx)
            trailing_zeros_counts.insert(i, val_to_move)
            
        return ans
```
