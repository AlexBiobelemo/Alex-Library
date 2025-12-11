## Finding 3 Digit Even Numbers
**Language:** python
**Tags:** python,permutations,set,sorting,itertools

### Description:
---

### 1. Overview & Intent

This Python code defines a class `Solution` with a method `findEvenNumbers`.
Its primary purpose is to:
*   Given a list of `digits` (e.g., `[1, 2, 3]`), find all unique three-digit even numbers that can be formed using any three *distinct* digits from the input list.
*   The formed numbers must not have leading zeros.
*   The final output should be a sorted list of these unique even numbers.

**Example:**
*   Input: `digits = [2, 1, 3, 0]`
*   Output (examples): `102, 120, 130, 210, 230, 302, 310, 320` (and more, sorted)

---

### 2. How It Works

The algorithm proceeds in the following main steps:

1.  **Initialize a `set`**: A `set` named `unique_even_numbers` is created. This ensures that only unique numbers are stored, fulfilling the "unique integers" requirement.
2.  **Generate Permutations**: It uses `itertools.permutations(digits, 3)` to generate all possible unique ordered combinations of three digits from the input `digits` list. Each combination `(d1, d2, d3)` represents a potential three-digit number.
3.  **Filter Permutations**: For each generated permutation `(d1, d2, d3)`:
    *   **Leading Zero Check**: It first checks if the first digit `d1` is `0`. If it is, the permutation is skipped (as three-digit numbers cannot start with `0`).
    *   **Even Number Check**: Next, it checks if the third digit `d3` is odd (`d3 % 2 != 0`). If it is, the permutation is skipped (as the number must be even).
4.  **Form Number and Store**: If both checks pass (no leading zero and the number is even), the three-digit number is constructed using `d1 * 100 + d2 * 10 + d3`. This number is then added to the `unique_even_numbers` set.
5.  **Sort and Return**: After processing all permutations, the `unique_even_numbers` set is converted into a list, which is then sorted in ascending order. This sorted list is the final result.

---

### 3. Key Design Decisions

*   **`itertools.permutations(digits, 3)`**: This is an excellent choice for generating all unique ordered combinations of 3 distinct digits. It's concise, efficient, and handles the "distinct elements" constraint naturally.
*   **Using a `set` (`unique_even_numbers`)**: A `set` is ideal for storing unique numbers. It automatically handles duplicate values, ensuring that each valid number is stored only once, and provides average O(1) time complexity for additions.
*   **Early Filtering**: Checking for leading zeros (`d1 == 0`) and evenness (`d3 % 2 != 0`) *before* constructing the number and attempting to add it to the set is an optimization. It avoids unnecessary computations for invalid permutations.
*   **Mathematical Number Construction**: Forming the number directly `d1 * 100 + d2 * 10 + d3` is simple, clear, and computationally efficient compared to string concatenations and conversions.

---

### 4. Complexity

Let `N` be the number of digits in the input `digits` list.

*   **Time Complexity**:
    *   `itertools.permutations(digits, 3)`: This generates `P(N, 3)` permutations, which is `N * (N-1) * (N-2)`. This is `O(N^3)`.
    *   The loop iterates `P(N, 3)` times. Inside the loop, operations (checks, arithmetic, `set.add()`) are all average `O(1)`.
    *   Converting the set to a list: `O(K)`, where `K` is the number of unique even numbers found.
    *   Sorting the list: `O(K log K)`.
    *   Since `digits` typically contains 0-9, `N` is at most 10. `P(10, 3) = 10 * 9 * 8 = 720`.
    *   Therefore, `K` is bounded by 720 (the maximum number of distinct 3-digit permutations from 10 unique digits), and more generally by 900 (the total number of 3-digit numbers from 100 to 999).
    *   Given `K` is effectively a constant (max 900), the conversion and sorting steps are `O(1)` with respect to `N`.
    *   **Overall Time Complexity: `O(N^3)`** (dominated by permutation generation).

*   **Space Complexity**:
    *   `itertools.permutations`: Generates permutations lazily (on demand), so its direct space usage is minimal.
    *   `unique_even_numbers` set: Stores up to `K` integers. As established, `K` is bounded by a constant (max 900).
    *   `result` list: Stores the same `K` integers.
    *   **Overall Space Complexity: `O(1)`** (as `K` is a bounded constant, independent of the input `N` for typical digit lists).

---

### 5. Edge Cases & Correctness

*   **Input `digits` has fewer than 3 elements (e.g., `[1, 2]`)**: `itertools.permutations` will return an empty iterator. The loop won't execute, `unique_even_numbers` will remain empty, and an empty list will be returned, which is correct.
*   **No even digits in `digits`**: All permutations will fail the `d3 % 2 != 0` check. An empty list will be returned, which is correct.
*   **No non-zero digits available for `d1` (e.g., `[0, 0, 0]`)**: All permutations will fail the `d1 == 0` check. An empty list will be returned, which is correct.
*   **All digits are the same (e.g., `[2, 2, 2]`)**: `itertools.permutations([2,2,2], 3)` yields `(2,2,2)` once. This forms `222`, which is even. The set adds `222`. The result `[222]` is correct. (`itertools.permutations` considers elements at different indices as distinct even if their values are identical, which is appropriate here.)
*   **Digits include `0`**: The leading zero check (`d1 == 0`) correctly handles this. `0` can appear as `d2` or `d3` without issues.
*   **Uniqueness and Sorting**: The use of `set` guarantees uniqueness, and `list.sort()` guarantees the final sorted output.

---

### 6. Improvements & Alternatives

*   **Pre-filtering Digits (Minor Optimization)**: For extremely large `N` (though `N` is typically small for digit problems), one could pre-filter `digits` into `even_digits` and `non_zero_digits` to slightly reduce the work for `d1` and `d3`. However, `itertools.permutations` followed by direct checks is often clearer and sufficiently fast for the problem constraints.
*   **Alternative `set` + `sort`**: If the number of results `K` were expected to be massive and we needed sorted output *as elements are found*, a min-heap (`heapq`) could be an option. However, for the bounded `K` in this problem, `set` followed by `list.sort()` is idiomatic Python and performs very well.
*   **Readability**: The current code is very readable and follows a clear flow. No significant readability improvements are necessary.

---

### 7. Security/Performance Notes

*   **Performance**: The solution is highly performant. With `N` (number of input digits) usually limited to 10 (0-9), the `O(N^3)` complexity translates to a maximum of `10^3 = 1000` operations for permutation generation, plus minor constant work. This will execute in milliseconds, well within typical time limits. There are no performance bottlenecks for the given problem scope.
*   **Security**: The code operates purely on numerical data and does not interact with external systems, files, or user-provided strings that could lead to injection vulnerabilities. No security concerns are apparent.

### Code:
```python
from typing import List
import itertools

class Solution:
    def findEvenNumbers(self, digits: List[int]) -> List[int]:
        unique_even_numbers = set()

        for d1, d2, d3 in itertools.permutations(digits, 3):
            # Requirement 2: The integer does not have leading zeros.
            if d1 == 0:
                continue

            # Requirement 3: The integer is even.
            if d3 % 2 != 0:
                continue

            # Requirement 1: The integer consists of the concatenation of three elements.
            num = d1 * 100 + d2 * 10 + d3
            unique_even_numbers.add(num)

        # Return a sorted array of the unique integers.
        result = list(unique_even_numbers)
        result.sort()
        return result
```
