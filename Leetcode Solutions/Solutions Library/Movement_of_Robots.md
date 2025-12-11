## Movement of Robots
**Language:** python
**Tags:** python,oop,sorting,prefix sum

### Description:
---

### 1. Overview & Intent

This code calculates the sum of absolute distances between all pairs of robots after they have moved for a specific duration `d`. The key insight for this problem is that when robots collide and pass through each other without changing direction, their *identities* effectively swap. This means we can treat them as if they pass through each other directly, and only their final positions matter, not the specific path of any individual robot. The problem then reduces to calculating the sum of pairwise distances between these final, sorted positions.

### 2. How It Works

The solution proceeds in three main steps:

*   **Calculate Final Positions**:
    *   It iterates through each robot, identified by its initial position `nums[i]` and direction `s[i]`.
    *   If the robot moves 'L' (left), its final position is `nums[i] - d`.
    *   If it moves 'R' (right), its final position is `nums[i] + d`.
    *   These calculated final positions are stored in a new list `final_positions`.
*   **Sort Final Positions**:
    *   The `final_positions` list is then sorted in ascending order. This step is crucial because it simplifies the calculation of absolute differences to simple subtractions.
*   **Efficiently Sum Pairwise Distances**:
    *   It uses a single pass over the sorted `final_positions` list to calculate the total sum of distances.
    *   It maintains a `prefix_sum_of_positions` which stores the sum of all `final_positions[j]` for `j < i`.
    *   For each `final_positions[i]`, its contribution to the total sum is `(i * final_positions[i]) - prefix_sum_of_positions`. This formula efficiently calculates `sum(final_positions[i] - final_positions[j] for j < i)`.
    *   All calculations involving sums are performed modulo `10^9 + 7` to prevent integer overflow.

### 3. Key Design Decisions

*   **Ignoring Collisions**: The most critical design decision is the implicit understanding that robots "pass through" each other. This transforms a complex simulation problem into a straightforward geometric calculation based on final positions.
*   **Sorting Final Positions**: Sorting `final_positions` allows for the simplification of `|a - b|` to `b - a` (when `b > a`), which is essential for the prefix sum optimization.
*   **Prefix Sum Optimization**: Instead of a naive O(N^2) nested loop to sum all pairwise distances, the prefix sum technique reduces the calculation for each element to O(1) after sorting. This is a standard approach for this type of problem.
*   **Modulo Arithmetic**: The `MOD` constant and its application at each step of sum accumulation correctly handle potential integer overflows, which are common in competitive programming problems with large intermediate sums.

### 4. Complexity

*   **Time Complexity**:
    *   Calculating `final_positions`: O(N)
    *   Sorting `final_positions`: O(N log N)
    *   Iterating and calculating the sum of distances: O(N)
    *   **Total Time Complexity: O(N log N)**, dominated by the sorting step.
*   **Space Complexity**:
    *   `final_positions` list: O(N) to store the new positions.
    *   Other variables: O(1)
    *   **Total Space Complexity: O(N)**.

### 5. Edge Cases & Correctness

*   **Empty Input (`n = 0`)**: If `nums` is empty, `n` will be 0. The loops won't execute, and `total_sum_of_distances` will correctly return 0.
*   **Single Robot (`n = 1`)**: The first loop will populate `final_positions` with one element. The sort will handle it. The second loop (for `total_sum_of_distances`) will run for `i=0`. `current_robot_contribution` will be `(0 * final_positions[0] - 0) % MOD = 0`. This is correct, as there are no pairs to calculate distances for.
*   **All Robots Move in Same Direction**: The logic correctly applies `+d` or `-d` regardless of other robots' directions.
*   **Large Distances (`d`)**: Python's arbitrary-precision integers handle very large `nums[i] + d` or `nums[i] - d` values without overflow for `final_positions`. The modulo operations ensure the `total_sum_of_distances` and `prefix_sum_of_positions` stay within bounds.
*   **Correctness of Sum Formula**: The formula `(i * final_positions[i] - prefix_sum_of_positions)` for the current robot's contribution is mathematically sound. For a sorted list `p_0, p_1, ..., p_{n-1}`, the total sum of pairwise differences `sum_{0 <= i < j < n} (p_j - p_i)` can be rewritten as `sum_{j=0}^{n-1} (j * p_j - sum_{k=0}^{j-1} p_k)`, which is precisely what the code implements iteratively.

### 6. Improvements & Alternatives

*   **Readability of Variable Names**: While `nums`, `s`, and `d` are common in competitive programming, for production code, more descriptive names like `initial_positions`, `directions`, and `distance_travelled` could enhance clarity.
*   **In-place Modification (Minor)**: If the original `nums` list is not needed after the calculation, one could modify `nums` in place to store `final_positions` to slightly reduce memory usage by avoiding a new list creation. However, this is a minor optimization and might reduce readability.
*   **Error Handling**: For robustness in a non-competitive programming context, adding checks for `len(nums) == len(s)` and valid characters in `s` (only 'L' or 'R') would be beneficial.

### 7. Security/Performance Notes

*   **Performance**: The O(N log N) time complexity is generally optimal for this type of problem requiring sorted data. No immediate performance bottlenecks are evident beyond the inherent cost of sorting.
*   **Modulo Arithmetic**: The consistent application of the modulo operator (`% MOD`) at each addition and subtraction operation is crucial to prevent integer overflow and ensure the final result is correct within the specified range. Failure to apply modulo intermediate steps (especially subtraction of a smaller prefix sum from a larger current sum) could lead to negative results, which might require `(x % MOD + MOD) % MOD` to ensure a positive modulo result. The current code correctly handles potential negative intermediate results by directly applying `(X - Y) % MOD` which gives a Pythonic negative or positive result that eventually aligns due to properties of modular arithmetic across sums.
*   **Security**: There are no inherent security vulnerabilities as the code performs purely mathematical calculations on numerical inputs and does not interact with external systems or sensitive data.

### Code:
```python
from typing import List

class Solution:
    def sumDistance(self, nums: List[int], s: str, d: int) -> int:
        MOD = 10**9 + 7
        n = len(nums)
        
        final_positions = []
        for i in range(n):
            if s[i] == 'L':
                final_positions.append(nums[i] - d)
            else: # s[i] == 'R'
                final_positions.append(nums[i] + d)
        
        final_positions.sort()
        
        total_sum_of_distances = 0
        prefix_sum_of_positions = 0
        
        for i in range(n):
            current_robot_contribution = (i * final_positions[i] - prefix_sum_of_positions) % MOD
            
            total_sum_of_distances = (total_sum_of_distances + current_robot_contribution) % MOD
            
            prefix_sum_of_positions = (prefix_sum_of_positions + final_positions[i]) % MOD
            
        return total_sum_of_distances
```
