## 3Sum
**Language:** python
**Tags:** array,sorting,two-pointers,triplets

### Description:
<p>As a senior code reviewer and educator, I've analyzed the provided Python solution for the <code>threeSum</code> problem.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements a solution to the "3Sum" problem, which aims to find all unique triplets <code>[a, b, c]</code> in a given integer array <code>nums</code> such that <code>a + b + c = 0</code>. The solution must not contain duplicate triplets.</p>
<h3>2. How It Works</h3>
<p>The algorithm follows a well-established pattern for finding combinations that sum to a target, often called the "two-pointer" approach after an initial sort.</p>
<ol>
<li><strong>Sort the Array</strong>: The input array <code>nums</code> is first sorted in ascending order. This is a crucial step that enables both the two-pointer technique and efficient duplicate handling.</li>
<li><strong>Iterate and Fix First Element</strong>: The code iterates through the sorted array using an outer loop with index <code>i</code>. For each <code>nums[i]</code>, this <code>nums[i]</code> is considered the first element of a potential triplet.<ul>
<li><strong>Skip Duplicates for <code>i</code></strong>: To avoid duplicate triplets, if <code>nums[i]</code> is the same as the previous element <code>nums[i-1]</code> (and <code>i &gt; 0</code>), the loop skips this iteration.</li>
</ul>
</li>
<li><strong>Two-Pointer Search</strong>: For each fixed <code>nums[i]</code>, two pointers, <code>left</code> (starting at <code>i + 1</code>) and <code>right</code> (starting at <code>n - 1</code>), are used to search for the remaining two elements in the rest of the array.<ul>
<li><strong>Calculate Sum</strong>: The <code>current_sum</code> is calculated as <code>nums[i] + nums[left] + nums[right]</code>.</li>
<li><strong>Found Triplet</strong>: If <code>current_sum == 0</code>, a valid triplet is found <code>[nums[i], nums[left], nums[right]]</code>. This triplet is added to the <code>result</code> list.<ul>
<li><strong>Skip Duplicates for <code>left</code> and <code>right</code></strong>: After finding a triplet, both <code>left</code> and <code>right</code> pointers are moved inwards. To avoid duplicate triplets, additional inner <code>while</code> loops skip over consecutive elements that are identical to the current <code>nums[left]</code> and <code>nums[right]</code> respectively.</li>
<li>Finally, <code>left</code> and <code>right</code> are moved one step further inward (<code>left += 1</code>, <code>right -= 1</code>) to continue the search for other unique triplets.</li>
</ul>
</li>
<li><strong>Adjust Pointers</strong>:<ul>
<li>If <code>current_sum &lt; 0</code>, it means the sum is too small, so <code>left</code> is incremented to try a larger second number.</li>
<li>If <code>current_sum &gt; 0</code>, it means the sum is too large, so <code>right</code> is decremented to try a smaller third number.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Return Result</strong>: After iterating through all possible <code>i</code>, the collected list of unique triplets is returned.</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sorting</strong>: The fundamental decision to sort the array <code>nums</code> is key.<ul>
<li>It allows the efficient <strong>two-pointer approach</strong> to work because if <code>nums[i] + nums[left] + nums[right]</code> is too small, we <em>know</em> increasing <code>left</code> will increase the sum (as the array is sorted), and similarly for <code>right</code>.</li>
<li>It simplifies <strong>duplicate handling</strong> significantly. By checking <code>nums[i] == nums[i-1]</code>, or <code>nums[left] == nums[left+1]</code>, duplicates can be skipped easily since identical values are adjacent.</li>
</ul>
</li>
<li><strong>Two-Pointer Technique</strong>: This is an efficient way to find two numbers that sum to a specific target (here, <code>-nums[i]</code>) within a sorted subarray. It reduces the search space from <code>O(N)</code> for each <code>left</code> and <code>right</code> to <code>O(N)</code> for the entire inner loop.</li>
<li><strong>Explicit Duplicate Skipping</strong>: The inclusion of <code>if i &gt; 0 and nums[i] == nums[i-1]: continue</code> and the <code>while</code> loops for <code>left</code> and <code>right</code> pointers after finding a triplet are essential. Without these, the <code>result</code> list would contain duplicate triplets (e.g., <code>[-1, 0, 1]</code> and then another <code>[-1, 0, 1]</code> if the input had multiple <code>0</code>s or <code>1</code>s).</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>nums.sort()</code>: <code>O(N log N)</code> where N is the length of <code>nums</code>. Python's Timsort is used.</li>
<li>Outer loop (<code>for i</code>): <code>O(N)</code> iterations.</li>
<li>Inner <code>while left &lt; right</code> loop: In the worst case, <code>left</code> traverses almost to <code>right</code>, making it <code>O(N)</code> for each <code>i</code>.</li>
<li>Duplicate skipping <code>while</code> loops: These also contribute to moving <code>left</code> or <code>right</code> and are amortized within the <code>O(N)</code> of the inner <code>while</code> loop.</li>
<li>Total: <code>O(N log N + N * N) = O(N^2)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li>Sorting: Python's Timsort typically uses <code>O(N)</code> auxiliary space in the worst case.</li>
<li><code>result</code> list: <code>O(K)</code> where <code>K</code> is the number of unique triplets found. In the worst case, <code>K</code> could be <code>O(N^3)</code> but practically much less (e.g., <code>N^2</code> in dense cases). This is output space.</li>
<li>Auxiliary variables (<code>left</code>, <code>right</code>, <code>current_sum</code>): <code>O(1)</code>.</li>
<li>Total (auxiliary space, excluding output): <code>O(N)</code> due to sorting. If the sorting algorithm was strictly in-place with <code>O(1)</code> auxiliary space, then overall would be <code>O(1)</code> (excluding output).</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty array or array with fewer than 3 elements</strong>:<ul>
<li><code>n &lt; 3</code>: The <code>for i in range(n - 2)</code> loop will not execute, and an empty <code>result</code> list will be returned, which is correct.</li>
</ul>
</li>
<li><strong>Array with all zeros <code>[0, 0, 0]</code></strong>:<ul>
<li>The code correctly identifies <code>[0, 0, 0]</code> as a triplet and adds it once.</li>
</ul>
</li>
<li><strong>Array with many duplicate numbers <code>[-1, 0, 1, 2, -1, -4]</code></strong>:<ul>
<li>Sorted: <code>[-4, -1, -1, 0, 1, 2]</code>.</li>
<li>The duplicate skipping logic (<code>if i &gt; 0 and nums[i] == nums[i-1]</code>, and the <code>while</code> loops after finding a sum) ensures that only unique triplets like <code>[-1, 0, 1]</code> and <code>[-1, -1, 2]</code> are returned, without redundant entries.</li>
</ul>
</li>
<li><strong>All negative or all positive numbers</strong>:<ul>
<li>If all numbers are negative or all positive, no triplet can sum to zero. The <code>current_sum</code> will always be <code>&lt; 0</code> or <code>&gt; 0</code>, respectively, and no triplets will be added. This is correct.</li>
</ul>
</li>
<li><strong>Correctness</strong>: The combination of sorting, the two-pointer approach, and meticulous duplicate handling guarantees that all unique triplets summing to zero are found exactly once.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Early Exit for <code>n &lt; 3</code></strong>: While the existing code implicitly handles <code>n &lt; 3</code> by having the outer loop not run, an explicit check at the beginning could improve readability:<pre><code class="language-python">if n &lt; 3:
    return []
</code></pre>
</li>
<li><strong>Clarity of Duplicate Skipping</strong>: The duplicate skipping logic is compact but can be tricky to read. Adding comments to the <code>while</code> loops for skipping duplicates could improve understanding.<pre><code class="language-python">            if current_sum == 0:
                result.append([nums[i], nums[left], nums[right]])
                # Skip duplicate values for the second element to avoid identical triplets
                while left &lt; right and nums[left] == nums[left + 1]:
                    left += 1
                # Skip duplicate values for the third element
                while left &lt; right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
</code></pre>
</li>
<li><strong>Alternative (Hash Set Approach - Less Efficient for this problem):</strong><ul>
<li>One could use a hash set to store seen numbers or pairs to achieve <code>O(N^2)</code> on average. However, correctly handling duplicates and ensuring unique <em>triplets</em> (not just unique numbers in a triplet) with hash sets can be more complex than with the sorted two-pointer approach, often leading to worse performance or higher space usage for intermediate sets. For 3Sum, the sorted two-pointer approach is generally preferred for its optimal time complexity and simpler duplicate handling.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(N^2)</code> time complexity is considered optimal for the 3Sum problem in the general case (barring very specific constraints like bounded integer ranges allowing radix sort or hashing tricks). There's no immediately obvious way to achieve better worst-case time complexity.</li>
<li><strong>Security</strong>: This algorithm operates on a list of integers and produces another list of integers. There are no direct security implications or vulnerabilities such as injection flaws, resource exhaustion (beyond typical memory use for large <code>N</code>), or data exposure. It's a purely computational problem.</li>
</ul>


### Code:
```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums.sort()
        result = []
        n = len(nums)

        for i in range(n - 2):
            # Skip duplicate values for the first element of the triplet
            if i > 0 and nums[i] == nums[i-1]:
                continue

            left, right = i + 1, n - 1
            while left < right:
                current_sum = nums[i] + nums[left] + nums[right]

                if current_sum == 0:
                    result.append([nums[i], nums[left], nums[right]])
                    # Skip duplicate values for the second element
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    # Skip duplicate values for the third element
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1
                    left += 1
                    right -= 1
                elif current_sum < 0:
                    left += 1
                else: # current_sum > 0
                    right -= 1
        
        return result
```
