## Random Pick with Blacklist
**Language:** python
**Tags:** python,oop,hashmap,set,randomization

### Description:
This code provides a solution for picking a random number from a range `[0, n-1]` while excluding a given list of `blacklist` numbers. It optimizes for constant-time random number selection after an initial setup phase.

---

### 1. Overview & Intent

The primary goal of this `Solution` class is to efficiently pick a random number from a set of valid (non-blacklisted) numbers within a predefined range `[0, n-1]`. The design prioritizes extremely fast (O(1)) `pick` operations, achieved through a pre-processing step during initialization. This is a common pattern for "pick random from subset" problems, especially when `pick` is called many times.

### 2. How It Works

1.  **Initialization (`__init__`)**:
    *   Calculates `self.m = n - len(blacklist)`. This `m` represents the total count of valid numbers. The strategy is to map the problem of picking from `n - len(blacklist)` numbers to picking from the range `[0, m-1]`.
    *   Converts `blacklist` into a `blacklist_set` for efficient O(1) average-time lookups.
    *   Initializes `last_valid_num` to `n - 1`. This pointer will be used to find valid (non-blacklisted) numbers from the upper end of the `[0, n-1]` range.
    *   It iterates through each `b_num` in the `blacklist`.
        *   **Key Remapping Logic**: If a blacklisted number `b_num` falls within the effective picking range `[0, m-1]` (i.e., `b_num < self.m`), it needs to be "replaced".
        *   It finds the next available valid number from `[m, n-1]` by decrementing `last_valid_num` until it points to a non-blacklisted number.
        *   This `b_num` is then mapped to the `last_valid_num` in the `self.mapping` dictionary.
        *   `last_valid_num` is decremented again to prepare for the next potential remapping.
    *   Effectively, this process swaps blacklisted numbers in `[0, m-1]` with valid numbers from `[m, n-1]`, conceptually moving all blacklisted numbers out of the `[0, m-1]` range.

2.  **Picking (`pick`)**:
    *   Generates a random integer `idx` uniformly from the range `[0, self.m - 1]` using `random.randrange(self.m)`.
    *   Checks if this `idx` is present as a key in `self.mapping`.
        *   If `idx` is in `self.mapping`, it means `idx` was a blacklisted number within the `[0, m-1]` range, and it was remapped to a valid number from `[m, n-1]`. The remapped value is returned.
        *   If `idx` is *not* in `self.mapping`, it means `idx` itself is a valid (non-blacklisted) number within `[0, m-1]`, so `idx` is returned directly.

### 3. Key Design Decisions

*   **Virtual Range `[0, m-1]`**: The most critical design decision is to create a "virtual" contiguous range `[0, m-1]` of indices from which to pick. This allows `random.randrange` to work efficiently.
*   **Hash Map (`self.mapping`)**: Using a dictionary to store remappings enables O(1) average-case lookups during the `pick` operation. This is fundamental for achieving constant-time picks.
*   **Set for Blacklist (`blacklist_set`)**: Converting the input `blacklist` list to a `set` allows for average O(1) membership testing (`in` operator) during initialization, crucial for efficiently finding `last_valid_num`.
*   **Two-Pointer/Remapping Strategy**: The initialization process uses a two-pointer-like approach (implicit, one iterating through `blacklist`, one `last_valid_num` scanning backwards) to efficiently "move" blacklisted numbers out of the `[0, m-1]` range by mapping them to available non-blacklisted numbers from `[m, n-1]`.

### 4. Complexity

*   **Time Complexity**:
    *   **`__init__`**:
        *   Creating `blacklist_set`: O(B), where B is `len(blacklist)`.
        *   Iterating through `blacklist`: O(B) iterations.
        *   The `while last_valid_num in blacklist_set` loop: While individual checks are O(1) on average, `last_valid_num` is only decremented at most B times in total across all iterations of the outer `for` loop. So, the cumulative cost of the `while` loops is O(B) on average.
        *   **Total `__init__`**: O(B) average-case.
    *   **`pick`**:
        *   `random.randrange(self.m)`: O(1).
        *   `idx in self.mapping`: O(1) on average.
        *   **Total `pick`**: O(1) average-case.

*   **Space Complexity**:
    *   `self.mapping`: Stores at most `min(B, self.m)` entries, representing the blacklisted numbers that fall within `[0, m-1]`. So, O(B) in the worst case.
    *   `blacklist_set`: Stores B entries. So, O(B).
    *   **Total space**: O(B).

### 5. Edge Cases & Correctness

*   **Empty `blacklist`**:
    *   `self.m` becomes `n`.
    *   `blacklist_set` is empty.
    *   The `for` loop in `__init__` does not execute. `self.mapping` remains empty.
    *   `pick` correctly calls `random.randrange(n)` and always returns `idx` directly, effectively picking from `[0, n-1]`. Correct.
*   **`blacklist` contains all numbers (or `n <= len(blacklist)`)**:
    *   If `n <= len(blacklist)`, then `self.m` would be 0 or negative. `random.randrange(0)` would raise a `ValueError`. This scenario is usually prevented by problem constraints (e.g., "it's guaranteed there is at least one non-blacklisted number"). If allowed, an explicit check for `self.m <= 0` and an appropriate error or handling strategy would be needed. Assuming valid constraints.
*   **`blacklist` contains numbers outside `[0, n-1]`**:
    *   Problem constraints typically guarantee `0 <= b_num < n`. If not, `blacklist_set` would contain them, but the `if b_num < self.m` check still correctly filters only the relevant blacklisted numbers for remapping. `last_valid_num` would also only consider `[0, n-1]`. Correct under typical assumptions.
*   **All blacklisted numbers are `>= self.m`**:
    *   The `for` loop `if b_num < self.m` condition would never be true. `self.mapping` remains empty. `pick` works correctly, always returning `idx`, as no numbers in `[0, m-1]` are blacklisted. Correct.
*   **Correctness of Probability**: Each of the `m` valid numbers (either directly from `[0, m-1]` or via mapping) has an equal 1/m chance of being selected, ensuring uniform distribution among non-blacklisted numbers.

### 6. Improvements & Alternatives

*   **Robustness for `m <= 0`**: If `n` and `blacklist` are such that `self.m` could be 0 or negative (meaning no valid numbers or `n` is too small), the `__init__` method should raise an error or handle this gracefully before `random.randrange(self.m)` is called.
    ```python
    def __init__(self, n: int, blacklist: List[int]):
        self.m = n - len(blacklist)
        if self.m <= 0:
            raise ValueError("No valid numbers can be picked or n is too small.")
        # ... rest of the code
    ```
*   **Clarity/Comments**: Add comments to explain the purpose of `self.m` and the remapping logic for better readability, especially the `while` loop logic.
*   **Docstrings**: Include comprehensive docstrings for the class and its methods to explain their purpose, arguments, and return values.
*   **Alternative if `n` is small**: If `n` is very small and `blacklist` is also small, a simpler approach could be to build a list of all valid numbers explicitly during `__init__` and then `random.choice` from that list. However, this wouldn't scale to large `n` if `blacklist` is also large or `n` is huge. The current approach is generally superior for large `n` and smaller `B`.

### 7. Security/Performance Notes

*   **Randomness**: The `random` module uses a pseudo-random number generator (Mersenne Twister). While suitable for most applications (simulations, games), it is **not cryptographically secure**. If the application requires high-security randomness (e.g., for generating security tokens), the `secrets` module in Python should be used instead.
*   **Performance**: This solution provides excellent performance for repeated `pick` operations, achieving O(1) average time complexity. The O(B) initialization cost is acceptable for scenarios where `pick` is called many more times than `__init__`. For problems where `len(blacklist)` is very close to `n`, or `B` is large, the `O(B)` space and initialization time might become significant, but the O(1) `pick` is hard to beat.

### Code:
```python
import random
from typing import List

class Solution:

    def __init__(self, n: int, blacklist: List[int]):
        self.m = n - len(blacklist)
        self.mapping = {}
        
        blacklist_set = set(blacklist)
        
        last_valid_num = n - 1
        
        for b_num in blacklist:
            if b_num < self.m:
                while last_valid_num in blacklist_set:
                    last_valid_num -= 1
                self.mapping[b_num] = last_valid_num
                last_valid_num -= 1
        

    def pick(self) -> int:
        idx = random.randrange(self.m)
        
        if idx in self.mapping:
            return self.mapping[idx]
        else:
            return idx
```
