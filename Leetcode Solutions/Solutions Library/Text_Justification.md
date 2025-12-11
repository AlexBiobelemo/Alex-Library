## Text Justification
**Language:** python
**Tags:** text justification,greedy algorithm,string manipulation,text formatting

### Description:
<p>This solution implements a text justification algorithm, aligning words within a given <code>maxWidth</code>. It follows a standard two-phase approach for this problem.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>fullJustify</code> method takes a list of <code>words</code> and an integer <code>maxWidth</code>. Its intent is to format these words into lines such that each line is exactly <code>maxWidth</code> characters long, with specific rules for space distribution:</p>
<ul>
<li><strong>Full Justification:</strong> For most lines, spaces are distributed as evenly as possible between words to fill the width. If spaces cannot be perfectly even, extra spaces are added to the gaps from left to right.</li>
<li><strong>Left Justification:</strong> The last line, and any line containing only a single word, is left-justified, with extra spaces padded at the end.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm processes words line by line in a main loop, managing two distinct phases for each line:</p>
<ul>
<li><p><strong>Phase 1: Greedy Line Packing (<code>while j &lt; len(words) ...</code>)</strong></p>
<ul>
<li>It starts with the first word of a potential line (<code>words[i]</code>) and iteratively adds subsequent words (<code>words[j]</code>).</li>
<li>Words are added as long as the next word, plus at least one space to separate it, fits within <code>maxWidth</code>.</li>
<li>This "greedy" approach ensures each line contains the maximum possible number of words.</li>
<li><code>current_line_words</code> is then extracted using array slicing (<code>words[i:j]</code>).</li>
<li>The total length of just the words (<code>total_word_length</code>) and the <code>total_spaces</code> required (<code>maxWidth - total_word_length</code>) are calculated.</li>
</ul>
</li>
<li><p><strong>Phase 2: Justification Phase (<code>if is_last_line or num_words == 1: ... else: ...</code>)</strong></p>
<ul>
<li><strong>Special Cases (Left Justified):</strong> If <code>j</code> has reached the end of the <code>words</code> list (it's the last line) or if <code>current_line_words</code> contains only one word (<code>num_words == 1</code>), the line is left-justified:<ul>
<li>Words are joined with a single space.</li>
<li>The remaining width is filled with spaces appended to the end of the line.</li>
</ul>
</li>
<li><strong>General Case (Fully Justified):</strong> For all other lines, spaces are distributed evenly:<ul>
<li>The number of gaps between words (<code>num_gaps</code>) is <code>num_words - 1</code>.</li>
<li><code>base_spaces</code> (<code>total_spaces // num_gaps</code>) are allocated to each gap.</li>
<li><code>extra_spaces_slots</code> (<code>total_spaces % num_gaps</code>) represent the number of gaps (from left to right) that will receive one additional space.</li>
<li>The line is constructed by iterating through <code>current_line_words</code>, adding the word, and then adding <code>base_spaces</code> plus an extra space if it's one of the <code>extra_spaces_slots</code> gaps.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Iteration:</strong> After a line is processed and added to <code>result</code>, the <code>i</code> pointer is advanced to <code>j</code> to start processing the next line.</p>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Greedy Line Breaking:</strong> The algorithm decides which words go on a line by taking as many as possible. This is a common strategy in text justification, leading to generally aesthetically pleasing results (minimizing "raggedness").</li>
<li><strong>Two-Phase Processing:</strong> Separating the line-packing logic from the space-distribution logic enhances clarity and maintainability.</li>
<li><strong>Explicit Handling of Edge Cases:</strong> The problem defines special rules for the last line and single-word lines (left-justification). The code correctly identifies and applies these rules <em>before</em> attempting general justification. This prevents issues like division by zero if <code>num_gaps</code> were 0 for a single-word line.</li>
<li><strong>Left-to-Right Space Distribution:</strong> When <code>total_spaces</code> cannot be perfectly divided among <code>num_gaps</code>, the extra spaces are distributed one by one starting from the leftmost gaps. This is a standard requirement for full justification.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity: <code>O(TotalCharacters)</code></strong></p>
<ul>
<li>Where <code>TotalCharacters</code> is the sum of <code>maxWidth</code> for all output lines (or equivalently, the sum of lengths of all words plus all spaces in the output).</li>
<li>Each word is visited by the <code>i</code> and <code>j</code> pointers a constant number of times across all lines.</li>
<li><code>len()</code> and <code>sum()</code> operations on <code>current_line_words</code> are proportional to the number of words on a line.</li>
<li>String construction (joining and concatenation) is proportional to the length of the final line string for each line.</li>
<li>Therefore, the total time is dominated by iterating through all words and constructing all output strings.</li>
</ul>
</li>
<li><p><strong>Space Complexity: <code>O(TotalCharacters)</code></strong></p>
<ul>
<li>The <code>result</code> list stores all generated lines. In the worst case, this could store <code>NumLines * MaxWidth</code> characters.</li>
<li><code>current_line_words</code> stores a subset of the input words, at most <code>O(maxWidth)</code> characters.</li>
<li>Other variables use constant space.</li>
<li>Thus, the total space complexity is proportional to the size of the final output.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>words</code> list:</strong> If <code>words</code> is empty, <code>i &lt; len(words)</code> is false, and an empty <code>result</code> list is returned immediately, which is correct.</li>
<li><strong>Single word per line:</strong> If <code>len(current_line_words)</code> is 1, the <code>num_words == 1</code> condition is met, leading to correct left-justification. This also correctly prevents division by zero if <code>num_gaps</code> were calculated as <code>num_words - 1</code> (which would be 0).</li>
<li><strong>Last line:</strong> The <code>is_last_line</code> check correctly identifies the last line and applies left-justification as required.</li>
<li><strong>Words exactly filling a line:</strong> If <code>total_spaces</code> is 0, <code>base_spaces</code> and <code>extra_spaces_slots</code> will both be 0, correctly adding no extra spaces between words.</li>
<li><strong>Words longer than <code>maxWidth</code>:</strong> The problem constraints typically guarantee <code>len(word) &lt;= maxWidth</code>. If a word <em>could</em> be longer, the current logic would place it on its own line, but <code>total_spaces</code> would be negative, leading to unexpected behavior. Assuming valid inputs per typical problem statements.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><p><strong>String Construction Efficiency:</strong> In Python, repeated <code>line += ...</code> within a loop can be less efficient than building a list of string parts and then using <code>"".join()</code> once at the end. For the <code>else</code> block (General Case), this could be modified:</p>
<pre><code class="language-python"># Inside the else block for General Case
line_parts = []
for k in range(num_words):
    line_parts.append(current_line_words[k])
    if k &lt; num_gaps:
        spaces_to_add = base_spaces
        if k &lt; extra_spaces_slots:
            spaces_to_add += 1
        line_parts.append(" " * spaces_to_add)
line = "".join(line_parts)
result.append(line)
</code></pre>
<p>This minor optimization can improve performance, especially for lines with many words and gaps.</p>
</li>
<li><p><strong>Helper Functions for Readability:</strong> The justification logic for the general case could be extracted into a dedicated helper function (e.g., <code>_justify_line(words_on_line, total_spaces, maxWidth)</code>). This would improve modularity.</p>
</li>
<li><p><strong>Error Handling:</strong> For a production system, adding checks for <code>maxWidth &lt; 0</code> or <code>len(word) &gt; maxWidth</code> might be beneficial, though usually not required for competitive programming.</p>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance Bottleneck:</strong> As mentioned in "Improvements", repeated string concatenation with <code>+=</code> can sometimes create many intermediate string objects, leading to performance degradation for very large inputs or many small concatenations. Using <code>"".join()</code> with a list of parts is generally preferred for building strings incrementally.</li>
<li><strong>No Security Concerns:</strong> The code primarily manipulates strings and integers based on problem logic, and does not involve external input, file I/O, or network operations that typically introduce security vulnerabilities.</li>
</ul>


### Code:
```python
class Solution(object):
    def fullJustify(self, words, maxWidth):
        """
        :type words: List[str]
        :type maxWidth: int
        :rtype: List[str]
        """
        result = []
        i = 0  # Pointer to the first word of the current line

        while i < len(words):
            # 1. Greedy Line Packing 📦
            
            # Start the current line with the word at index i
            j = i + 1  # Pointer to the next word to consider
            line_length = len(words[i])
            
            # Keep adding words until the next word can't fit
            # (word_length + current_length + 1 space for separation)
            while j < len(words) and line_length + len(words[j]) + 1 <= maxWidth:
                line_length += len(words[j]) + 1
                j += 1
            
            # words[i:j] are the words for the current line
            current_line_words = words[i:j]
            num_words = len(current_line_words)
            
            # 2. Justification Phase 📐
            
            # Calculate the total number of spaces needed
            # (total length allowed - total length of words)
            total_word_length = sum(len(w) for w in current_line_words)
            total_spaces = maxWidth - total_word_length
            
            # Check if this is the last line (j has reached the end)
            is_last_line = (j == len(words))

            if is_last_line or num_words == 1:
                # 2A. Last Line or Single-Word Line (Left Justified)
                
                # Join words with a single space
                line = " ".join(current_line_words)
                # Pad the rest with spaces to reach maxWidth
                line += " " * (maxWidth - len(line))
                result.append(line)
            
            else:
                # 2B. General Case (Fully Justified)
                
                # Number of gaps between words (always num_words - 1)
                num_gaps = num_words - 1
                
                # Calculate the minimum number of spaces for each gap
                base_spaces = total_spaces // num_gaps
                # Calculate the number of gaps that need one extra space
                extra_spaces_slots = total_spaces % num_gaps
                
                line = ""
                for k in range(num_words):
                    line += current_line_words[k]
                    
                    if k < num_gaps:  # Don't add spaces after the very last word
                        # Start with the base number of spaces
                        spaces_to_add = base_spaces
                        
                        # Assign extra space from the left slots first
                        if k < extra_spaces_slots:
                            spaces_to_add += 1
                        
                        line += " " * spaces_to_add
                
                result.append(line)
            
            # Move the pointer to the next word that starts a new line
            i = j
            
        return result
```
