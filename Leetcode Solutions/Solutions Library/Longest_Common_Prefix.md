## Longest Common Prefix
**Language:** python
**Tags:** python,string,algorithm,prefix

### Description:
<p>This code implements an algorithm to find the longest common prefix among a list of strings.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Given an array of strings <code>strs</code>, find the longest common prefix string amongst them.</li>
<li><strong>Example:</strong> For <code>["flower", "flow", "flight"]</code>, the LCP is <code>"fl"</code>.</li>
<li><strong>Edge Case:</strong> If there is no common prefix, return an empty string <code>""</code>.</li>
<li><strong>Purpose:</strong> This is a fundamental string processing task, often used in autocomplete features, file system navigation, or data normalization.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a horizontal scanning approach where it iteratively shortens a candidate prefix:</p>
<ol>
<li><strong>Handle Empty Input:</strong> If the input list <code>strs</code> is empty, an empty string <code>""</code> is immediately returned.</li>
<li><strong>Initialize Prefix:</strong> The first string in the list (<code>strs[0]</code>) is taken as the initial candidate for the longest common prefix.</li>
<li><strong>Iterate and Adjust:</strong><ul>
<li>The code then iterates through the <em>rest</em> of the strings in the <code>strs</code> list (from the second string onwards).</li>
<li>For each string <code>strs[i]</code>, it enters a <code>while</code> loop that continues as long as the current <code>prefix</code> is <em>not</em> a prefix of <code>strs[i]</code> (i.e., <code>strs[i].find(prefix) != 0</code>).</li>
<li><strong>Shorten Prefix:</strong> Inside this <code>while</code> loop, if <code>prefix</code> is not found at the beginning of <code>strs[i]</code>, the <code>prefix</code> is shortened by one character from its end (<code>prefix = prefix[:-1]</code>).</li>
<li><strong>Empty Prefix Check:</strong> If <code>prefix</code> becomes an empty string <code>""</code> during this shortening process, it means there's no common prefix among <em>all</em> strings, so the function immediately returns <code>""</code>.</li>
</ul>
</li>
<li><strong>Return Result:</strong> After iterating through all strings and making necessary adjustments, the remaining <code>prefix</code> is the longest common prefix among all strings, and it is returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Algorithm Choice: Horizontal Scanning</strong><ul>
<li>The algorithm starts with the first string as a potential LCP and then progressively refines it by comparing it against subsequent strings.</li>
<li>It's an intuitive and relatively simple approach to implement.</li>
</ul>
</li>
<li><strong>Data Structures:</strong><ul>
<li>Uses a <code>List[str]</code> for input and <code>str</code> for the prefix. No complex data structures are introduced, keeping the code simple.</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Pros:</strong> Straightforward to understand and implement using built-in string methods (<code>find</code>, slicing).</li>
<li><strong>Cons:</strong> Involves repeated string slicing, which creates new string objects in memory. The <code>find</code> operation, even when optimized for prefix checks, can be called many times, potentially leading to performance bottlenecks for very long strings or a large number of strings.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let:</p>
<ul>
<li><p><code>N</code> be the number of strings in <code>strs</code>.</p>
</li>
<li><p><code>L_max</code> be the length of the longest string in <code>strs</code>.</p>
</li>
<li><p><code>L_0</code> be the length of the first string <code>strs[0]</code> (which serves as the initial <code>prefix</code>).</p>
</li>
<li><p><strong>Time Complexity:</strong> <code>O(N * L_0 * L_max)</code> in the worst case, but more precisely <code>O(N * L_0^2)</code> if <code>str.find</code> at index 0 is <code>O(L_p)</code> and slicing is <code>O(L_p)</code>.</p>
<ul>
<li>The outer loop runs <code>N-1</code> times.</li>
<li>Inside the outer loop, for each string <code>strs[i]</code>:<ul>
<li>The <code>while</code> loop condition <code>strs[i].find(prefix) != 0</code> checks if <code>prefix</code> is a prefix of <code>strs[i]</code>. In Python, <code>str.find</code> is highly optimized (e.g., Boyer-Moore-Horspool). Checking for a match at index 0 specifically can be <code>O(len(prefix))</code>.</li>
<li>The <code>prefix = prefix[:-1]</code> operation creates a new string by copying <code>len(prefix) - 1</code> characters, costing <code>O(len(prefix))</code>.</li>
<li>In the worst case (e.g., <code>["abcde", "abcz", "abcy"]</code>), for each string <code>strs[i]</code>, the <code>prefix</code> might be shortened character by character. If the initial <code>prefix</code> has length <code>L_0</code>, the <code>while</code> loop could run up to <code>L_0</code> times.</li>
<li>The total cost for comparing and shortening <code>prefix</code> against a <em>single</em> <code>strs[i]</code> would be <code>sum_{k=1 to L_0} O(k) = O(L_0^2)</code>.</li>
</ul>
</li>
<li>Therefore, the total time complexity is <code>O(N * L_0^2)</code>. Since <code>L_0</code> can be up to <code>L_max</code>, this can be expressed as <code>O(N * L_max^2)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong> <code>O(L_max)</code></p>
<ul>
<li>The <code>prefix</code> variable stores a string whose maximum length is <code>L_max</code>.</li>
<li>Although slicing <code>prefix[:-1]</code> creates new string objects, only one <code>prefix</code> string exists at any given time, so the additional space usage is proportional to the current prefix length.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty input list <code>strs = []</code>:</strong> Handled by <code>if not strs: return ""</code>. Correct.</li>
<li><strong>List with one string <code>strs = ["apple"]</code>:</strong> <code>prefix</code> becomes "apple", the loop for <code>i</code> doesn't run, "apple" is returned. Correct.</li>
<li><strong>List contains empty strings <code>strs = ["abc", "", "ab"]</code>:</strong><ul>
<li><code>prefix</code> starts as "abc".</li>
<li>When compared with <code>""</code>, <code>"".find("abc")</code> returns -1. <code>prefix</code> is shortened repeatedly until it becomes <code>""</code>.</li>
<li>The <code>if not prefix:</code> check triggers, returning <code>""</code>. Correct, as an empty string has no common prefix with anything non-empty.</li>
</ul>
</li>
<li><strong>No common prefix <code>strs = ["flower", "dog", "car"]</code>:</strong><ul>
<li><code>prefix</code> starts as "flower".</li>
<li>When compared with "dog", <code>prefix</code> will be shortened until it becomes <code>""</code>.</li>
<li>The <code>if not prefix:</code> check triggers, returning <code>""</code>. Correct.</li>
</ul>
</li>
<li><strong>All strings identical <code>strs = ["test", "test", "test"]</code>:</strong><ul>
<li><code>prefix</code> starts as "test".</li>
<li>For all strings, <code>strs[i].find("test")</code> will be 0. <code>prefix</code> remains "test". Returns "test". Correct.</li>
</ul>
</li>
<li><strong>Common prefix is part of the first string <code>strs = ["apple", "app", "apricot"]</code>:</strong><ul>
<li><code>prefix</code> starts as "apple".</li>
<li>When compared with "app", <code>prefix</code> shortens to "app".</li>
<li>When compared with "apricot", <code>prefix</code> shortens to "ap". Returns "ap". Correct.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Vertical Scanning (More Efficient):</strong></p>
<ul>
<li>Instead of shortening a prefix, iterate character by character from left to right.</li>
<li>For each character position <code>j</code>, compare <code>strs[0][j]</code> with <code>strs[1][j]</code>, <code>strs[2][j]</code>, ..., <code>strs[N-1][j]</code>.</li>
<li>If a mismatch is found or any string ends, the common prefix is <code>strs[0][:j]</code>.</li>
<li><strong>Complexity:</strong> <code>O(N * L_min)</code>, where <code>L_min</code> is the length of the shortest string. This is often more efficient than the current method, especially for long strings.</li>
<li><strong>Example Implementation Idea:</strong><pre><code class="language-python">if not strs: return ""
for j in range(len(strs[0])):
    char = strs[0][j]
    for i in range(1, len(strs)):
        if j == len(strs[i]) or strs[i][j] != char:
            return strs[0][:j]
return strs[0]
</code></pre>
</li>
</ul>
</li>
<li><p><strong>Divide and Conquer:</strong></p>
<ul>
<li>Recursively solve the problem by splitting the array of strings into two halves, finding the LCP for each half, and then finding the LCP of those two results.</li>
<li><strong>Complexity:</strong> <code>O(N * L_min)</code>.</li>
</ul>
</li>
<li><p><strong>Trie (Prefix Tree):</strong></p>
<ul>
<li>Insert all strings into a Trie.</li>
<li>Traverse the Trie from the root. Continue traversing as long as a node has only one child and is not marked as the end of a word.</li>
<li>The path traversed represents the LCP.</li>
<li><strong>Complexity:</strong> Building the Trie is <code>O(S)</code> (where <code>S</code> is the sum of lengths of all strings). Finding the LCP is <code>O(L_min)</code>. This is highly efficient if you need to perform multiple LCP queries or if <code>N</code> is very large.</li>
</ul>
</li>
<li><p><strong>Python's <code>os.path.commonprefix</code>:</strong></p>
<ul>
<li>For competitive programming, usually not allowed, but in general Python development, <code>os.path.commonprefix(strs)</code> can be used. It's implemented in C and is highly optimized.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The current <code>O(N * L_max^2)</code> complexity can be a performance bottleneck for inputs with many strings or very long strings. Python's string operations (slicing and <code>find</code>) involve creating new string objects and copying data, which adds overhead.<ul>
<li>For <code>N = 2 * 10^4</code> and <code>L_max = 2 * 10^2</code> (typical constraints for competitive programming), <code>N * L_max^2 = 2 * 10^4 * (2 * 10^2)^2 = 2 * 10^4 * 4 * 10^4 = 8 * 10^8</code>, which is too slow.</li>
<li>A <code>O(N * L_min)</code> approach like vertical scanning would be <code>2 * 10^4 * 2 * 10^2 = 4 * 10^6</code>, which is much more feasible.</li>
</ul>
</li>
<li><strong>Security:</strong> This algorithm itself does not introduce direct security vulnerabilities as it only processes and compares string data. It doesn't interpret strings as code, paths, or commands that could lead to injection attacks.</li>
</ul>


### Code:
```python
class Solution(object):
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        if not strs:
            return ""

        prefix = strs[0]
        for i in range(1, len(strs)):
            while strs[i].find(prefix) != 0:
                prefix = prefix[:-1]
                if not prefix:
                    return ""
        return prefix
```
