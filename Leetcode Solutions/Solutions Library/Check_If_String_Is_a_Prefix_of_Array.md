## Check If String Is a Prefix of Array
**Language:** python
**Tags:** python,string,prefix,concatenation,list

### Description:
<p>This code defines a method <code>isPrefixString</code> that checks if a target string <code>s</code> can be formed by concatenating a prefix of a given list of strings <code>words</code>.</p>
<h2>1. Overview &amp; Intent</h2>
<p>The primary goal of this code is to determine if the string <code>s</code> is exactly equal to the concatenation of the first <code>k</code> strings from the <code>words</code> list, for some <code>k</code> (where <code>0 &lt;= k &lt;= len(words)</code>).</p>
<h2>2. How It Works</h2>
<ol>
<li><strong>Initialization</strong>: An empty string <code>current_concat</code> is initialized to store the cumulative concatenation of words from the <code>words</code> list.</li>
<li><strong>Iteration</strong>: The code iterates through each <code>word</code> in the <code>words</code> list.</li>
<li><strong>Concatenation</strong>: In each iteration, the current <code>word</code> is appended to <code>current_concat</code>.</li>
<li><strong>Match Check</strong>: After concatenation, <code>current_concat</code> is compared directly with <code>s</code>. If they are equal, it means <code>s</code> has been successfully formed by a prefix of <code>words</code>, and the function returns <code>True</code>.</li>
<li><strong>Length Optimization</strong>: An important optimization is included: if <code>current_concat</code> becomes longer than <code>s</code>, it's impossible for <code>current_concat</code> to ever equal <code>s</code> (since it only grows). In this case, the function immediately returns <code>False</code>.</li>
<li><strong>No Match</strong>: If the loop completes without <code>current_concat</code> ever matching <code>s</code>, the function returns <code>False</code>.</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Data Structures</strong>:<ul>
<li>Uses standard Python strings (<code>s</code>, <code>current_concat</code>) and a list of strings (<code>words</code>). These are appropriate for the problem domain.</li>
</ul>
</li>
<li><strong>Algorithm</strong>:<ul>
<li>Employs an iterative, greedy approach: build the prefix string incrementally and check for a match at each step.</li>
</ul>
</li>
<li><strong>Trade-offs</strong>:<ul>
<li><strong>Simplicity vs. Performance</strong>: The iterative concatenation <code>current_concat += word</code> is straightforward to understand. However, string concatenation in Python typically creates new string objects, which can incur performance overhead, especially for many concatenations or very long strings.</li>
<li><strong>Early Exit</strong>: The <code>len(current_concat) &gt; len(s)</code> check is a crucial optimization to avoid unnecessary work once <code>s</code> can no longer be matched.</li>
</ul>
</li>
</ul>
<h2>4. Complexity</h2>
<p>Let <code>N</code> be <code>len(s)</code>.
Let <code>M</code> be <code>len(words)</code>.
Let <code>L_i</code> be <code>len(words[i])</code>.</p>
<ul>
<li><p><strong>Time Complexity</strong>: <code>O(N * K)</code></p>
<ul>
<li>Where <code>K</code> is the number of words processed before <code>current_concat</code> matches or exceeds <code>s</code> (so <code>K &lt;= M</code>).</li>
<li>Each string concatenation <code>current_concat += word</code> involves creating a new string. If <code>current_concat</code> has length <code>P</code> and <code>word</code> has length <code>Q</code>, this operation takes <code>O(P + Q)</code> time.</li>
<li>In the worst case, <code>current_concat</code> grows up to <code>N</code> characters. The sum of lengths of <code>current_concat</code> across all <code>K</code> iterations can be estimated. The <code>k</code>-th concatenation takes <code>O(sum(L_j for j &lt; k))</code> time. Summing these up, the total cost of concatenations can be up to <code>O(K * N)</code>.</li>
<li>Each comparison <code>current_concat == s</code> takes <code>O(len(current_concat))</code> time, which is at most <code>O(N)</code>. This occurs <code>K</code> times, contributing <code>O(K * N)</code>.</li>
<li>Therefore, the dominant factor results in a total time complexity of <code>O(N * K)</code>, which in the worst case (when all words are processed) is <code>O(N * M)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>: <code>O(N)</code></p>
<ul>
<li>The <code>current_concat</code> string can grow up to the length of <code>s</code>. This requires <code>O(N)</code> additional space.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong><code>s</code> is an empty string (<code>""</code>)</strong>:<ul>
<li><code>current_concat</code> starts as <code>""</code>.</li>
<li>The condition <code>current_concat == s</code> (<code>"" == ""</code>) will be <code>True</code> immediately before the loop, returning <code>True</code>. This is correct as an empty string can be formed by concatenating zero words.</li>
</ul>
</li>
<li><strong><code>words</code> is an empty list (<code>[]</code>)</strong>:<ul>
<li>The <code>for</code> loop will not execute.</li>
<li>The function will directly return <code>False</code>. This is correct (unless <code>s</code> is empty, covered above).</li>
</ul>
</li>
<li><strong><code>s</code> is shorter than the first word</strong>:<ul>
<li>Example: <code>s = "a"</code>, <code>words = ["bc", "d"]</code>.</li>
<li><code>current_concat</code> becomes <code>"bc"</code>.</li>
<li><code>len("bc")</code> (2) is <code>&gt;</code> <code>len("a")</code> (1). The optimization <code>len(current_concat) &gt; len(s)</code> triggers, returning <code>False</code>. This is correct.</li>
</ul>
</li>
<li><strong><code>s</code> is longer than all words combined</strong>:<ul>
<li>Example: <code>s = "abcde"</code>, <code>words = ["ab", "c"]</code>.</li>
<li><code>current_concat</code> will become <code>"abc"</code>.</li>
<li>Loop finishes. <code>current_concat</code> is never equal to <code>s</code>, and its length (<code>3</code>) never exceeds <code>len(s)</code> (<code>5</code>).</li>
<li>The function correctly returns <code>False</code>.</li>
</ul>
</li>
<li><strong>Exact Match</strong>: The code correctly identifies when <code>s</code> is formed exactly by a prefix of <code>words</code>.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<p>The primary area for improvement is the efficiency of string handling.</p>
<ol>
<li><p><strong>Optimized Approach using <code>startswith</code></strong>:
A more efficient approach would be to avoid repeated string concatenations and instead use string prefix matching.</p>
<pre><code class="language-python">class Solution(object):
    def isPrefixString(self, s, words):
        """
        :type s: str
        :type words: List[str]
        :rtype: bool
        """
        current_idx = 0  # Tracks the current position in 's'
        for word in words:
            word_len = len(word)
            
            # Check if the current word matches the segment of 's'
            # starting from current_idx
            if not s.startswith(word, current_idx):
                return False
            
            current_idx += word_len
            
            # If we've exactly matched the entire string 's'
            if current_idx == len(s):
                return True
            
            # If we've overshot the length of 's'
            if current_idx &gt; len(s):
                return False # This case is implicitly handled by startswith failing earlier if word is too long
                              # but explicit check can be useful.
                              # Example: s="abc", word="abcd" -&gt; startswith fails.
                              # Example: s="abc", word="a", next word "bcd" -&gt; current_idx becomes 4, &gt; len(s).
                              # So this check is still relevant.
                              
        # After iterating through all words, check if 's' was fully matched
        return current_idx == len(s)
</code></pre>
<ul>
<li><strong>Time Complexity (Optimized)</strong>: <code>O(N)</code><ul>
<li>Each character of <code>s</code> is visited and compared by <code>startswith</code> at most once in total across all iterations.</li>
<li><code>len(word)</code> is <code>O(1)</code> (for typical Python string lengths, or <code>O(length_of_word)</code> if considering string obj itself).</li>
<li>The loop runs <code>K</code> times (number of words processed).</li>
<li>Total operations are proportional to the length of <code>s</code>.</li>
</ul>
</li>
<li><strong>Space Complexity (Optimized)</strong>: <code>O(1)</code> (excluding input strings).</li>
</ul>
</li>
</ol>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Performance Bottleneck</strong>: The original implementation's <code>O(N * M)</code> time complexity can be a significant performance bottleneck for large inputs. If <code>s</code> is very long (<code>N</code> = 10^5) and <code>words</code> contains many small strings (<code>M</code> = 10^3), <code>N * M</code> becomes <code>10^8</code>, which is generally too slow for typical competitive programming time limits (usually around <code>10^7</code> operations per second). The optimized <code>O(N)</code> approach is highly recommended for such constraints.</li>
<li><strong>No Security Concerns</strong>: The code does not handle external input or perform operations that could introduce security vulnerabilities like injection or arbitrary code execution. It solely processes strings in memory.</li>
</ul>


### Code:
```python
class Solution(object):
    def isPrefixString(self, s, words):
        """
        :type s: str
        :type words: List[str]
        :rtype: bool
        """
        current_concat = ""
        for word in words:
            current_concat += word
            if current_concat == s:
                return True
            # Optimization: if current_concat becomes longer than s,
            # it can no longer be equal to s.
            if len(current_concat) > len(s):
                return False
        return False
```
