## Beautiful Array
**Language:** ruby
**Tags:** ruby,divide and conquer,recursion,memoization,hashmap

### Description:
This Ruby code defines a function `beautiful_array(n)` which generates a "beautiful array" of length `n`.

---

### 1. Overview & Intent

*   **Problem**: Given an integer `n`, return *any* permutation of the integers from `1` to `n` such that for any `i < j < k`, `A[i] + A[k] != 2 * A[j]`. This condition means that no three elements `A[i], A[j], A[k]` can form an arithmetic progression. Such an array is called a "beautiful array".
*   **Goal**: The function aims to construct and return one such beautiful array for a given `n`. It utilizes a divide-and-conquer strategy with memoization to efficiently solve the problem.

---

### 2. How It Works

The function employs a recursive, top-down dynamic programming approach with memoization:

1.  **Memoization Check**: It first checks if the result for `n` has already been computed and stored in the `@memo` hash. If so, it returns the cached result immediately.
2.  **Base Case**: If `n` is `1`, the beautiful array is simply `[1]`. This is the stopping condition for the recursion.
3.  **Divide**: For `n > 1`, the problem is divided into two subproblems:
    *   Generate a beautiful array for `ceil(n/2)` elements (which will form the basis for odd numbers).
    *   Generate a beautiful array for `floor(n/2)` elements (which will form the basis for even numbers).
    These are done via recursive calls: `beautiful_array((n + 1) / 2)` and `beautiful_array(n / 2)`.
4.  **Transform**: The results from the subproblems (`left_half_base` and `right_half_base`) are transformed:
    *   Elements from `left_half_base` are transformed to `2 * x - 1` to produce a sequence of odd numbers.
    *   Elements from `right_half_base` are transformed to `2 * x` to produce a sequence of even numbers.
5.  **Conquer (Combine)**: The transformed odd numbers and transformed even numbers are concatenated (`odd_nums + even_nums`).
6.  **Memoization Store**: The resulting array for `n` is stored in `@memo` before being returned.

The core insight is that if `x, y, z` form an arithmetic progression, then `x + z = 2y`. This implies that `x`, `y`, and `z` must all have the same parity. By constructing the final array with all odd numbers followed by all even numbers, any potential arithmetic progression *must* occur entirely within the odd subsequence or entirely within the even subsequence. Since these subsequences are themselves constructed from "beautiful arrays" (just scaled), the "beautiful" property is maintained.

---

### 3. Key Design Decisions

*   **Divide and Conquer**: The problem is inherently recursive. Breaking `n` into `ceil(n/2)` and `floor(n/2)` subproblems allows the reuse of the "beautiful array" property on smaller ranges.
*   **Memoization (Dynamic Programming)**: This is crucial to prevent redundant computations. Since `beautiful_array(k)` is called multiple times for the same `k` (e.g., `beautiful_array(2)` is needed for `n=3` and `n=4`), memoization drastically improves performance.
*   **Transformation Logic (`2*x - 1` and `2*x`)**: This is the mathematical core. If `[a, b, c]` is an arithmetic progression, then `[2a-1, 2b-1, 2c-1]` is also an arithmetic progression, and `[2a, 2b, 2c]` is also an arithmetic progression. This property allows us to take a beautiful array of `k` elements (e.g., `[1...k]`) and map it to a beautiful array of `k` odd numbers or `k` even numbers, preserving the non-arithmetic progression property.
*   **Instance Variable for Memoization (`@memo`)**: Common in LeetCode Ruby solutions. It provides a simple way to store state across recursive calls without explicitly passing the memoization table. However, it requires careful handling if the function is part of a class instance that might be reused.

---

### 4. Complexity

*   **Time Complexity: O(n log n)**
    *   Each `beautiful_array(k)` for `k` from `1` to `n` is computed exactly once due to memoization.
    *   To compute `beautiful_array(k)`, it makes two recursive calls and then performs `map` operations and an array concatenation. These array operations take `O(k)` time in total for an array of size `k`.
    *   The recurrence relation is `T(n) = T(n/2) + T((n+1)/2) + O(n)`. By the Master Theorem (or unrolling the recursion tree), this resolves to `O(n log n)`.
*   **Space Complexity: O(n^2)**
    *   The memoization hash `@memo` stores `n` arrays.
    *   `@memo[k]` stores an array of length `k`.
    *   The total space occupied by these stored arrays is the sum of their lengths: `1 + 2 + 3 + ... + n = n * (n + 1) / 2`.
    *   Therefore, the total space complexity is `O(n^2)`.
    *   The recursion call stack depth is `O(log n)`.

---

### 5. Edge Cases & Correctness

*   **`n = 1`**: Handled correctly by the base case, returning `[1]`.
*   **`n = 2`**:
    *   `left_half_base = beautiful_array(1) = [1]`
    *   `right_half_base = beautiful_array(1) = [1]`
    *   `odd_nums = [2*1 - 1] = [1]`
    *   `even_nums = [2*1] = [2]`
    *   Result: `[1, 2]`. Correct, as no AP can be formed with two numbers.
*   **Correctness Logic**: The fundamental correctness relies on the parity property. If `A[i], A[j], A[k]` form an AP, then `A[i] + A[k] = 2 * A[j]`. This equation implies that `A[i]` and `A[k]` must have the same parity. The `2*x-1` transformation always produces odd numbers, and `2*x` always produces even numbers. By concatenating *all* odd numbers first and *all* even numbers second, any AP (`x, y, z`) in the final array *must* consist of elements all having the same parity. Since the transformed `odd_nums` and `even_nums` subsequences are themselves guaranteed to be "beautiful" (due to the recursive calls and transformation property), no AP can exist within them. Therefore, no AP can exist in the combined array.

---

### 6. Improvements & Alternatives

*   **Iterative (Bottom-Up) DP**: Instead of top-down recursion with memoization, one could build the solution iteratively from `n=1` up to the target `N`. This would avoid recursion overhead and might improve constant factors for time/space, but the asymptotic complexity would remain `O(N log N)` time and `O(N^2)` space.
*   **Memoization Scope**: Using an instance variable `@memo` means its state persists across multiple calls to `beautiful_array` if the `Solution` object (in a LeetCode context) is reused. It's safer practice to:
    *   Wrap the function in a class and explicitly reset `@memo` in an initializer or before each public call.
    *   Pass the memoization hash as an argument to the recursive function.
    *   Define a helper `_beautiful_array(n, memo_hash)` and call it from a public `beautiful_array(n)` that initializes `memo_hash = {}`.
*   **Clarity of variable names**: While comments are good, `left_half_base` and `right_half_base` could be more descriptive of their eventual purpose, e.g., `base_for_odd_transformation` or `base_for_even_transformation`.
*   **Alternative Construction**: There are other valid constructions for beautiful arrays. This specific approach generates *one* such valid array, but not necessarily the only one or a lexicographically smallest/largest one.

---

### 7. Security/Performance Notes

*   **Performance (Space)**: The `O(N^2)` space complexity, while acceptable for typical competitive programming constraints (e.g., `N` up to 1000, `1000^2` means 1 million integers, which is a few megabytes), could become a bottleneck for very large `N`. If `N` were much larger, alternative approaches that avoid storing all intermediate arrays might be necessary.
*   **Ruby Array Operations**: Ruby's `map` and `+` (concatenation) operations create new arrays. While this is standard for functional-style operations, it means new memory is allocated for each intermediate array, potentially leading to more garbage collection overhead compared to languages that allow more in-place manipulation or have different memory models for arrays. This doesn't change the Big-O complexity but can affect real-world performance constants.

### Code:
```ruby
# @param {Integer} n
# @return {Integer[]}
def beautiful_array(n)
    # Memoization hash to store results of subproblems
    # Using an instance variable for memoization, common in LeetCode Ruby solutions
    @memo ||= {}
    return @memo[n] if @memo.key?(n)

    # Base case: A beautiful array of length 1 is simply [1]
    if n == 1
        @memo[n] = [1]
        return [1]
    end

    # Divide and Conquer approach:
    # A beautiful array can be constructed by concatenating two smaller beautiful arrays.
    # The key insight is that if nums[i], nums[k], nums[j] form an arithmetic progression,
    # then nums[i] and nums[j] must have the same parity, and nums[k] must also have that parity.
    # By placing all odd numbers before all even numbers, any arithmetic progression
    # must be entirely within the odd numbers or entirely within the even numbers.

    # 1. Construct the "left half" for odd numbers:
    # This part will contain numbers 1, 3, 5, ... up to n (or n-1).
    # We recursively call beautiful_array with ceil(n/2) elements.
    # Ruby's integer division (n + 1) / 2 correctly calculates ceil(n/2).
    left_half_base = beautiful_array((n + 1) / 2)
    
    # 2. Construct the "right half" for even numbers:
    # This part will contain numbers 2, 4, 6, ... up to n (or n-1).
    # We recursively call beautiful_array with floor(n/2) elements.
    # Ruby's integer division n / 2 correctly calculates floor(n/2).
    right_half_base = beautiful_array(n / 2)
    
    # 3. Transform the base arrays into the final beautiful array:
    # For each element `x` in `left_half_base`, transform it to `2*x - 1` to get odd numbers.
    odd_nums = left_half_base.map { |x| 2 * x - 1 }
    
    # For each element `x` in `right_half_base`, transform it to `2*x` to get even numbers.
    even_nums = right_half_base.map { |x| 2 * x }
    
    # 4. Concatenate the odd numbers and even numbers.
    # The resulting array will be a permutation of [1, n] and beautiful.
    result = odd_nums + even_nums
    
    # Store the result in memoization hash before returning
    @memo[n] = result
    result
end

```
