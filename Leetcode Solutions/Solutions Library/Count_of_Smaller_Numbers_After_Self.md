## Count of Smaller Numbers After Self
**Language:** python
**Tags:** python,merge sort,sorting,divide and conquer

### Description:
The provided Python code solves the "Count of Smaller Numbers After Self" problem using a modified merge sort algorithm. For each element in the input array, it efficiently calculates how many elements to its right are strictly smaller than itself.

---

### 1. Overview & Intent

*   **Problem**: For a given array `nums`, return a new array `counts` where `counts[i]` is the number of elements `nums[j]` such that `j > i` and `nums[j] < nums[i]`.
*   **Intent**: To provide an efficient solution, typically required for input sizes where a brute-force O(N^2) approach would be too slow. The chosen approach leverages the divide-and-conquer nature of merge sort to achieve O(N log N) time complexity.

---

### 2. How It Works

1.  **Preparation**:
    *   The code first handles the edge case of an empty input array.
    *   It creates a new list `indexed_nums`. Each element in this list is a tuple `(value, original_index)`. This is crucial because sorting would otherwise lose the original positions needed to update the `counts` correctly.
    *   An array `counts` of the same length as `nums` is initialized with zeros. This array will store the final result.

2.  **Modified Merge Sort**:
    *   The core logic resides within a recursive `merge_sort` function. This function behaves like a standard merge sort but incorporates a counting mechanism during the merge step.
    *   **Divide**: The array `arr_to_sort` is recursively split into `left_half` and `right_half` until only single elements (base case `len(arr_to_sort) <= 1`) remain.
    *   **Conquer & Count (Merge Step)**:
        *   When two sorted halves (`left_half` and `right_half`) are merged, the key insight is applied.
        *   A `right_count` variable is introduced, initialized to 0. It tracks how many elements from `right_half` have been placed into the `merged` array *before* a specific element from `left_half` is picked.
        *   If an element `right_half[j]` is smaller than `left_half[i]`, `right_half[j]` is added to `merged`, and `right_count` is incremented. These elements from `right_half` are by definition smaller than `left_half[i]` and *also originally to the right* of `left_half[i]`.
        *   If an element `left_half[i]` is smaller than or equal to `right_half[j]` (or `right_half` is exhausted), `left_half[i]` is added to `merged`. At this moment, `right_count` accurately represents the number of elements from the original right subarray that are smaller than `left_half[i]` and were originally to its right. So, `counts[left_half[i][1]]` is incremented by `right_count`.
    *   The `merge_sort` function returns the sorted `merged` array, but its primary side effect is populating the global `counts` array.

3.  **Result**: After the initial call to `merge_sort(indexed_nums)` completes, the `counts` array contains the desired result for each element.

---

### 3. Key Design Decisions

*   **Tuples `(value, original_index)`**: Essential for preserving the original position of each number during the sorting process. Without it, we wouldn't know which `count` belongs to which original number.
*   **Modified Merge Sort**: The choice of merge sort is deliberate. Its divide-and-conquer nature naturally separates elements such that those in the "right half" of a merge operation were *always originally to the right* of elements in the "left half." This property is fundamental to the counting logic.
*   **`right_count` Variable**: This is the core algorithmic trick. By accumulating the count of smaller elements from the right subarray as they are merged, we can efficiently update the total count for any element from the left subarray when it's chosen.
*   **In-place Counting (via global `counts` array)**: Instead of passing `counts` through recursive calls or returning complex structures, using a globally accessible `counts` array simplifies updates, as individual merge operations only need to update specific indices.

---

### 4. Complexity

*   **Time Complexity**: **O(N log N)**
    *   Creating `indexed_nums`: O(N)
    *   `merge_sort`: Standard merge sort has O(N log N) time complexity. The additional work in the merge step (updating `right_count` and `counts`) is done in constant time per element during the merge, which sums up to O(N) for each level of merging. This does not change the overall O(N log N).
*   **Space Complexity**: **O(N)**
    *   `indexed_nums`: O(N)
    *   `counts`: O(N)
    *   Auxiliary lists (`left_half`, `right_half`, `merged`) created during recursion: These require O(N) space at each level of the recursion call stack, leading to an overall O(N) auxiliary space requirement.
    *   Recursion call stack: O(log N) depth.

---

### 5. Edge Cases & Correctness

*   **Empty List (`n=0`)**: Explicitly handled, returns `[]`. Correct.
*   **Single Element List (`n=1`)**: `indexed_nums` will have one element. `merge_sort` base case returns it. `counts` remains `[0]`. Correct, as there are no elements to its right.
*   **All Elements Increasing (`[1, 2, 3]`)**: `right_count` will always be 0 when `left_half[i]` is picked, resulting in `[0, 0, 0]`. Correct.
*   **All Elements Decreasing (`[3, 2, 1]`)**:
    *   `indexed_nums = [(3,0), (2,1), (1,2)]`.
    *   `counts = [0,0,0]`.
    *   During merges, `right_count` will increment appropriately. For example, when merging `[(2,1)]` and `[(1,2)]`: `(1,2)` is picked, `right_count=1`. Then `(2,1)` is picked, `counts[1]+=1`.
    *   Expected `[2, 1, 0]`. The algorithm correctly computes this.
*   **Duplicate Elements (`[5, 2, 5, 1]`)**: The condition `left_half[i][0] <= right_half[j][0]` ensures that if elements are equal, the element from the `left_half` is picked first. This means elements equal to `left_half[i]` but appearing in `right_half` are *not* counted towards `left_half[i]`'s smaller count, which aligns with the "strictly smaller" requirement. The algorithm correctly handles duplicates.

The core correctness relies on the invariant that for any merge of `left_half` and `right_half`, all elements in `right_half` were originally to the right of all elements in `left_half`. The `right_count` then precisely tallies the elements from `right_half` that are smaller than the current `left_half` element being processed.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   Renaming `arr_to_sort` to `sub_array` or `items_to_sort` could slightly improve clarity.
    *   More descriptive variable names for `i` and `j` in the merge loop (e.g., `left_ptr`, `right_ptr`) could be used, though `i` and `j` are standard for merge operations.
*   **Alternative Algorithms**:
    *   **Fenwick Tree (Binary Indexed Tree / BIT)** or **Segment Tree**: This approach involves processing `nums` from right to left. First, map unique values in `nums` to ranks (to handle large values efficiently). Then, for each `nums[i]`, query the BIT/Segment Tree for the count of elements already added (from its right) that are smaller than `nums[i]`, and then add `nums[i]` to the data structure. This also achieves O(N log N) time and O(N) space and can sometimes be faster in practice due to lower constant factors and no recursion overhead.
    *   **Balanced Binary Search Tree (e.g., AVL tree, Red-Black tree)**: Iterate `nums` from right to left. Insert each `nums[i]` into a custom BST where each node also stores the count of nodes in its left subtree. When inserting, the count of smaller elements is derived from traversing the tree. This is also O(N log N) time and O(N) space.
*   **Iterative Merge Sort**: For extremely large `N` (beyond typical competitive programming constraints), an iterative merge sort could be used to avoid potential `RecursionError` due to deep recursion stack, though Python's default recursion limit is usually sufficient for competitive programming scales.

---

### 7. Security/Performance Notes

*   **Performance**: The O(N log N) time complexity is optimal for comparison-based solutions to this problem, making it suitable for typical input sizes (e.g., N up to 10^5 or 10^6).
*   **Stack Depth**: While recursive solutions inherently have a stack depth proportional to log N, for typical input sizes (e.g., `N = 10^5`, `log2(N) ≈ 17`), the recursion depth is very shallow and far below Python's default recursion limit (usually 1000 or 3000). Therefore, stack overflow is not a practical concern for this problem in Python.

### Code:
```python
from typing import List

class Solution:
    def countSmaller(self, nums: List[int]) -> List[int]:
        n = len(nums)
        if n == 0:
            return []

        indexed_nums = []
        for i in range(n):
            indexed_nums.append((nums[i], i))

        counts = [0] * n

        def merge_sort(arr_to_sort):
            if len(arr_to_sort) <= 1:
                return arr_to_sort

            mid = len(arr_to_sort) // 2
            left_half = merge_sort(arr_to_sort[:mid])
            right_half = merge_sort(arr_to_sort[mid:])

            merged = []
            i = j = 0
            right_count = 0
            
            while i < len(left_half) or j < len(right_half):
                if j == len(right_half) or (i < len(left_half) and left_half[i][0] <= right_half[j][0]):
                    counts[left_half[i][1]] += right_count
                    merged.append(left_half[i])
                    i += 1
                else:
                    right_count += 1
                    merged.append(right_half[j])
                    j += 1
            return merged

        merge_sort(indexed_nums)
        return counts
```
