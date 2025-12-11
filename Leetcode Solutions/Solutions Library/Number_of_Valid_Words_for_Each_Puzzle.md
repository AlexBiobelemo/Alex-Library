## Number of Valid Words for Each Puzzle
**Language:** python
**Tags:** bit manipulation,hash map,subset generation,string processing

### Description:
<p>This code solves the "Number of Valid Words for Each Puzzle" problem, a classic challenge often found in coding interviews and platforms like LeetCode. It leverages bit manipulation to efficiently determine word validity.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to count, for each given <code>puzzle</code>, how many <code>words</code> from a provided list are "valid". A word is considered valid if:</p>
<ol>
<li>All characters in the <code>word</code> are also present in the <code>puzzle</code>.</li>
<li>The first character of the <code>puzzle</code> must be present in the <code>word</code>.</li>
</ol>
<p>The solution uses bitmasks to represent sets of characters, significantly speeding up character set comparisons and subset checks.</p>
<hr>
<h3>2. How It Works</h3>
<p>The solution proceeds in two main phases:</p>
<ul>
<li><p><strong>Step 1: Preprocess Words (Character Set Representation)</strong></p>
<ul>
<li>It iterates through each <code>word</code> in the <code>words</code> list.</li>
<li>For each <code>word</code>, it generates a unique <em>bitmask</em>. Each bit in the mask corresponds to a letter of the alphabet (e.g., bit 0 for 'a', bit 1 for 'b', etc.). If a character is present in the word, its corresponding bit is set to 1.</li>
<li>These bitmasks, along with their frequencies (how many words map to the same unique character set), are stored in a <code>defaultdict</code>. This is crucial because multiple words might share the same set of unique characters (e.g., "cat" and "act" both map to the same mask <code>0b1000000000000000000101</code>).</li>
</ul>
</li>
<li><p><strong>Step 2: Process Puzzles (Validation and Counting)</strong></p>
<ul>
<li>It iterates through each <code>puzzle</code> in the <code>puzzles</code> list.</li>
<li>For each <code>puzzle</code>:<ul>
<li>It calculates its own bitmask and a separate bitmask for its <em>first character</em>.</li>
<li>It then efficiently iterates through <em>all possible subsets</em> of the <code>puzzle</code>'s character bitmask. This is achieved using the bit manipulation trick <code>submask = (submask - 1) &amp; puzzle_mask</code>.</li>
<li>For each <code>submask</code> (representing a potential valid word's character set):<ul>
<li>It checks if this <code>submask</code> contains the first character of the <code>puzzle</code> (condition 2).</li>
<li>If it does, it looks up this <code>submask</code> in the preprocessed <code>word_masks_count</code> dictionary. If found, it adds the count of words sharing this character set to the current puzzle's total. (Condition 1 is implicitly handled because <code>submask</code> is always a subset of <code>puzzle_mask</code>).</li>
</ul>
</li>
</ul>
</li>
<li>Finally, the total count for the current <code>puzzle</code> is appended to the <code>answer</code> list.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Bitmasks for Character Sets</strong>:<ul>
<li><strong>Decision</strong>: Representing unique character sets as bitmasks (integers where each bit corresponds to a letter 'a'-'z').</li>
<li><strong>Benefit</strong>: Allows for extremely fast O(1) operations for character set union (<code>|</code>), intersection (<code>&amp;</code>), and subset checks. Given only 26 lowercase English letters, a standard 32-bit integer is sufficient.</li>
</ul>
</li>
<li><strong><code>collections.defaultdict(int)</code></strong>:<ul>
<li><strong>Decision</strong>: Using a hash map to store counts of identical word character masks.</li>
<li><strong>Benefit</strong>: Avoids redundant calculations for words that have the same unique character set. It also simplifies the logic by providing a default value of <code>0</code> for non-existent masks, making lookups cleaner.</li>
</ul>
</li>
<li><strong>Efficient Subset Iteration</strong>:<ul>
<li><strong>Decision</strong>: Using the <code>submask = (submask - 1) &amp; puzzle_mask</code> idiom to iterate through all subsets of the <code>puzzle_mask</code>.</li>
<li><strong>Benefit</strong>: This is a highly optimized bit manipulation technique that generates all subsets of a given mask <code>k</code> without needing to generate <code>2^26</code> total masks. Since puzzle length is small (max 7), the number of subsets is at most <code>2^7 = 128</code>, making this part of the algorithm very fast.</li>
</ul>
</li>
<li><strong>Preprocessing</strong>:<ul>
<li><strong>Decision</strong>: Separating the processing of <code>words</code> from <code>puzzles</code>.</li>
<li><strong>Benefit</strong>: <code>words</code> are processed once, creating a reusable lookup table (<code>word_masks_count</code>). This avoids re-scanning the entire <code>words</code> list for each <code>puzzle</code>, which would be much slower.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the number of words, <code>M</code> be the number of puzzles.
Let <code>L_w</code> be the maximum length of a word, <code>L_p</code> be the maximum length of a puzzle.
Let <code>A</code> be the size of the alphabet (26 for 'a'-'z').</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><strong>Preprocessing words</strong>: For each of <code>N</code> words, we iterate through its <code>L_w</code> characters to create a mask. Dictionary insertion is O(1) on average.<ul>
<li>Total: <code>O(N * L_w)</code>.</li>
</ul>
</li>
<li><strong>Processing puzzles</strong>: For each of <code>M</code> puzzles:<ul>
<li>Creating puzzle mask: <code>O(L_p)</code>.</li>
<li>Calculating <code>first_letter_mask</code>: <code>O(1)</code>.</li>
<li>Subset iteration: A puzzle has at most <code>L_p</code> unique characters. The number of subsets of a set of <code>k</code> elements is <code>2^k</code>. So, <code>O(2^L_p)</code> iterations in the worst case.</li>
<li>Inside the loop, dictionary lookup is O(1) on average.</li>
<li>Total for puzzles: <code>O(M * (L_p + 2^L_p))</code>.</li>
</ul>
</li>
<li><strong>Overall Time Complexity</strong>: <code>O(N * L_w + M * (L_p + 2^L_p))</code>.</li>
<li>Given typical constraints (<code>N, M</code> up to 10^5, <code>L_w</code> up to 10, <code>L_p</code> up to 7), <code>2^L_p</code> is at most <code>2^7 = 128</code>. This makes the <code>2^L_p</code> factor very small and efficient.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>word_masks_count</code>: In the worst case, all <code>N</code> words could have unique character sets, so it stores up to <code>N</code> entries. Each entry is an integer key and an integer value.<ul>
<li>Space: <code>O(N)</code>.</li>
</ul>
</li>
<li><code>answer</code> list: Stores one integer per puzzle.<ul>
<li>Space: <code>O(M)</code>.</li>
</ul>
</li>
<li><strong>Overall Space Complexity</strong>: <code>O(N + M)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>words</code> or <code>puzzles</code> lists</strong>: The code handles this gracefully. Loops won't execute, and the <code>answer</code> list will be empty or contain zeros.</li>
<li><strong>Words/Puzzles with repeated characters</strong>: Bitmasks inherently handle this by only setting a bit once for each unique character, which is correct for character set comparisons.</li>
<li><strong>Words/Puzzles with a single character</strong>: The bitmasking logic works correctly. The subset iteration and first letter check will still apply.</li>
<li><strong>All characters in <code>puzzle</code> are the same</strong>: The <code>puzzle_mask</code> will have only one bit set. The <code>submask</code> iteration will still correctly go from <code>puzzle_mask</code> down to <code>0</code>.</li>
<li><strong>Characters outside 'a'-'z'</strong>: The code assumes lowercase English alphabet because of <code>char_code - ord('a')</code>. If inputs could contain other characters, this mapping would need adjustment or validation. Standard competitive programming problems usually guarantee this constraint.</li>
<li><strong>Correctness of subset iteration</strong>: The <code>submask = (submask - 1) &amp; puzzle_mask</code> algorithm correctly generates all subsets that are contained within <code>puzzle_mask</code>, ensuring that condition 1 (all letters in word are in puzzle) is satisfied for any <code>submask</code> being considered. The explicit check <code>(submask &amp; first_letter_mask) == first_letter_mask</code> ensures condition 2.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Code Readability</strong>:<ul>
<li>Extract the mask generation logic into a small helper function, e.g., <code>_get_mask(s)</code>. This would make the main loop more concise.</li>
<li>Store <code>ord('a')</code> in a constant <code>OFFSET = ord('a')</code> to avoid repeated calls and improve clarity of the bit shift.</li>
</ul>
</li>
<li><strong>Minor Optimizations (unlikely to be significant in Python)</strong>:<ul>
<li>Pre-calculating <code>ord(puzzle[0]) - ord('a')</code> once per puzzle loop. (Currently done when creating <code>first_letter_mask</code>).</li>
</ul>
</li>
<li><strong>Alternative Data Structures/Algorithms</strong>:<ul>
<li>For very different constraints (e.g., extremely long puzzles or very few words but many puzzles), other approaches like a Trie (or Aho-Corasick) might be considered, but for <em>this specific problem's constraints</em> (especially <code>L_p &lt;= 7</code>), the bitmasking and subset iteration is generally optimal. Tries are usually for prefix/substring matching, not character set inclusion.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The solution is highly performant due to the efficient use of bitmasks and a carefully chosen algorithm for subset generation. The preprocessing step amortizes the cost of analyzing words across all puzzles. The critical part, <code>2^L_p</code>, is bounded by a very small constant (128), making it extremely fast.</li>
<li><strong>Security</strong>: No direct security vulnerabilities are apparent in this pure algorithmic code. It processes string character sets and counts, without external interactions or sensitive data handling. Input validation for character ranges might be added in a production scenario if input guarantees are not strict.</li>
</ul>


### Code:
```python
import collections

class Solution(object):
    def findNumOfValidWords(self, words, puzzles):
        """
        :type words: List[str]
        :type puzzles: List[str]
        :rtype: List[int]
        """
        # Step 1: Preprocess words into bitmasks and count their occurrences
        # A bitmask represents the set of unique characters in a word.
        # For example, 'a' -> 1 (1<<0), 'b' -> 2 (1<<1), 'c' -> 4 (1<<2), etc.
        # 'cat' -> (1<<2) | (1<<0) | (1<<19)
        word_masks_count = collections.defaultdict(int)
        for word in words:
            mask = 0
            for char_code in map(ord, word):
                mask |= (1 << (char_code - ord('a')))
            word_masks_count[mask] += 1

        # Step 2: Process each puzzle
        answer = []
        for puzzle in puzzles:
            puzzle_mask = 0
            for char_code in map(ord, puzzle):
                puzzle_mask |= (1 << (char_code - ord('a')))

            # The first letter of the puzzle must be present in a valid word.
            # We create a bitmask for this first letter.
            first_letter_mask = (1 << (ord(puzzle[0]) - ord('a')))

            current_puzzle_valid_words_count = 0

            # Iterate through all possible subsets of the puzzle_mask.
            # This is efficient because puzzle length is at most 7, so there are at most 2^7 = 128 subsets.
            # The loop `submask = (submask - 1) & puzzle_mask` efficiently generates all subsets.
            submask = puzzle_mask
            while submask > 0:
                # Condition 1: Check if the current submask (representing a word's character set)
                # contains the first letter of the puzzle.
                # This is true if (submask BITWISE_AND first_letter_mask) equals first_letter_mask.
                if (submask & first_letter_mask) == first_letter_mask:
                    # Condition 2: All letters in the word must be in the puzzle.
                    # This is implicitly handled because `submask` is always a subset of `puzzle_mask`.

                    # If this submask corresponds to any preprocessed word, add its count.
                    current_puzzle_valid_words_count += word_masks_count[submask]
                
                # Move to the next smaller subset of puzzle_mask
                submask = (submask - 1) & puzzle_mask
            
            answer.append(current_puzzle_valid_words_count)

        return answer
```
