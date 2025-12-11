## Substring with Concatenation of All Words
**Language:** python
**Tags:** python,sliding window,string search,hash map

### Description:
<p>This code implements a solution to the "Substring with Concatenation of All Words" problem. It uses a sliding window approach with <code>collections.Counter</code> to efficiently find all starting indices in a string <code>s</code> where a concatenation of all words from a given list <code>words</code> exists. The order of words in the concatenation does not matter, but each word in <code>words</code> must be used exactly once.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of <code>findSubstring</code> is to locate all starting positions within a larger string <code>s</code> where a contiguous substring is formed by concatenating all words from the input list <code>words</code>, exactly once and without any intervening characters.</p>
<p>For example, if <code>s = "barfoothefoobarman"</code> and <code>words = ["foo", "bar"]</code>, the function should return <code>[0, 9]</code> because "barfoo" starts at index 0 and "foobar" starts at index 9.</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a sophisticated sliding window technique, adapting it to handle the fixed-length nature of the words and the possibility of different starting alignments.</p>
<ol>
<li><p><strong>Initialization &amp; Edge Cases:</strong></p>
<ul>
<li>Handles empty <code>s</code> or <code>words</code> lists immediately.</li>
<li>Calculates <code>num_words</code>, <code>word_len</code>, <code>total_len</code> (total length of all concatenated words), and <code>s_len</code>.</li>
<li>Returns early if <code>s</code> is shorter than <code>total_len</code>.</li>
<li><code>word_counts</code>: A <code>collections.Counter</code> stores the required frequency of each word from the input <code>words</code> list.</li>
<li><code>result</code>: An empty list to store the starting indices of found substrings.</li>
</ul>
</li>
<li><p><strong>Handling Multiple Alignments (<code>for i in range(word_len)</code>):</strong></p>
<ul>
<li>The core challenge is that the concatenated string might not start at index <code>0</code>. It could start at <code>s[1]</code>, <code>s[2]</code>, up to <code>s[word_len-1]</code>.</li>
<li>This outer loop effectively creates <code>word_len</code> independent "strands" or "sub-problems". Each <code>i</code> represents a starting offset for the <em>first</em> word of a potential concatenation. For example, if <code>word_len</code> is 3, <code>i=0</code> checks <code>s[0], s[3], s[6], ...</code>, <code>i=1</code> checks <code>s[1], s[4], s[7], ...</code>, and <code>i=2</code> checks <code>s[2], s[5], s[8], ...</code>.</li>
</ul>
</li>
<li><p><strong>Sliding Window (<code>for j in range(i, s_len - word_len + 1, word_len)</code>):</strong></p>
<ul>
<li>Inside each <code>i</code> loop, a standard sliding window is maintained.</li>
<li><code>left</code>: Marks the starting index of the current window.</li>
<li><code>count</code>: Tracks how many words from <code>words</code> are currently within the window.</li>
<li><code>window_word_counts</code>: A <code>collections.Counter</code> to track the frequency of words in the <em>current window</em>.</li>
<li>The inner loop iterates <code>j</code> from <code>i</code> up to <code>s_len - word_len</code>, incrementing by <code>word_len</code> at each step. <code>j</code> represents the start of the <em>current word</em> being processed.</li>
</ul>
</li>
<li><p><strong>Processing Each Word in the Window:</strong></p>
<ul>
<li><code>word = s[j : j + word_len]</code>: Extracts a word-sized substring from <code>s</code>.</li>
<li><strong>If <code>word</code> is valid (in <code>word_counts</code>):</strong><ul>
<li>Increment <code>window_word_counts[word]</code> and <code>count</code>.</li>
<li><strong>Shrink window from left (if needed):</strong> A <code>while</code> loop checks if the count of <code>word</code> in the current <code>window_word_counts</code> exceeds its required count in <code>word_counts</code>. If so, it means we have too many instances of this word. To correct this, words are removed from the <code>left</code> of the window until the count is valid again. This involves decrementing <code>window_word_counts</code>, <code>count</code>, and advancing <code>left</code>.</li>
<li><strong>Found a match:</strong> If <code>count</code> now equals <code>num_words</code>, it means we have found a valid concatenation. The current <code>left</code> index is added to <code>result</code>.</li>
<li><strong>Slide window forward:</strong> After finding a match (or just processing a valid word), the window must slide. The leftmost word <code>s[left : left + word_len]</code> is effectively removed from the window (<code>window_word_counts</code> and <code>count</code> are decremented), and <code>left</code> is advanced by <code>word_len</code>. This prepares the window for the next word that <code>j</code> will extract.</li>
</ul>
</li>
<li><strong>If <code>word</code> is invalid (not in <code>word_counts</code>):</strong><ul>
<li>The current window is invalid because it contains a word not from <code>words</code>.</li>
<li><code>window_word_counts</code> is cleared.</li>
<li><code>count</code> is reset to 0.</li>
<li><code>left</code> is moved past the invalid word (<code>j + word_len</code>), effectively starting a new window from that point.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Return <code>result</code>:</strong> After checking all possible starting offsets and sliding windows, the list of all found indices is returned.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sliding Window:</strong> This is the most efficient approach for problems involving contiguous substrings. By moving the window by <code>word_len</code> at a time, it avoids re-evaluating overlapping characters unnecessarily.</li>
<li><strong><code>collections.Counter</code>:</strong><ul>
<li>Used for <code>word_counts</code> to quickly establish the required frequency of each word in <code>words</code>.</li>
<li>Used for <code>window_word_counts</code> to track current word frequencies within the sliding window, enabling efficient comparison with <code>word_counts</code> and quick detection of over-counted words.</li>
<li>This choice is crucial for handling duplicate words in the <code>words</code> list (e.g., <code>["foo", "foo"]</code>).</li>
</ul>
</li>
<li><strong>Multiple Starting Offsets (<code>for i in range(word_len)</code>):</strong> This is the most critical design decision unique to this problem. Since words have a fixed length, a concatenation of words <em>must</em> start at an index <code>k</code> where <code>k % word_len</code> is constant. Iterating <code>i</code> from <code>0</code> to <code>word_len-1</code> ensures that all such possible alignments are checked independently, effectively transforming one large problem into <code>word_len</code> smaller, independent sliding window problems.</li>
<li><strong>Fixed-Length Window Segments:</strong> The window always processes <code>word_len</code>-sized chunks, which aligns perfectly with the problem constraints.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of <code>s</code>, <code>M</code> be the number of words in <code>words</code>, and <code>L</code> be the length of each word (<code>word_len</code>).</p>
<ul>
<li><p><strong>Time Complexity:</strong> O(<code>L</code> * <code>N</code>)</p>
<ul>
<li>Building <code>word_counts</code>: O(<code>M</code> * <code>L</code>) as it iterates through <code>M</code> words, each taking O(<code>L</code>) to hash and store.</li>
<li>Outer loop (<code>for i</code>): Runs <code>L</code> times.</li>
<li>Inner loop (<code>for j</code>): For each <code>i</code>, <code>j</code> iterates roughly <code>N/L</code> times.</li>
<li>Inside the inner loop:<ul>
<li>String slicing (<code>s[j : j + L]</code>): O(<code>L</code>).</li>
<li><code>collections.Counter</code> operations (lookup, increment, decrement): On average, O(<code>L</code>) because dictionary keys are strings, and hashing a string of length <code>L</code> takes O(<code>L</code>) time.</li>
<li>The <code>while</code> loop (shrinking the window): In total, for a given <code>i</code>, the <code>left</code> pointer will traverse the string <code>s</code> at most once from <code>i</code> to <code>N</code>. Each word removal involves string slicing and <code>Counter</code> operations, taking O(<code>L</code>).</li>
</ul>
</li>
<li>Combining these: <code>L</code> (outer loop) * (<code>N/L</code> * <code>L</code> (inner loop's slicing/hashing) + <code>N</code> (amortized cost of <code>left</code> pointer movement and <code>while</code> loop)) = <code>L * (N + N)</code> = O(<code>L * N</code>).</li>
</ul>
</li>
<li><p><strong>Space Complexity:</strong> O(<code>M</code> * <code>L</code>)</p>
<ul>
<li><code>word_counts</code>: Stores up to <code>M</code> unique words, each of length <code>L</code>. So, O(<code>M</code> * <code>L</code>).</li>
<li><code>window_word_counts</code>: Stores at most <code>M</code> words, each of length <code>L</code>. So, O(<code>M</code> * <code>L</code>).</li>
<li><code>result</code>: In the worst case, <code>s</code> could consist of many valid concatenations, potentially O(<code>N</code>) indices.</li>
<li>Overall, the space is dominated by the <code>Counter</code> objects for storing the words.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>s</code> or <code>words</code>:</strong> Handled at the beginning, returning <code>[]</code>.</li>
<li><strong><code>s_len &lt; total_len</code>:</strong> Handled, returning <code>[]</code>. This prevents out-of-bounds access and unnecessary computation.</li>
<li><strong><code>words</code> contains duplicate words:</strong> Correctly handled by <code>collections.Counter</code>, which stores frequencies (e.g., <code>words = ["foo", "foo"]</code> means "foo" must appear twice).</li>
<li><strong>Words not found in <code>words</code>:</strong> The <code>else</code> block correctly resets the window, moving <code>left</code> past the invalid word and clearing <code>window_word_counts</code>. This ensures that only valid concatenations are considered.</li>
<li><strong>All words are identical:</strong> E.g., <code>s = "foofoofoo", words = ["foo", "foo"]</code>. The <code>Counter</code> logic correctly requires two "foo"s, and the sliding window identifies <code>s[0:6]</code> as a match.</li>
<li><strong>Non-existent <code>words[0]</code> (e.g., <code>words = []</code>):</strong> Handled by initial check <code>if not words</code>.</li>
<li><strong>Word length is zero (<code>len(words[0]) == 0</code>):</strong> This scenario is not explicitly handled and would lead to issues (e.g., <code>word_len</code> would be 0, <code>range(0)</code> for outer loop means it wouldn't run, or <code>word_len</code> used in slicing <code>s[j : j + word_len]</code> would cause an empty slice that can match any empty string, which is probably not intended). Problem statements usually guarantee non-empty words.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (Minor):</strong><ul>
<li>The <code>left_word = s[left : left + word_len]</code> slicing is repeated when a match is found and the window slides (<code>result.append(left)</code> then <code>left_word = ...</code>). The <code>left_word</code> is already known from the previous <code>left</code> calculation when it was added to the window. While minor, one could cache <code>word</code>s for the window, but this adds complexity that might not be worth it in Python due to string/hashing optimizations.</li>
</ul>
</li>
<li><strong>Clarity:</strong> The code is already quite clear, with good variable names and comments explaining the purpose of the loops.</li>
<li><strong>Pre-computing Word Hashes/IDs:</strong> If <code>words</code> contains very long words or the same words are repeated millions of times, one could map each unique word to an integer ID and store counts of these IDs. This would make <code>Counter</code> operations O(1) instead of O(<code>L</code>) for string hashing, but adds the overhead of mapping. For typical contest constraints, the current approach is usually optimal enough.</li>
<li><strong>Alternative Data Structures:</strong> While <code>Counter</code> is excellent here, for problems with variable word lengths or prefix searches, a Trie could be a consideration, but it would significantly complicate this specific fixed-length problem.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Denial of Service (DoS):</strong> No obvious DoS vectors related to malicious input strings leading to infinite loops or excessive memory usage beyond the O(<code>N*L</code>) time and O(<code>M*L</code>) space. The string slicing and <code>Counter</code> operations are well-defined.</li>
<li><strong>Memory Usage:</strong> The memory scales with <code>M * L</code>. If <code>words</code> contains an extremely large number of very long unique words, this could be substantial. However, <code>M * L</code> is typically constrained in such problems.</li>
<li><strong>String Hashing Performance:</strong> Python's string hashing is efficient, but if <code>L</code> (word length) is extremely large, the O(<code>L</code>) factor for hashing each word will be significant. This is an inherent cost for processing words of that length.</li>
</ul>


### Code:
```python
import collections

class Solution(object):
    def findSubstring(self, s, words):
        """
        :type s: str
        :type words: List[str]
        :rtype: List[int]
        """
        if not s or not words:
            return []

        num_words = len(words)
        word_len = len(words[0])
        total_len = num_words * word_len
        s_len = len(s)

        if s_len < total_len:
            return []

        word_counts = collections.Counter(words)
        result = []

        # Iterate through all possible starting offsets for the first word
        # This handles cases where the concatenated string might start at s[0], s[1], ..., s[word_len-1]
        for i in range(word_len):
            left = i
            count = 0 # Number of words currently in the window
            window_word_counts = collections.Counter()

            # Slide the window by `word_len` at a time
            # `j` is the starting index of the current word being considered
            for j in range(i, s_len - word_len + 1, word_len):
                word = s[j : j + word_len]

                if word in word_counts:
                    window_word_counts[word] += 1
                    count += 1

                    # If this word count exceeds the required count, shrink the window from the left
                    while window_word_counts[word] > word_counts[word]:
                        left_word = s[left : left + word_len]
                        window_word_counts[left_word] -= 1
                        count -= 1
                        left += word_len

                    # If we have exactly `num_words` in our window, we found a match
                    if count == num_words:
                        result.append(left)

                        # Move the window one word to the right (remove the leftmost word)
                        left_word = s[left : left + word_len]
                        window_word_counts[left_word] -= 1
                        count -= 1
                        left += word_len
                else:
                    # If the word is not in `words`, reset the window
                    window_word_counts.clear()
                    count = 0
                    left = j + word_len # Start new window after this invalid word

        return result
```
