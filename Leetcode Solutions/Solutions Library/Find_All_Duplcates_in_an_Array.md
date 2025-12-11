## Find All Duplcates in an Array
**Language:** python
**Tags:** python,in-place algorithm,array,frequency counting

### Description:
This code implements an efficient algorithm to find all numbers that appear twice in an array of integers.

---

### 1. Overview & Intent

The `findDuplicates` method aims to identify and return a list of all numbers that are duplicated within the input list `nums`.

**Key Constraint (Implied by the solution technique):** The problem typically specifies that the input array `nums` contains `n` integers where each integer `num` is in the range `[1, n]`. This constraint is crucial for the in-place modification strategy to work correctly.

---

### 2. How It Works

The algorithm cleverly uses the input array itself as a hash map or a frequency counter, leveraging the `[1, n]` range constraint. It iterates through the array once:

1.  **Calculate Index**: For each `num` in `nums`, it calculates an `idx` by taking its absolute value and subtracting 1 (`idx = abs(num) - 1`). This `idx` corresponds to the 0-indexed position where `num` *should* be if it were sorted and unique.
2.  **Check for Duplication**: It then examines the sign of the number at `nums[idx]`:
    *   **If `nums[idx]` is negative**: This indicates that the number corresponding to `abs(num)` has been encountered before (and its "slot" at `idx` was marked negative). Therefore, `abs(num)` is a duplicate, and it's added to the `duplicates` list.
    *   **If `nums[idx]` is positive**: This is the first time `abs(num)` is being encountered. To mark it as "seen," the value at `nums[idx]` is negated (`nums[idx] = -nums[idx]`).
3.  **Return Duplicates**: After iterating through all numbers, the `duplicates` list contains all unique numbers that appeared twice.

---

### 3. Key Design Decisions

*   **In-Place Modification**: The most significant design choice is to modify the input array `nums` directly. This allows the algorithm to use the array's indices and values to store state (whether a number has been "seen" before).
*   **Sign as a Flag**: The sign of `nums[idx]` acts as a boolean flag: positive means "not yet seen," negative means "seen."
*   **1-Indexed to 0-Indexed Conversion**: `abs(num) - 1` correctly maps the problem's 1-indexed number range `[1, n]` to the 0-indexed array `[0, n-1]`.

**Trade-offs:**
*   **Pros**: Extremely efficient in terms of space (O(1) auxiliary space) and time (O(N)).
*   **Cons**:
    *   **Modifies Input**: The original `nums` array is altered. If the caller needs the original array, they would need to make a copy first.
    *   **Strict Constraints**: Relies heavily on the constraint that numbers are in the range `[1, n]` and are initially positive. If numbers could be negative, zero, or outside this range, this specific technique would not work directly.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The algorithm iterates through the input list `nums` exactly once. Each operation inside the loop (absolute value, subtraction, array access, negation, append) takes constant time.
*   **Space Complexity: O(1)** (Auxiliary Space)
    *   The algorithm uses a constant amount of extra space, irrespective of the input size, for variables like `idx` and `num`.
    *   The `duplicates` list, which stores the result, is typically not counted towards auxiliary space complexity in competitive programming contexts, as it's the required output. If counted, it would be O(K) where K is the number of duplicates.

---

### 5. Edge Cases & Correctness

*   **Empty List `[]`**: The loop won't execute, and an empty `duplicates` list will be returned, which is correct.
*   **No Duplicates `[1, 2, 3, 4]`**: Each number will be encountered once. Its corresponding `nums[idx]` will be negated. No numbers will be added to `duplicates`. Correctly returns `[]`.
*   **All Duplicates `[1, 1, 2, 2]`**:
    *   `num = 1`: `idx = 0`. `nums[0]` becomes `-1`.
    *   `num = 1`: `idx = 0`. `nums[0]` is `-1` (negative). `duplicates.append(1)`.
    *   `num = 2`: `idx = 1`. `nums[1]` becomes `-2`.
    *   `num = 2`: `idx = 1`. `nums[1]` is `-2` (negative). `duplicates.append(2)`.
    *   Correctly returns `[1, 2]`.
*   **Single Element `[1]`**: `num = 1`, `idx = 0`. `nums[0]` becomes `-1`. Loop ends. Correctly returns `[]`.

The correctness hinges entirely on the input constraint: numbers must be in `[1, n]`. If `num` could be `0` or negative initially, `abs(num) - 1` would lead to invalid indices or incorrect logic. If `num` could be greater than `n`, `abs(num) - 1` would lead to an `IndexError`.

---

### 6. Improvements & Alternatives

**Improvements to Current Algorithm:**
*   The current solution is highly optimized for its specific constraints. Further micro-optimizations are unlikely to yield significant benefits and might reduce readability. The comments are already quite good.

**Alternative Approaches (with different trade-offs):**

1.  **Using a Hash Set (Python `set`)**:
    *   **Approach**: Iterate through `nums`. For each `num`, if it's already in the set, add it to duplicates. Otherwise, add it to the set.
    *   **Complexity**: O(N) time, O(N) space (for the set).
    *   **Pros**: Doesn't modify input, handles wider range of integer values (including 0, negative numbers, or numbers outside `[1, n]`), generally more readable.
    *   **Cons**: Uses more auxiliary space than the in-place approach.

2.  **Sorting the Array**:
    *   **Approach**: Sort `nums`. Then iterate through the sorted array, checking adjacent elements for equality.
    *   **Complexity**: O(N log N) time (due to sorting), O(1) or O(N) space (depending on sort algorithm, Python's `sort()` is Timsort which is O(N) worst-case space).
    *   **Pros**: Doesn't require specific `[1, n]` constraints (though numbers would still need to be sortable).
    *   **Cons**: Slower time complexity, may modify input if `sort()` is in-place.

3.  **Using a Frequency Map (Python `dict` or `collections.Counter`)**:
    *   **Approach**: Count occurrences of each number. Then iterate through the counts to find numbers with count 2.
    *   **Complexity**: O(N) time, O(N) space.
    *   **Pros**: Very readable, handles any integer values.
    *   **Cons**: Uses more auxiliary space.

---

### 7. Security/Performance Notes

*   **Performance**: This algorithm is one of the most performant solutions for this specific problem due to its O(N) time and O(1) auxiliary space complexity. It avoids the overhead of hash table collisions or sorting comparisons.
*   **Security**: There are no direct security vulnerabilities associated with this algorithm. However, in a larger system, modifying input arrays in-place without clear documentation or expectation from the caller could lead to unexpected side effects or bugs if other parts of the system rely on the original array content. This is a design consideration, not a security flaw.

### Code:
```python
class Solution:
    def findDuplicates(self, nums: List[int]) -> List[int]:
        duplicates = []
        for num in nums:
            # Get the absolute value of the current number to use as an index.
            # Numbers are 1-indexed (range [1, n]), array is 0-indexed,
            # so subtract 1 to get the correct array index.
            idx = abs(num) - 1

            # If the number at the calculated index (nums[idx]) is negative,
            # it means we have encountered the number 'abs(num)' before.
            # This is the second time we are seeing it, so 'abs(num)' is a duplicate.
            if nums[idx] < 0:
                duplicates.append(abs(num))
            else:
                # If the number at the calculated index is positive,
                # it means this is the first time we are encountering 'abs(num)'.
                # Mark it as seen by negating the value at nums[idx].
                nums[idx] = -nums[idx]
        
        return duplicates
```
