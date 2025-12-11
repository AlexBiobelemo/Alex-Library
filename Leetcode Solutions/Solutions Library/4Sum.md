## 4Sum
**Language:** python
**Tags:** two pointers,sorting,array,algorithms,k-sum problem

### Description:
<p>This Python code implements a solution to the "4Sum" problem, which involves finding all unique quadruplets in an array <code>nums</code> that sum up to a given <code>target</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given an array <code>nums</code> of <code>n</code> integers and an integer <code>target</code>, find all unique quadruplets <code>[a, b, c, d]</code> in <code>nums</code> such that <code>a + b + c + d</code> equals <code>target</code>.</li>
<li><strong>Goal:</strong> The function <code>fourSum</code> aims to return a list of all such unique quadruplets. Uniqueness means the set of numbers in the quadruplet must be distinct, even if they appear at different indices in the original array. For example, <code>[1, 2, 3, 4]</code> and <code>[4, 3, 2, 1]</code> are considered the same.</li>
<li><strong>Approach:</strong> It extends the common "2-Sum" and "3-Sum" patterns, using sorting and a two-pointer technique to efficiently find combinations while handling duplicates.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Initial Checks:</strong><ul>
<li>It first checks if the array <code>nums</code> has at least 4 elements. If not, no quadruplet can be formed, and an empty list is returned.</li>
</ul>
</li>
<li><strong>Sorting:</strong><ul>
<li>The <code>nums</code> array is sorted in ascending order. This is a crucial step that enables both the two-pointer technique and efficient duplicate handling.</li>
</ul>
</li>
<li><strong>Outer Loops (Fixing Two Numbers):</strong><ul>
<li>Two nested <code>for</code> loops iterate to fix the first two numbers of the quadruplet, <code>nums[i]</code> and <code>nums[j]</code>.</li>
<li><code>i</code> iterates from the beginning up to <code>n-3</code> (to leave at least three numbers for <code>j</code>, <code>left</code>, <code>right</code>).</li>
<li><code>j</code> iterates from <code>i+1</code> up to <code>n-2</code> (to leave at least two numbers for <code>left</code>, <code>right</code>).</li>
</ul>
</li>
<li><strong>Duplicate Skipping for <code>i</code> and <code>j</code>:</strong><ul>
<li>Inside both <code>i</code> and <code>j</code> loops, checks like <code>if i &gt; 0 and nums[i] == nums[i-1]: continue</code> are used to skip over identical numbers that would lead to duplicate quadruplets if processed again.</li>
</ul>
</li>
<li><strong>Pruning Optimizations:</strong><ul>
<li><strong>Early <code>break</code>:</strong> If <code>nums[i]</code> plus the three smallest numbers <em>after</em> it (<code>nums[i+1]</code>, <code>nums[i+2]</code>, <code>nums[i+3]</code>) already exceeds <code>target</code>, then any subsequent combinations with <code>nums[i]</code> (which would use even larger numbers) will also exceed <code>target</code>. The inner loops for <code>nums[i]</code> can be safely broken.</li>
<li><strong>Early <code>continue</code>:</strong> If <code>nums[i]</code> plus the three largest numbers in the entire array (<code>nums[n-3]</code>, <code>nums[n-2]</code>, <code>nums[n-1]</code>) is <em>less</em> than <code>target</code>, then <code>nums[i]</code> cannot be part of any solution (even with the largest possible remaining numbers). The outer loop skips to the next <code>i</code>.</li>
<li>Similar <code>break</code> and <code>continue</code> optimizations are applied within the <code>j</code> loop, considering <code>nums[i]</code> and <code>nums[j]</code> already fixed.</li>
</ul>
</li>
<li><strong>Inner Two-Pointer Approach (Finding Remaining Two Numbers):</strong><ul>
<li>For each pair <code>(nums[i], nums[j])</code>, two pointers, <code>left</code> (starting at <code>j+1</code>) and <code>right</code> (starting at <code>n-1</code>), are initialized.</li>
<li>These pointers move towards each other to find <code>nums[left]</code> and <code>nums[right]</code> such that <code>nums[i] + nums[j] + nums[left] + nums[right] == target</code>.</li>
<li><strong>If <code>current_sum == target</code>:</strong> A valid quadruplet is found. It's added to the <code>result</code> list. Then, <code>left</code> and <code>right</code> pointers are advanced while skipping duplicates to ensure uniqueness.</li>
<li><strong>If <code>current_sum &lt; target</code>:</strong> The sum is too small, so <code>left</code> is incremented to try a larger number.</li>
<li><strong>If <code>current_sum &gt; target</code>:</strong> The sum is too large, so <code>right</code> is decremented to try a smaller number.</li>
</ul>
</li>
<li><strong>Return Result:</strong> After all loops complete, the <code>result</code> list containing all unique quadruplets is returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sorting the Array:</strong><ul>
<li><strong>Rationale:</strong> Absolutely fundamental. It enables the two-pointer technique for efficient search and simplifies the logic for skipping duplicate elements.</li>
<li><strong>Trade-off:</strong> Adds an initial O(N log N) time complexity.</li>
</ul>
</li>
<li><strong>Two-Pointer Technique:</strong><ul>
<li><strong>Rationale:</strong> After fixing the first two elements (<code>nums[i]</code>, <code>nums[j]</code>), the problem effectively reduces to a "2-Sum" problem on the remaining sorted sub-array. The two-pointer approach solves this in O(N) time, which is optimal for a sorted array.</li>
<li><strong>Trade-off:</strong> Requires the array to be sorted.</li>
</ul>
</li>
<li><strong>Nested Loops:</strong><ul>
<li><strong>Rationale:</strong> The core structure of iterating through possible first (<code>i</code>) and second (<code>j</code>) elements. This forms the basis of the O(N^3) complexity.</li>
</ul>
</li>
<li><strong>Duplicate Skipping Logic:</strong><ul>
<li><strong>Rationale:</strong> <code>if i &gt; 0 and nums[i] == nums[i-1]: continue</code> (and similar for <code>j</code>, <code>left</code>, <code>right</code>) is crucial for ensuring that only <em>unique</em> quadruplets are added to the <code>result</code>. If not handled, <code>[1,2,2,3]</code> with <code>target=8</code> and another <code>[1,2,2,3]</code> (from a different '2') would both be added.</li>
</ul>
</li>
<li><strong>Early Pruning Optimizations:</strong><ul>
<li><strong>Rationale:</strong> The <code>break</code> and <code>continue</code> conditions based on minimum/maximum possible sums significantly reduce the number of iterations in many practical cases, especially when the target is far from the typical sum range of the numbers.</li>
<li><strong>Trade-off:</strong> Adds a bit of conditional logic complexity to the loops, but the performance gain often outweighs this.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong><ul>
<li><strong>Sorting:</strong> O(N log N) due to <code>nums.sort()</code>.</li>
<li><strong>Outer Loops:</strong> The outermost loop runs <code>N</code> times (for <code>i</code>). The second loop runs <code>N</code> times (for <code>j</code>).</li>
<li><strong>Inner Two-Pointer:</strong> The <code>while left &lt; right</code> loop runs at most <code>N</code> times in the worst case (each pointer traverses the remaining array once).</li>
<li><strong>Total:</strong> O(N log N + N * N * N) which simplifies to <strong>O(N^3)</strong>.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong><ul>
<li><strong>Sorting:</strong> Python's Timsort uses O(N) auxiliary space in the worst case, but often O(log N) or O(1) for nearly sorted arrays. If performed in-place by the language's sort, it can be considered O(1) for the algorithm's operational space.</li>
<li><strong>Result List:</strong> O(K), where <code>K</code> is the number of unique quadruplets found. In the worst theoretical case, <code>K</code> could be O(N^4) (e.g., if all combinations were distinct and valid), but practically it's often much less.</li>
<li><strong>Total Auxiliary Space (excluding output):</strong> Approximately <strong>O(1)</strong>, assuming in-place sorting or considering <code>N</code> for sorting if not.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n &lt; 4</code> (Not enough elements):</strong> Correctly handled by <code>if n &lt; 4: return []</code>.</li>
<li><strong>Empty <code>nums</code> array:</strong> Handled by the <code>n &lt; 4</code> check.</li>
<li><strong>All elements are the same (e.g., <code>[2,2,2,2]</code>, <code>target=8</code>):</strong> Correctly finds <code>[[2,2,2,2]]</code> due to the duplicate skipping logic.</li>
<li><strong>No solution found:</strong> Returns an empty list, as expected.</li>
<li><strong>Multiple solutions:</strong> All unique quadruplets are found and returned.</li>
<li><strong>Negative numbers, zero, and positive numbers mixed:</strong> Handled correctly because sorting works for all integers, and the sum/comparison logic remains valid.</li>
<li><strong>Target is zero:</strong> Handled correctly.</li>
<li><strong>Very large <code>target</code> or <code>nums</code> values:</strong> Python's arbitrary-precision integers handle this without overflow issues that might occur in languages with fixed-size integer types.</li>
<li><strong>Duplicate handling:</strong> The logic for skipping duplicates for <code>i</code>, <code>j</code>, <code>left</code>, and <code>right</code> pointers ensures that each unique quadruplet is added only once to the <code>result</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability of Optimizations:</strong> The current inline comments for optimizations are good. For extremely complex scenarios, abstracting these checks into helper functions might improve readability, but for this level, it's fine.</li>
<li><strong>Generalizing k-Sum:</strong> While this is a specific 4-Sum, the pattern (sorting + nested loops + two-pointers/recursion) can be generalized to <code>k-Sum</code>. A recursive <code>kSum</code> function could be implemented where <code>k=4</code>, and for <code>k=2</code> it calls the two-pointer solution. This might make the code more modular if <code>k</code> were a variable.</li>
<li><strong>Hash Map for 2-Sum:</strong> Instead of the two-pointer approach, after fixing <code>i</code> and <code>j</code>, one could iterate through the remaining elements and store <code>(target - nums[i] - nums[j] - nums[k])</code> in a hash set to quickly check for <code>nums[l]</code>. However, this would typically involve more memory overhead and a similar O(N) lookup/insertion, not changing the overall O(N^3) time complexity in the average case, and potentially being slower due to constant factors. The two-pointer approach is generally preferred here for its space efficiency and good performance on sorted arrays.</li>
<li><strong>Further Pruning:</strong> While the current pruning is effective, one could potentially add more bounds checks. For example, <code>nums[i] + nums[j] + nums[left] + nums[left+1]</code> vs <code>target</code> or <code>nums[i] + nums[j] + nums[right-1] + nums[right]</code> vs <code>target</code> within the inner loops. However, these might add more overhead than benefit.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no direct security vulnerabilities. The code operates on an input array of integers and does not interact with external systems, file systems, or user input in a way that would introduce security risks.</li>
<li><strong>Performance:</strong><ul>
<li>The O(N^3) time complexity means that for very large input arrays (e.g., N &gt; 2000), the execution time can become prohibitively long (2000^3 = 8 * 10^9 operations, which is too much).</li>
<li>For typical competitive programming constraints (N around 200-500), O(N^3) is generally acceptable (e.g., 500^3 = 1.25 * 10^8 operations).</li>
<li>The pruning optimizations are very important for practical performance, allowing the algorithm to finish much faster than the theoretical worst-case <code>N^3</code> when the <code>target</code> or <code>nums</code> distribution allows for early exits.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        n = len(nums)
        if n < 4:
            return []

        nums.sort()
        result = []

        for i in range(n - 3):
            # Skip duplicate for i
            if i > 0 and nums[i] == nums[i-1]:
                continue

            # Optimization: If the sum of nums[i] and the three smallest numbers after it is already greater than target,
            # then any combination with nums[i] will be too large. Break early.
            if nums[i] + nums[i+1] + nums[i+2] + nums[i+3] > target:
                break
            
            # Optimization: If the sum of nums[i] and the three largest numbers in the array
            # (effectively, the largest possible remaining) is less than target,
            # then nums[i] cannot be part of a solution, continue to the next i.
            if nums[i] + nums[n-3] + nums[n-2] + nums[n-1] < target:
                continue

            for j in range(i + 1, n - 2):
                # Skip duplicate for j
                if j > i + 1 and nums[j] == nums[j-1]:
                    continue

                # Optimization: Similar to i, check if sum with nums[j] and two smallest after it is too large.
                if nums[i] + nums[j] + nums[j+1] + nums[j+2] > target:
                    break
                
                # Optimization: Similar to i, check if sum with nums[j] and two largest remaining is too small.
                if nums[i] + nums[j] + nums[n-2] + nums[n-1] < target:
                    continue

                left = j + 1
                right = n - 1

                while left < right:
                    current_sum = nums[i] + nums[j] + nums[left] + nums[right]

                    if current_sum == target:
                        result.append([nums[i], nums[j], nums[left], nums[right]])
                        
                        # Skip duplicates for left
                        while left < right and nums[left] == nums[left+1]:
                            left += 1
                        # Skip duplicates for right
                        while left < right and nums[right] == nums[right-1]:
                            right -= 1
                        
                        left += 1
                        right -= 1
                    elif current_sum < target:
                        left += 1
                    else: # current_sum > target
                        right -= 1
        
        return result
```
