## Longest Substring Without Repeating Characters
**Language:** python
**Tags:** python,sliding window,string,two pointers

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code aims to find the <strong>length of the longest substring within a given string <code>s</code> that does not contain any repeating characters</strong>.</p>
<p>For example:</p>
<ul>
<li><code>"abcabcbb"</code> -&gt; <code>"abc"</code>, length 3</li>
<li><code>"bbbbb"</code> -&gt; <code>"b"</code>, length 1</li>
<li><code>"pwwkew"</code> -&gt; <code>"wke"</code> or <code>"kew"</code>, length 3</li>
</ul>
<p>It's a classic problem frequently encountered in technical interviews, testing understanding of string manipulation and data structures.</p>
<h3>2. How It Works</h3>
<p>The solution employs the "sliding window" technique. It maintains a window defined by two pointers, <code>left</code> and <code>right</code>, and a set (<code>charSet</code>) to keep track of unique characters within the current window.</p>
<p>The main steps are:</p>
<ul>
<li><strong>Initialize</strong>:<ul>
<li><code>charSet</code>: An empty set to store characters in the current window.</li>
<li><code>left</code>: A pointer marking the start of the window, initialized to 0.</li>
<li><code>maxLength</code>: Stores the maximum length found so far, initialized to 0.</li>
</ul>
</li>
<li><strong>Expand Window</strong>: The <code>right</code> pointer iterates through the string, attempting to expand the window to include <code>s[right]</code>.</li>
<li><strong>Handle Duplicates</strong>:<ul>
<li>If <code>s[right]</code> is <strong>already present</strong> in <code>charSet</code> (meaning it's a duplicate within the current window), the window needs to be shrunk from the left.</li>
<li>The <code>while</code> loop repeatedly removes <code>s[left]</code> from <code>charSet</code> and increments <code>left</code> until the duplicate <code>s[right]</code> is no longer in the set.</li>
</ul>
</li>
<li><strong>Add Character &amp; Update Length</strong>:<ul>
<li>Once <code>s[right]</code> can be added without creating a duplicate in the window, it's added to <code>charSet</code>.</li>
<li>The length of the current valid window (<code>right - left + 1</code>) is calculated, and <code>maxLength</code> is updated if the current window is longer.</li>
</ul>
</li>
<li><strong>Return Result</strong>: After iterating through the entire string, <code>maxLength</code> holds the length of the longest substring without repeating characters.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sliding Window Algorithm</strong>: This is the core algorithmic choice. It's efficient because it processes each character a limited number of times (at most twice by the <code>left</code> and <code>right</code> pointers), avoiding redundant re-checks of substrings.</li>
<li><strong><code>set</code> for <code>charSet</code></strong>:<ul>
<li><strong>Pros</strong>: Sets provide average O(1) time complexity for <code>add</code>, <code>remove</code>, and <code>in</code> (membership check) operations. This is crucial for the algorithm's performance, as these operations are performed frequently.</li>
<li><strong>Trade-offs</strong>: Requires additional space to store characters in the current window. For a very large character set (e.g., full Unicode), this could consume more memory than a fixed-size frequency array.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Where N is the length of the input string <code>s</code>.</li>
<li>Both the <code>left</code> and <code>right</code> pointers traverse the string at most once. Each character is added to the <code>charSet</code> once and removed from it at most once.</li>
<li>Set operations (add, remove, check) take O(1) time on average.</li>
<li>Therefore, the total time complexity is linear with respect to the string's length.</li>
</ul>
</li>
<li><strong>Space Complexity: O(min(N, M))</strong><ul>
<li>Where N is the length of the input string and M is the size of the character set (e.g., 26 for English alphabet, 128 for ASCII, 256 for extended ASCII, or potentially much larger for full Unicode).</li>
<li>In the worst case (e.g., a string with all unique characters), the <code>charSet</code> will store up to <code>N</code> characters. If the character set is smaller than <code>N</code> (e.g., an ASCII string of length 1000), the set will store at most <code>M</code> unique characters.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly:</p>
<ul>
<li><strong>Empty string (<code>s = ""</code>)</strong>:<ul>
<li><code>len(s)</code> is 0, the <code>for</code> loop does not execute. <code>maxLength</code> remains 0, which is correct.</li>
</ul>
</li>
<li><strong>String with a single character (<code>s = "a"</code>)</strong>:<ul>
<li><code>right</code> = 0. 'a' is added to <code>charSet</code>. <code>maxLength</code> becomes <code>max(0, 0-0+1) = 1</code>. Correct.</li>
</ul>
</li>
<li><strong>String with all unique characters (<code>s = "abcde"</code>)</strong>:<ul>
<li><code>left</code> remains 0 throughout. The <code>right</code> pointer expands, adding each unique character. <code>maxLength</code> correctly becomes <code>len(s)</code>. Correct.</li>
</ul>
</li>
<li><strong>String with all repeating characters (<code>s = "aaaaa"</code>)</strong>:<ul>
<li><code>right</code> = 0: 'a' added, <code>maxLength</code> = 1.</li>
<li><code>right</code> = 1: 'a' is in <code>charSet</code>. <code>while</code> loop runs: 'a' is removed from <code>charSet</code>, <code>left</code> becomes 1. Loop ends. 'a' is added to <code>charSet</code>. <code>maxLength</code> remains <code>max(1, 1-1+1) = 1</code>. This pattern continues. Correct, as the longest unique substring is "a".</li>
</ul>
</li>
<li><strong>Mixed string (<code>s = "pwwkew"</code>)</strong>:<ul>
<li>Correctly identifies "wke" or "kew" as the longest unique substrings, returning 3.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>Using a Dictionary (Map) for Character Indices</strong>:</p>
<ul>
<li>Instead of just storing characters, a dictionary (<code>char_to_index = {}</code>) could store <code>character -&gt; last_seen_index</code>.</li>
<li>When a duplicate <code>s[right]</code> is encountered, instead of iteratively removing characters from <code>left</code>, you can directly jump <code>left</code> to <code>max(left, char_to_index[s[right]] + 1)</code>.</li>
<li>This approach avoids the <code>while</code> loop for shrinking the window, potentially offering a slight constant-factor performance improvement in cases with many duplicates far apart, but it doesn't change the overall O(N) time complexity.</li>
<li>The current <code>set</code> based solution is often preferred for its simplicity and clear logic, and its performance is already optimal in terms of Big-O.</li>
</ul>
</li>
<li><p><strong>Readability</strong>: The current code is very readable with clear variable names and a straightforward implementation of the sliding window. No significant readability improvements are needed.</p>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The solution is highly performant (O(N) time complexity), making it suitable for large input strings. Python's built-in <code>set</code> operations are implemented in C and are very efficient.</li>
<li><strong>Security</strong>: For this specific problem, there are no direct security implications. The code only reads and processes a string; it doesn't interact with external systems or handle sensitive data in a way that would introduce vulnerabilities.</li>
</ul>


### Code:
```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        charSet = set()
        left = 0
        maxLength = 0

        for right in range(len(s)):
            while s[right] in charSet:
                charSet.remove(s[left])
                left += 1
            charSet.add(s[right])
            maxLength = max(maxLength, right - left + 1)
        
        return maxLength
```
