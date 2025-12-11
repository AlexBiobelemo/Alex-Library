## Count Hills and Valleys in an Array
**Language:** python
**Tags:** python,object-oriented programming,list,linear scan

### Description:
As a senior code reviewer and educator, I find this solution to be clear, well-structured, and correct. It directly addresses the problem's implicit requirement of considering only "closest non-equal neighbors" by pre-processing the input.

---

### 1. Overview & Intent

This Python code aims to count the number of "hills" and "valleys" in a given list of integers, `nums`.

*   **Hill**: A point `i` is a hill if its value `nums[i]` is strictly greater than its closest non-equal neighbors on both sides.
*   **Valley**: A point `i` is a valley if its value `nums[i]` is strictly smaller than its closest non-equal neighbors on both sides.

The key challenge addressed by the code is handling consecutive duplicate numbers, which form "flat" sections. The problem implicitly states that `nums[i] == nums[j]` means they are part of the same hill or valley, implying that such duplicates should be effectively "skipped" when determining neighbors.

---

### 2. How It Works

The solution employs a two-step approach:

1.  **Filter Consecutive Duplicates**: It first processes the input `nums` to create a new list, `effective_nums`. This list contains only the unique consecutive elements from `nums`. For example, `[1, 2, 2, 3, 1]` becomes `[1, 2, 3, 1]`. This step simplifies the subsequent logic by ensuring that any adjacent elements in `effective_nums` are guaranteed to be non-equal.
2.  **Count Hills and Valleys**: It then iterates through the `effective_nums` list, from the second element to the second-to-last element. For each element, it checks its immediate left and right neighbors (which are guaranteed to be non-equal due to Step 1).
    *   If `left < current > right`, it's a hill.
    *   If `left > current < right`, it's a valley.
    *   The count is incremented for each such instance.

---

### 3. Key Design Decisions

*   **Data Structure**: The use of `effective_nums` (a list) is a central design choice.
    *   **Pros**: It simplifies the logic for identifying hills and valleys significantly. By removing consecutive duplicates, the problem reduces to a straightforward scan where adjacent elements in `effective_nums` are directly the "closest non-equal neighbors." This makes the subsequent counting loop very clean and readable.
    *   **Cons**: It uses O(N) additional space in the worst case (when there are no consecutive duplicates).
*   **Algorithm**: The solution uses two linear scans (one for filtering, one for counting). This is a common and robust pattern for problems that benefit from input pre-processing.
*   **Trade-offs**: The decision to use an auxiliary list (`effective_nums`) trades off additional space complexity (O(N)) for simpler, more readable, and less error-prone time complexity (O(N)). An O(1) space solution would be more complex to implement correctly while maintaining O(N) time complexity.

---

### 4. Complexity

*   **Time Complexity**:
    *   **Step 1 (Filtering)**: The loop iterates through `nums` once. Appending to a list is amortized O(1). Thus, this step is O(N), where N is the length of `nums`.
    *   **Step 2 (Counting)**: The loop iterates through `effective_nums` once. The length of `effective_nums` (let's call it M) is at most N. Thus, this step is O(M), which is O(N).
    *   **Overall**: O(N) + O(N) = **O(N)**.
*   **Space Complexity**:
    *   **`effective_nums`**: In the worst case (e.g., `[1,2,1,2,1]`), `effective_nums` will have the same number of elements as `nums`. Thus, it uses O(N) space.
    *   **Overall**: **O(N)**.

---

### 5. Edge Cases & Correctness

The solution handles several important edge cases correctly:

*   **Empty `nums`**: `if not nums: return 0` handles this at the beginning of Step 1.
*   **`nums` with 1 or 2 elements**:
    *   `effective_nums` will have 1 or 2 elements.
    *   `if len(effective_nums) < 3: return 0` correctly handles this, as a hill/valley requires at least three distinct points (`left`, `current`, `right`).
*   **All elements are the same**: `[5,5,5,5]`
    *   `effective_nums` becomes `[5]`.
    *   `len(effective_nums) < 3` returns 0. Correct.
*   **Monotonically increasing/decreasing**: `[1,2,3,4,5]` or `[5,4,3,2,1]`
    *   `effective_nums` remains unchanged.
    *   The loop for counting will not find any hills or valleys. Returns 0. Correct.
*   **Duplicates forming flat sections**: `[2,4,4,1]`
    *   `effective_nums` becomes `[2,4,1]`.
    *   `4` is correctly identified as a hill. Returns 1. Correct.
*   **Adjacent duplicates at boundaries**: `[1,1,2,3]` or `[3,2,1,1]`
    *   `[1,2,3]` or `[3,2,1]`. Correctly returns 0.

---

### 6. Improvements & Alternatives

*   **O(1) Space Alternative**:
    While the current solution is efficient in time (O(N)), an alternative approach could achieve O(1) space complexity. This would involve a single pass where we keep track of the *previous distinct* value and for the *current* distinct value, we look ahead to find the *next distinct* value. This typically makes the loop logic more complex and prone to off-by-one errors or could degrade to O(N^2) time if the look-ahead is implemented naively. The current approach prioritizes clarity and correctness at the cost of O(N) space, which is often a good trade-off.

    A well-implemented O(1) space solution *can* achieve O(N) time but often involves a more intricate pointer/index management to correctly identify `left_neighbor`, `current_val`, and `right_neighbor` by skipping over arbitrary numbers of duplicates dynamically. The clarity of the current two-pass O(N) time, O(N) space solution often makes it preferable in practice.

*   **Minor Readability**:
    The comments are very helpful and explain the intent well. Variable names are clear. No significant readability improvements are strictly needed.

---

### 7. Security/Performance Notes

*   **Security**: There are no apparent security vulnerabilities in this code. It deals purely with numerical array manipulation and does not interact with external systems, user input in a sensitive way, or complex data structures that might be exploitable.
*   **Performance**: The solution is already optimal in terms of time complexity (O(N)) given that all elements must be examined at least once. For typical competitive programming constraints (N up to 10^5 or 10^6), an O(N) solution with O(N) space is perfectly acceptable. The constant factor for creating `effective_nums` is small.

### Code:
```python
class Solution:
    def countHillValley(self, nums: List[int]) -> int:
        
        # Step 1: Create an 'effective_nums' list by removing consecutive duplicates.
        # This simplifies the problem by ensuring that any adjacent elements in 'effective_nums'
        # are non-equal. This directly addresses the condition "Adjacent indices i and j
        # are part of the same hill or valley if nums[i] == nums[j]" and helps find
        # "closest non-equal neighbors".
        
        effective_nums = []
        if not nums:
            return 0
        
        effective_nums.append(nums[0])
        for i in range(1, len(nums)):
            if nums[i] != nums[i-1]:
                effective_nums.append(nums[i])
                
        # Step 2: Iterate through 'effective_nums' to count hills and valleys.
        # A hill or valley requires a peak/trough with distinct values on both sides.
        # This means we need at least 3 elements in 'effective_nums' to form one.
        # We iterate from the second element to the second-to-last element.
        
        count = 0
        if len(effective_nums) < 3:
            return 0
            
        for i in range(1, len(effective_nums) - 1):
            left_neighbor = effective_nums[i-1]
            current_val = effective_nums[i]
            right_neighbor = effective_nums[i+1]
            
            # Check for a hill: current value is greater than both its non-equal neighbors
            if left_neighbor < current_val and current_val > right_neighbor:
                count += 1
            # Check for a valley: current value is smaller than both its non-equal neighbors
            elif left_neighbor > current_val and current_val < right_neighbor:
                count += 1
                
        return count
```
