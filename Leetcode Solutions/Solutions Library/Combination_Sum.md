## Combination Sum
**Language:** python
**Tags:** python,backtracking,recursion,combinations

### Description:
<p>This code implements a solution for the "Combination Sum" problem. Given a list of distinct positive integers (<code>candidates</code>) and a target integer (<code>target</code>), it finds all unique combinations of numbers from <code>candidates</code> that sum up to <code>target</code>. The same number may be chosen from <code>candidates</code> an unlimited number of times.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Find all unique combinations of numbers from a given set (<code>candidates</code>) that sum up to a specific <code>target</code>.</li>
<li><strong>Key Constraint:</strong> Numbers in <code>candidates</code> can be reused multiple times in a single combination.</li>
<li><strong>Output:</strong> A list of lists, where each inner list is a valid combination.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core of the solution is a <strong>backtracking</strong> algorithm, which systematically explores all potential combinations.</p>
<ul>
<li><p><strong><code>backtrack(start, target, current_combination)</code> function:</strong></p>
<ul>
<li>This is a recursive helper function that attempts to build a combination.</li>
<li><code>start</code>: An index indicating where in <code>candidates</code> to begin considering numbers for the current combination. This is crucial for avoiding duplicate combinations (e.g., <code>[2,3]</code> and <code>[3,2]</code>) and ensuring numbers are picked in non-decreasing order.</li>
<li><code>target</code>: The remaining sum needed to reach the original target.</li>
<li><code>current_combination</code>: The list of numbers collected so far for the current path.</li>
</ul>
</li>
<li><p><strong>Base Cases:</strong></p>
<ul>
<li>If <code>target == 0</code>: A valid combination has been found. A <em>copy</em> of <code>current_combination</code> is appended to the <code>result</code> list. The function then returns.</li>
<li>If <code>target &lt; 0</code>: The current path has exceeded the target sum, so it's an invalid combination. The function returns.</li>
</ul>
</li>
<li><p><strong>Recursive Step:</strong></p>
<ul>
<li>The function iterates through <code>candidates</code> starting from the <code>start</code> index (<code>for i in range(start, len(candidates))</code>).</li>
<li><strong>Pruning:</strong> If <code>num &gt; target</code>, it skips this number and any subsequent numbers (due to sorting) because they would also exceed the current <code>target</code>.</li>
<li><strong>Include:</strong> The current <code>num</code> is added to <code>current_combination</code>.</li>
<li><strong>Recurse:</strong> <code>backtrack</code> is called recursively with:<ul>
<li><code>i</code>: The <code>start</code> index remains <code>i</code> (not <code>i+1</code>) to allow the <em>same number</em> to be chosen again (unlimited reuse).</li>
<li><code>target - num</code>: The remaining target sum.</li>
<li><code>current_combination</code>: The updated combination.</li>
</ul>
</li>
<li><strong>Backtrack (Exclude):</strong> After the recursive call returns, <code>num</code> is removed from <code>current_combination</code> (<code>current_combination.pop()</code>). This "undoes" the choice and allows exploration of other paths.</li>
</ul>
</li>
<li><p><strong>Initialization:</strong></p>
<ul>
<li><code>candidates.sort()</code>: The <code>candidates</code> list is sorted in ascending order. This is a crucial optimization for pruning and for implicitly handling the uniqueness of combinations.</li>
<li><code>result = []</code>: An empty list to store all valid combinations.</li>
<li><code>backtrack(0, target, [])</code>: The process starts with the first candidate (<code>start=0</code>), the original <code>target</code>, and an empty <code>current_combination</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm: Backtracking (Depth-First Search):</strong><ul>
<li><strong>Why:</strong> It's a natural fit for problems requiring exploration of all possible combinations or permutations by trying to build a solution step-by-step.</li>
<li><strong>Trade-off:</strong> Can lead to high time complexity for large inputs due to its exhaustive search nature.</li>
</ul>
</li>
<li><strong>Sorting <code>candidates</code>:</strong><ul>
<li><strong>Purpose 1 (Optimization):</strong> Enables early pruning (<code>if num &gt; target: continue</code>). Once a number exceeds the remaining target, all subsequent numbers (being larger) will also exceed it, so we can stop iterating for the current path.</li>
<li><strong>Purpose 2 (Uniqueness):</strong> Combined with the <code>start</code> parameter (<code>backtrack(i, ...)</code>) in the recursive call, sorting ensures that combinations are generated with elements in non-decreasing order (e.g., <code>[2,2,3]</code> instead of <code>[2,3,2]</code> or <code>[3,2,2]</code>). This implicitly prevents duplicate combinations that are just reorderings of the same elements.</li>
</ul>
</li>
<li><strong>Passing <code>start</code> index <code>i</code> (not <code>i+1</code>):</strong><ul>
<li><strong>Why:</strong> This is the mechanism that allows numbers to be reused multiple times in a combination.</li>
</ul>
</li>
<li><strong><code>result.append(current_combination[:])</code>:</strong><ul>
<li><strong>Why:</strong> <code>current_combination</code> is a list, and Python passes lists by reference. Appending <code>current_combination</code> directly would mean all entries in <code>result</code> would point to the <em>same mutable list</em>, which would be empty by the end of the execution due to <code>pop()</code> operations. Slicing <code>[:]</code> creates a shallow copy, ensuring each combination stored in <code>result</code> is independent.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong><ul>
<li><strong>Sorting:</strong> <code>O(N log N)</code> where <code>N</code> is the number of <code>candidates</code>.</li>
<li><strong>Backtracking:</strong> The number of recursive calls can be exponential. Let <code>M = target / min(candidates)</code> be the maximum possible depth of the recursion tree (if <code>min(candidates)</code> is positive). In the worst case, each node in the recursion tree can branch <code>N</code> times.</li>
<li>For each valid combination found, a list of length up to <code>M</code> is copied to <code>result</code>.</li>
<li>A loose upper bound often cited for similar problems is <code>O(N * M^N)</code> or <code>O(N * 2^T)</code>. A more refined perspective: <code>O(S * M + N * T)</code> where <code>S</code> is the number of valid solutions, <code>M</code> is the maximum length of a combination, and <code>N*T</code> is a rough upper bound on the number of states in the DFS tree.</li>
<li>It's inherently exponential in <code>target</code> or <code>N</code>, making it unsuitable for very large inputs.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong><ul>
<li><strong>Recursion Stack:</strong> <code>O(M)</code>, where <code>M</code> is the maximum depth of the recursion tree (<code>target / min(candidates)</code>).</li>
<li><strong><code>current_combination</code>:</strong> <code>O(M)</code> to store the current combination being built.</li>
<li><strong><code>result</code> List:</strong> <code>O(S * M)</code>, where <code>S</code> is the total number of unique combinations found, and <code>M</code> is the maximum length of a combination. This can be very significant if many combinations exist.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>candidates</code>:</strong><ul>
<li>The <code>for</code> loop <code>range(start, len(candidates))</code> will not execute. <code>result</code> will remain <code>[]</code>, which is correct.</li>
</ul>
</li>
<li><strong><code>target = 0</code>:</strong><ul>
<li>The initial call <code>backtrack(0, 0, [])</code> will immediately hit <code>target == 0</code>, append an empty list <code>[]</code> to <code>result</code>, and return. This is correct as an empty set sums to 0.</li>
</ul>
</li>
<li><strong>No combination possible:</strong><ul>
<li>If no path leads to <code>target == 0</code>, <code>result</code> will correctly remain <code>[]</code>.</li>
</ul>
</li>
<li><strong>Negative numbers in <code>candidates</code> or <code>target</code>:</strong><ul>
<li>The problem statement typically specifies positive integers. If negative numbers were allowed, <code>target &lt; 0</code> pruning might not prevent infinite loops (e.g., <code>target=1</code>, <code>candidates=[-1, 2]</code>) and <code>num &gt; target</code> optimization would be incorrect. Assuming positive integers as per typical problem context.</li>
</ul>
</li>
<li><strong>Duplicate values in <code>candidates</code> (e.g., <code>[2,2,3]</code>):</strong><ul>
<li>The standard "Combination Sum" problem assumes <code>candidates</code> contains <em>distinct</em> integers (e.g., <code>[2,3,6,7]</code>). If <code>candidates</code> could contain duplicates, the current <code>start</code> index logic and sorting would treat them as distinct <em>positions</em> initially, leading to <code>[[2(idx0), 2(idx1)]]</code> if <code>candidates=[2,2,3]</code> and target=4. This is generally the desired behavior for problems that allow duplicates in <code>candidates</code> (e.g., "Combination Sum II" where each number from candidates can only be used once, but the candidate list itself might have duplicates). For "Combination Sum I", <code>candidates</code> is usually distinct. Given the <code>start</code> index and sorting, it correctly identifies unique <em>sets</em> of numbers.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Dynamic Programming (for counting):</strong> If the problem only asked for the <em>count</em> of combinations (not the combinations themselves), a dynamic programming approach (e.g., <code>dp[i]</code> storing the number of ways to sum to <code>i</code>) would be more efficient, typically <code>O(N * target)</code>. However, for generating all combinations, DP tables usually store counts or boolean flags, not lists of lists directly due to memory constraints.</li>
<li><strong>Pre-filtering Candidates:</strong> For very large <code>candidates</code> lists, one could initially filter out numbers greater than <code>target</code> before sorting, but <code>candidates.sort()</code> already handles this implicitly well with the <code>if num &gt; target: continue</code> check.</li>
<li><strong>Readability:</strong> The code is already quite clear and follows standard backtracking patterns. No significant readability improvements are immediately apparent.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The exponential time complexity means this solution will perform poorly for large <code>target</code> values or a large number of <code>candidates</code> (e.g., <code>target=100</code>, <code>candidates=[1,2,3,4,5]</code>). Be mindful of input constraints for production environments.</li>
<li><strong>Security:</strong> There are no inherent security vulnerabilities in this algorithm as it only processes integer inputs and doesn't interact with external systems or user-controlled data in a sensitive way.</li>
</ul>


### Code:
```python
class Solution(object):
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        result = []
        
        def backtrack(start, target, current_combination):
            if target == 0:
                result.append(current_combination[:])
                return
            if target < 0:
                return
            
            for i in range(start, len(candidates)):
                num = candidates[i]
                if num > target:
                    continue
                current_combination.append(num)
                backtrack(i, target - num, current_combination)
                current_combination.pop()
        
        candidates.sort()  # Sort to optimize by skipping numbers > target
        backtrack(0, target, [])
        return result
```
