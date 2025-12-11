## Minimum Deviation in Array
**Language:** python
**Tags:** python,oop,heap,greedy

### Description:
This code addresses the problem of finding the minimum deviation in an array of integers. The allowed operations are: if a number is even, you can divide it by 2; if a number is odd, you can multiply it by 2. The goal is to perform these operations any number of times to minimize the difference between the maximum and minimum values in the array.

---

### 1. Overview & Intent

The primary goal of the code is to find the smallest possible range (maximum element - minimum element) that can be achieved by applying specific transformations to the numbers in the input array. Each number can either be multiplied by 2 (if odd) or divided by 2 (if even), repeatedly. The strategy involves maximizing all numbers initially and then iteratively reducing the largest number to narrow the range.

---

### 2. How It Works

1.  **Initial Transformation & Heap Population:**
    *   It initializes a max-heap (`max_heap`) using Python's `heapq` module (by storing negative values).
    *   It also tracks `current_min_in_heap`, which will always store the smallest actual value present in the conceptual set of numbers.
    *   For each number `num` in the input `nums`:
        *   If `num` is odd, it's multiplied by 2. This is a crucial step: it ensures that all numbers are initially brought to their largest possible *even* form (or stay as is if already even). For an odd `x`, it can be `x` or `2x`. By starting at `2x`, we cover both possibilities (we can later divide `2x` to get `x`).
        *   The (potentially transformed) `num` is pushed onto the `max_heap` (as `-num`).
        *   `current_min_in_heap` is updated with `min(current_min_in_heap, num)`.

2.  **Iterative Reduction & Deviation Tracking:**
    *   An initial `min_deviation` is calculated using the current maximum value (top of `max_heap`) and `current_min_in_heap`.
    *   The code enters a `while True` loop:
        *   It extracts the largest value (`max_val`) from the `max_heap`.
        *   **Termination Condition:** If `max_val` is odd, it cannot be divided further. Since we can only reduce the maximum, and this `max_val` cannot be reduced, the loop breaks. The current `min_deviation` is the answer.
        *   **Reduction Step:** If `max_val` is even, it's divided by 2 (`new_val = max_val // 2`).
        *   `new_val` is pushed back onto the `max_heap`.
        *   `current_min_in_heap` is updated to include `new_val` (as `new_val` might be the new global minimum).
        *   The `min_deviation` is updated by comparing the current deviation (current max in heap - current min in heap) with the `min_deviation` found so far.

3.  **Return:** The final `min_deviation` is returned.

---

### 3. Key Design Decisions

*   **Max-Heap for Efficient Max Element Retrieval:** Using a max-heap (simulated with `heapq` and negative values) allows for `O(log N)` retrieval and removal of the current maximum element, which is essential for the iterative reduction strategy.
*   **Initial Maximization of Odd Numbers:** This is a critical greedy step. By multiplying all odd numbers by 2 initially, we ensure that each number starts at its largest possible *even* value. This expands the search space upwards, covering all states. For any number `x`, its values can range from `x` (if x is odd) to `2x` (if x is odd), or `x` down to `x/2^k` (if x is even). By making all initial odds `2*odd`, all numbers effectively start at their maximum *even* potential, from which they can only be reduced.
*   **Separate `current_min_in_heap` Tracking:** The `heapq` only guarantees the minimum (or maximum with negative values) element at its root. To find the overall deviation, we need the *true minimum* among all numbers currently in the conceptual set represented by the heap. `current_min_in_heap` correctly maintains this minimum by comparing against every number pushed into the heap.
*   **Greedy Reduction of Maximum:** The core strategy is to always reduce the maximum element. This is because to minimize `max - min`, you either need to decrease `max` or increase `min`. Since we've already maximized all numbers initially (only increasing odds), the only remaining action that can reduce the deviation is to decrease the current maximum.

---

### 4. Complexity

*   **Time Complexity:** `O(N log N + N log M log N)`
    *   **Initial Population:** `N` elements are pushed onto the heap. Each push takes `O(log N)`. Total: `O(N log N)`.
    *   **While Loop:** In the worst case, each number (initially up to `M`, where `M` is the maximum possible value after initial multiplication) can be divided by 2 approximately `log M` times until it becomes odd. Since there are `N` numbers, in theory, we might perform `N * log M` division operations. Each operation involves a heap pop and a heap push, both `O(log N)`. Total: `O(N log M log N)`.
    *   Overall: `O(N log N + N log M log N)`. Since `N log M log N` usually dominates `N log N`, it's often simplified to `O(N log M log N)`.
        *   *Note*: `M` here refers to the maximum possible value an element can reach, which could be `2 * max(nums)` if `max(nums)` is odd. If `max(nums)` is `10^9`, `log M` is around 30.

*   **Space Complexity:** `O(N)`
    *   The `max_heap` stores up to `N` elements.

---

### 5. Edge Cases & Correctness

*   **Empty Input Array:** The problem constraints for competitive programming typically guarantee `nums` is non-empty. If it could be empty, the code would error on `max_heap[0]` or the loops.
*   **Single Element Array:**
    *   E.g., `nums = [3]`.
    *   `3` becomes `6`. `max_heap = [-6]`, `current_min_in_heap = 6`.
    *   `min_deviation` initializes to `-(-6) - 6 = 0`.
    *   Loop: `max_val = 6`. `new_val = 3`.
    *   `max_heap = [-3]`, `current_min_in_heap = min(6, 3) = 3`.
    *   `min_deviation = min(0, -(-3) - 3) = min(0, 0) = 0`.
    *   Loop: `max_val = 3`. `max_val` is odd, so `break`.
    *   Returns `0`. This is correct, as deviation for a single number is 0.
*   **All Numbers Are Initially Even:** The initial transformation step `if num % 2 == 1: num *= 2` will be skipped for all numbers, and they will be pushed as is. The algorithm proceeds correctly.
*   **All Numbers Are Already Optimized:** If the input `nums` is `[2, 4, 8]`.
    *   `max_heap = [-8, -4, -2]`, `current_min_in_heap = 2`.
    *   `min_deviation = 8 - 2 = 6`.
    *   Loop 1: `max_val = 8`. `new_val = 4`. `max_heap` now conceptually `[4, 4, 2]`. `current_min_in_heap` remains 2. `min_deviation = min(6, 4 - 2) = 2`.
    *   Loop 2: `max_val = 4`. `new_val = 2`. `max_heap` now conceptually `[4, 2, 2]`. `current_min_in_heap` remains 2. `min_deviation = min(2, 4 - 2) = 2`.
    *   Loop 3: `max_val = 4`. `new_val = 2`. `max_heap` now conceptually `[2, 2, 2]`. `current_min_in_heap` remains 2. `min_deviation = min(2, 2 - 2) = 0`.
    *   The logic correctly explores possibilities and finds the minimum.

---

### 6. Improvements & Alternatives

*   **Readability:** The code is quite readable. The comments for simulating a max-heap with negative values are helpful. Variable names are clear.
*   **Performance:** The chosen algorithm (greedy approach with a heap) is generally considered optimal for this problem type. Alternative approaches that might involve trying all combinations of operations would be combinatorial and thus significantly slower.
*   **Pythonic Nuances:** Using `float('inf')` for an initial minimum and `heapq` for heap operations are standard Python practices.
*   **Built-in Max-Heap:** If Python had a direct `max_heap` implementation, it would slightly simplify the code by removing the need for negating values, but the underlying complexity would remain the same.

---

### 7. Security/Performance Notes

*   **Security:** There are no inherent security vulnerabilities in this algorithm. It operates on numerical inputs and does not interact with external systems or user-controlled data in a way that would introduce security risks.
*   **Performance:** The `N log M log N` complexity can be significant for very large `N` and `M`. For typical competitive programming constraints (`N` up to `10^5`, `M` up to `10^9`), this complexity is usually acceptable (e.g., `10^5 * 30 * log(10^5)` is roughly `10^5 * 30 * 17` which is around `5 * 10^7` operations, feasible within a few seconds).
*   The use of `heapq` is efficient, implemented in C for performance-critical parts.

### Code:
```python
import heapq
from typing import List

class Solution:
    def minimumDeviation(self, nums: List[int]) -> int:
        max_heap = [] # Use a max-heap to store numbers
        current_min_in_heap = float('inf') # Track the minimum element currently in the heap

        for num in nums:
            if num % 2 == 1:
                num *= 2  # If odd, multiply by 2 to maximize its initial value and make it even
            
            heapq.heappush(max_heap, -num) # Push negative value to simulate max-heap
            current_min_in_heap = min(current_min_in_heap, num) # Update the overall minimum
        
        min_deviation = -max_heap[0] - current_min_in_heap # Initial deviation: max_val - min_val

        while True:
            max_val = -heapq.heappop(max_heap) # Get the current maximum value from the heap
            
            if max_val % 2 == 1:
                break # If max_val is odd, it cannot be divided further, so we stop
            
            new_val = max_val // 2 # Divide the even max_val by 2
            heapq.heappush(max_heap, -new_val) # Push the new value back to the heap
            
            current_min_in_heap = min(current_min_in_heap, new_val) # Update the overall minimum
            
            min_deviation = min(min_deviation, -max_heap[0] - current_min_in_heap) # Update min deviation
            
        return min_deviation
```
