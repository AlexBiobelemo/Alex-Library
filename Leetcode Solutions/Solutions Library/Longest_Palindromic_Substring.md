## Longest Palindromic Substring
**Language:** python
**Tags:** string,palindrome,substring,two pointers

### Description:
<p>This code solves the "Longest Palindromic Substring" problem, a common challenge in string manipulation.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to find the longest palindromic substring within a given input string <code>s</code>. A palindrome is a sequence of characters that reads the same forwards and backwards (e.g., "madam", "racecar"). The function returns this longest palindromic substring.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses an "expand from center" strategy:</p>
<ol>
<li><p><strong>Initialization:</strong> It initializes <code>start</code> to 0 and <code>max_len</code> to 0, which will track the starting index and length of the longest palindrome found so far. It also handles the edge case of an empty input string.</p>
</li>
<li><p><strong><code>expand_from_center</code> Helper Function:</strong></p>
<ul>
<li>This private helper function takes the string <code>s</code> and two indices, <code>left</code> and <code>right</code>.</li>
<li>It attempts to expand outwards from these <code>left</code> and <code>right</code> pointers, decrementing <code>left</code> and incrementing <code>right</code>, as long as the characters <code>s[left]</code> and <code>s[right]</code> match and the pointers remain within string bounds.</li>
<li>Once the expansion stops (due to mismatch or boundary hit), it returns the length of the palindrome found: <code>right - left - 1</code>. This effectively calculates the length of the substring <code>s[left+1 : right]</code>.</li>
</ul>
</li>
<li><p><strong>Main Loop:</strong></p>
<ul>
<li>The code iterates through each character index <code>i</code> in the input string <code>s</code>. Each <code>i</code> is considered a potential center of a palindrome.</li>
<li><strong>Odd Length Palindromes:</strong> It calls <code>expand_from_center(s, i, i)</code> to find the longest palindrome centered at <code>i</code> (e.g., "aba" centered at 'b').</li>
<li><strong>Even Length Palindromes:</strong> It calls <code>expand_from_center(s, i, i + 1)</code> to find the longest palindrome centered <em>between</em> <code>i</code> and <code>i+1</code> (e.g., "abba" centered between the two 'b's).</li>
<li><strong>Update Longest:</strong> It takes the maximum of these two lengths (<code>len1</code>, <code>len2</code>) as <code>current_len</code>.</li>
<li>If <code>current_len</code> is greater than the <code>max_len</code> found so far, <code>max_len</code> is updated. The <code>start</code> index is then calculated using the formula <code>i - (current_len - 1) // 2</code>. This formula cleverly works for both odd and even length palindromes to find the correct starting index.</li>
</ul>
</li>
<li><p><strong>Result:</strong> After checking all possible centers, the function returns the substring <code>s[start : start + max_len]</code>.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Expand from Center Strategy:</strong> This is the core algorithmic approach.<ul>
<li><strong>Pro:</strong> It's intuitive, relatively simple to implement, and handles both odd and even length palindromes efficiently without needing separate logic. It avoids the overhead of dynamic programming table creation.</li>
<li><strong>Con:</strong> Its time complexity is O(N^2), which is not optimal for very large strings (Manacher's Algorithm is O(N)).</li>
</ul>
</li>
<li><strong>Helper Function <code>expand_from_center</code>:</strong><ul>
<li>Encapsulates the core logic of palindrome expansion, making the main loop cleaner and more readable.</li>
<li>Reduces code duplication for odd and even length checks.</li>
</ul>
</li>
<li><strong>No Dynamic Programming Table:</strong> Unlike some solutions that build a 2D boolean array <code>dp[i][j]</code> to indicate if <code>s[i...j]</code> is a palindrome, this approach uses O(1) extra space.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: O(N^2)</strong></p>
<ul>
<li>The outer <code>for</code> loop iterates <code>N</code> times, where <code>N</code> is the length of the string <code>s</code>.</li>
<li>Inside the loop, <code>expand_from_center</code> is called twice. In the worst case (e.g., a string like "aaaaa" or "abacaba"), this helper function might expand outwards almost <code>N/2</code> times.</li>
<li>Therefore, the total time complexity is roughly <code>N * (N/2)</code> which simplifies to O(N^2).</li>
</ul>
</li>
<li><p><strong>Space Complexity: O(1)</strong></p>
<ul>
<li>The algorithm uses only a few constant-space variables (<code>start</code>, <code>max_len</code>, <code>left</code>, <code>right</code>, <code>len1</code>, <code>len2</code>, <code>current_len</code>).</li>
<li>It does not use any auxiliary data structures that scale with the input size (like a DP table or large lists). The slice operation at the end <code>s[start : start + max_len]</code> creates a new string object, but this is part of the output and not auxiliary space used <em>during</em> computation.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty String (<code>""</code>):</strong><ul>
<li>Correctly handled by <code>if not s: return ""</code>.</li>
</ul>
</li>
<li><strong>Single Character String (<code>"a"</code>):</strong><ul>
<li><code>i=0</code>: <code>len1</code> = 1 (from <code>expand(s,0,0)</code>), <code>len2</code> = 0 (from <code>expand(s,0,1)</code> as <code>1 &lt; len(s)</code> is false). <code>current_len</code> = 1. <code>max_len</code> becomes 1. <code>start</code> becomes <code>0 - (1-1)//2 = 0</code>.</li>
<li>Returns <code>s[0:1]</code> which is <code>"a"</code>. Correct.</li>
</ul>
</li>
<li><strong>String with All Same Characters (<code>"aaaaa"</code>):</strong><ul>
<li>The <code>expand_from_center</code> function will correctly identify the full string as the longest palindrome.</li>
<li><code>max_len</code> will eventually be <code>len(s)</code>, and <code>start</code> will be 0. Correct.</li>
</ul>
</li>
<li><strong>String with No Palindromes Longer Than 1 (<code>"abcde"</code>):</strong><ul>
<li>For each <code>i</code>, <code>len1</code> will be 1 (single character palindrome), and <code>len2</code> will be 0.</li>
<li><code>max_len</code> will remain 1, and <code>start</code> will be 0 (from the first character).</li>
<li>Returns <code>"a"</code>. This is correct, as any single character is a palindrome and is the longest in this case.</li>
</ul>
</li>
<li><strong>Correctness of <code>start</code> Calculation <code>i - (current_len - 1) // 2</code>:</strong><ul>
<li><strong>Odd length palindrome:</strong> If <code>current_len = 2k + 1</code>, then <code>(current_len - 1) // 2 = k</code>. The palindrome starts at <code>i - k</code>. So <code>i - k</code> correctly becomes <code>i - (current_len - 1) // 2</code>.</li>
<li><strong>Even length palindrome:</strong> If <code>current_len = 2k</code>, then <code>(current_len - 1)</code> is <code>2k - 1</code> (an odd number). <code>(current_len - 1) // 2</code> is <code>k - 1</code>. The palindrome starts at <code>i - k + 1</code>. So <code>i - (k - 1)</code> correctly becomes <code>i - current_len // 2 + 1</code>, which is the correct start index.</li>
<li>This unified formula is elegant and correct for both cases.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Manacher's Algorithm (O(N) Time):</strong><ul>
<li>This is the theoretically most optimal solution. It achieves O(N) time complexity by preprocessing the string (e.g., adding sentinels like <code>#a#b#a#</code>) and using a clever dynamic programming approach to avoid re-calculating palindrome lengths.</li>
<li><strong>Trade-off:</strong> Significantly more complex to understand and implement compared to the "expand from center" method.</li>
</ul>
</li>
<li><strong>Dynamic Programming (O(N^2) Time, O(N^2) Space):</strong><ul>
<li>Create a 2D boolean array <code>dp[i][j]</code> where <code>dp[i][j]</code> is true if <code>s[i...j]</code> is a palindrome.</li>
<li>Base cases: <code>dp[i][i] = True</code> and <code>dp[i][i+1] = (s[i] == s[i+1])</code>.</li>
<li>Recurrence: <code>dp[i][j] = (s[i] == s[j]) and dp[i+1][j-1]</code>.</li>
<li><strong>Trade-off:</strong> Easier to reason about for some, but uses O(N^2) space, which can be an issue for very long strings.</li>
</ul>
</li>
<li><strong>Readability &amp; Comments:</strong><ul>
<li>The code is already quite readable. A small comment explaining the <code>start</code> calculation's versatility for both odd/even lengths could be beneficial.</li>
</ul>
</li>
<li><strong>Minor Optimization (Early Exit):</strong><ul>
<li>If <code>max_len</code> ever becomes equal to <code>len(s)</code>, or if <code>max_len</code> is already greater than or equal to the remaining possible length of a palindrome starting from <code>i</code>, the loop could potentially be terminated early. This rarely offers significant practical gains unless early exits are very common.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> There are no inherent security vulnerabilities in this code. It performs basic string manipulations and does not interact with external systems or user-controlled data in a way that would introduce common web vulnerabilities (e.g., injection, XSS).</li>
<li><strong>Performance:</strong><ul>
<li>For typical interview constraints (string length up to 1000-2000), the O(N^2) complexity is generally acceptable.</li>
<li>For very large strings (e.g., N = 10^5 or more), O(N^2) would be too slow (10^10 operations) and Manacher's O(N) algorithm would be required.</li>
<li>Python string slicing (<code>s[start : start + max_len]</code>) creates a new string object. While this is an O(K) operation where K is the length of the slice, it only happens once at the end, so it's not a performance bottleneck in the overall algorithm.</li>
</ul>
</li>
</ul>


### Code:
```python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        if not s:
            return ""

        start = 0
        max_len = 0

        def expand_from_center(s, left, right):
            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1
            return right - left - 1 # Length of the palindrome found

        for i in range(len(s)):
            # Odd length palindrome, centered at i
            len1 = expand_from_center(s, i, i)
            # Even length palindrome, centered between i and i+1
            len2 = expand_from_center(s, i, i + 1)

            current_len = max(len1, len2)

            if current_len > max_len:
                max_len = current_len
                # Calculate the starting index of the longest palindrome
                # For a palindrome of length `current_len` centered at `i` (or `i, i+1`),
                # the start index is `i - (current_len - 1) // 2`.
                start = i - (current_len - 1) // 2
        
        return s[start : start + max_len]
```
