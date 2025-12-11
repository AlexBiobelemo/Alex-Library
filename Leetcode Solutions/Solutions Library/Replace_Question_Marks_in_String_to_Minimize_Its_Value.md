## Replace Question Marks in String to Minimize Its Value
**Language:** python
**Tags:** python,oop,heap,greedy,sorting

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to replace all <code>'?'</code> characters in a given string <code>s</code> with lowercase English letters such that:</p>
<ol>
<li>The "string value" is minimized. The "string value" is implicitly defined as the sum of the squares of the frequencies of all characters in the final string. This is minimized by making character frequencies as uniform as possible.</li>
<li>Among all strings that achieve the minimum "string value," the lexicographically smallest string is chosen.</li>
</ol>
<h3>2. How It Works</h3>
<p>The algorithm proceeds in several logical steps:</p>
<ul>
<li><p><strong>1. Initial Frequency Count &amp; <code>?</code> Tracking:</strong></p>
<ul>
<li>It first iterates through the input string <code>s</code>.</li>
<li>It counts the frequencies of all existing non-<code>?</code> characters in an array <code>freq</code> (size 26 for 'a'-'z').</li>
<li>It stores the indices of all <code>'?'</code> characters in a list <code>question_indices</code>.</li>
</ul>
</li>
<li><p><strong>2. Initialize Min-Heap:</strong></p>
<ul>
<li>A min-priority queue (min-heap <code>pq</code>) is initialized.</li>
<li>It's populated with tuples <code>(frequency, character_index)</code> for each of the 26 possible lowercase English letters, using the initial frequencies calculated. This allows efficient retrieval of the character with the current lowest frequency.</li>
</ul>
</li>
<li><p><strong>3. Determine Characters to Add:</strong></p>
<ul>
<li>For each <code>'?'</code> in the input string:<ul>
<li>The character with the lowest frequency is extracted from the <code>pq</code>.</li>
<li>This character is added to a temporary list <code>chars_to_add</code>.</li>
<li>Its frequency is incremented, and it's pushed back into the <code>pq</code>. This greedy approach ensures frequencies are balanced to minimize the sum of squares. Python's <code>heapq</code> tie-breaking (comparing <code>index</code> if <code>count</code> is equal) inherently favors 'a' over 'b' if their counts are identical, which aids in lexicographical minimization.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>4. Sort Added Characters:</strong></p>
<ul>
<li>The <code>chars_to_add</code> list is sorted in alphabetical order. This step is crucial for achieving the lexicographically smallest final string.</li>
</ul>
</li>
<li><p><strong>5. Reconstruct the String:</strong></p>
<ul>
<li>The original string <code>s</code> is converted into a list of characters (<code>s_list</code>) for mutable access.</li>
<li>The code iterates through the <code>question_indices</code> (which are already sorted by position in the original string).</li>
<li>For each <code>?</code> position, it replaces it with the next character from the <em>sorted</em> <code>chars_to_add</code> list.</li>
<li>Finally, <code>s_list</code> is joined back into a string and returned.</li>
</ul>
</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Frequency Array (<code>freq</code>)</strong>: A simple, direct array mapping character ASCII values to counts. Provides <code>O(1)</code> access for frequency updates.</li>
<li><strong>Min-Heap (<code>pq</code>)</strong>: The core data structure for the greedy approach. It efficiently (<code>O(log K)</code> where <code>K=26</code>) provides the character with the minimum current frequency, which is key to balancing frequencies and minimizing the "string value."</li>
<li><strong>Sorting <code>chars_to_add</code></strong>: After determining <em>which</em> characters to add (based on frequency balancing), sorting them ensures that when placed into the <code>?</code> positions (which are filled from left to right), the resulting string is lexicographically smallest. The "string value" (sum of squares) is independent of the order, only the counts matter.</li>
<li><strong>Mutable String (<code>s_list</code>)</strong>: Python strings are immutable. Converting to a list allows efficient in-place modification before joining back into a string.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of the input string <code>s</code>, and <code>M</code> be the number of <code>'?'</code> characters. <code>K</code> is the size of the alphabet (26).</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li>Step 1 (Counting frequencies): <code>O(N)</code> for iterating through the string.</li>
<li>Step 2 (Building heap): <code>O(K log K)</code> for inserting <code>K</code> elements.</li>
<li>Step 3 (Determining characters to add): <code>M</code> operations, each involving <code>heapq.heappop</code> and <code>heapq.heappush</code>, both <code>O(log K)</code>. Total <code>O(M log K)</code>.</li>
<li>Step 4 (Sorting <code>chars_to_add</code>): <code>O(M log M)</code> in the worst case (if all characters are <code>?</code>).</li>
<li>Step 5 (Reconstructing string): <code>O(N)</code> for <code>list(s)</code>, <code>O(M)</code> for replacements, <code>O(N)</code> for <code>"".join()</code>. Total <code>O(N)</code>.</li>
<li><strong>Overall Time Complexity</strong>: <code>O(N + K log K + M log K + M log M)</code>. Since <code>K</code> is a small constant, this simplifies to <code>O(N + M log M)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>freq</code>: <code>O(K)</code></li>
<li><code>question_indices</code>: <code>O(M)</code></li>
<li><code>pq</code>: <code>O(K)</code></li>
<li><code>chars_to_add</code>: <code>O(M)</code></li>
<li><code>s_list</code>: <code>O(N)</code></li>
<li><strong>Overall Space Complexity</strong>: <code>O(N + K + M)</code>. Since <code>M &lt;= N</code> and <code>K</code> is a constant, this simplifies to <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty String (<code>s = ""</code>)</strong>:<ul>
<li><code>freq</code> will be <code>[0]*26</code>, <code>question_indices</code> empty.</li>
<li><code>pq</code> will contain <code>(0,0)</code> to <code>(0,25)</code>.</li>
<li><code>chars_to_add</code> will be empty.</li>
<li><code>s_list</code> will be <code>[]</code>. <code>"".join(s_list)</code> returns <code>""</code>. Correct.</li>
</ul>
</li>
<li><strong>String with no <code>?</code> (<code>s = "abc"</code>)</strong>:<ul>
<li><code>question_indices</code> will be empty.</li>
<li>The loop to determine <code>chars_to_add</code> won't run.</li>
<li><code>s_list</code> will be <code>['a', 'b', 'c']</code>.</li>
<li>The replacement loop won't run. <code>"".join(s_list)</code> returns <code>"abc"</code>. Correct.</li>
</ul>
</li>
<li><strong>String with all <code>?</code> (<code>s = "???"</code>)</strong>:<ul>
<li><code>freq</code> will be <code>[0]*26</code>. <code>question_indices</code> will be <code>[0, 1, 2]</code>.</li>
<li>The heap will initially contain <code>(0,0)</code> to <code>(0,25)</code>.</li>
<li>For the three <code>?</code>s, it will pick 'a', then 'b', then 'c' (due to <code>heapq</code>'s tie-breaking favoring smaller indices).</li>
<li><code>chars_to_add</code> will be <code>['a', 'b', 'c']</code>, which after sorting remains <code>['a', 'b', 'c']</code>.</li>
<li><code>s_list</code> becomes <code>['a', 'b', 'c']</code>. Result: <code>"abc"</code>. This is correct, as it minimizes frequency variance and is lexicographically smallest.</li>
</ul>
</li>
<li><strong>Correctness of Value Minimization</strong>: The greedy approach of always picking the character with the current lowest frequency from the min-heap to fill a <code>?</code> is proven to minimize the sum of squares of frequencies. By balancing frequencies as much as possible, you minimize their variance, thus minimizing the sum of their squares.</li>
<li><strong>Correctness of Lexicographical Minimization</strong>:<ul>
<li>Python's <code>heapq</code> tie-breaking <code>(count, index)</code> naturally prefers 'a' over 'b' if counts are equal, which is helpful.</li>
<li>Sorting <code>chars_to_add</code> (<code>chars_to_add.sort()</code>) ensures that the <em>set</em> of characters chosen for replacement is assigned in alphabetical order.</li>
<li>By iterating through <code>question_indices</code> (which are sorted by definition, being filled by <code>enumerate</code>) and assigning the smallest available character from <code>chars_to_add</code> to the earliest <code>?</code> position, the resulting string will be lexicographically minimized.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Clarity of "String Value"</strong>: The problem statement's definition of "string value" isn't explicit in the code comments. Adding a comment clarifying that it implies <code>sum(frequency_i^2)</code> would enhance understanding for future readers.</li>
<li><strong>Constant for Alphabet Size</strong>: Instead of <code>26</code> and <code>ord('a')</code>, using constants like <code>ALPHABET_SIZE = 26</code> and <code>A_ORD = ord('a')</code> can improve readability and maintainability, though for such a standard constant like 26, it's often acceptable.</li>
<li><strong>Minor Optimization (Python specific)</strong>: For very short strings, the overhead of list conversions and joins might be noticeable, but for typical competitive programming constraints, the current approach is standard and efficient.</li>
<li><strong>Alternative for Lexicographical order</strong>: If the problem <em>only</em> required minimizing the string value and <em>not</em> lexicographical order, then step 4 (<code>chars_to_add.sort()</code>) and step 5's dependency on it could be simplified. You could directly assign <code>chr(idx + ord('a'))</code> to <code>s_list[q_idx]</code> as soon as it's popped from the heap, without collecting and sorting. However, given the problem's implicit dual requirement, the current approach is necessary.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: The code operates solely on the input string and standard library functions. There are no external dependencies, file I/O, or network operations, so no direct security vulnerabilities are apparent.</li>
<li><strong>Performance</strong>: The chosen algorithm is highly efficient. The use of a min-heap for frequency management is optimal for this type of problem where you repeatedly need the minimum element. The constant size of the alphabet (<code>K=26</code>) means <code>log K</code> operations are practically constant time, making the dominant factors the string length <code>N</code> and the number of <code>?</code>s <code>M</code> (for the final sort). For typical string lengths (up to 10^5-10^6), <code>O(N + M log M)</code> is well within practical limits.</li>
</ul>


### Code:
```python
import heapq

class Solution:
    def minimizeStringValue(self, s: str) -> str:
        # 1. Count frequencies of existing characters
        freq = [0] * 26
        question_indices = []
        
        for i, char in enumerate(s):
            if char == '?':
                question_indices.append(i)
            else:
                freq[ord(char) - ord('a')] += 1
        
        # 2. Use a min-heap to efficiently find the character with the lowest frequency
        # Heap stores tuples: (count, index)
        # Python's heap automatically handles ties by comparing the second element (index),
        # ensuring we pick 'a' (index 0) over 'b' (index 1) if counts are equal.
        pq = []
        for i in range(26):
            heapq.heappush(pq, (freq[i], i))
            
        chars_to_add = []
        
        # 3. Determine the set of characters to add
        for _ in range(len(question_indices)):
            count, idx = heapq.heappop(pq)
            chars_to_add.append(chr(idx + ord('a')))
            # Push back with incremented count
            heapq.heappush(pq, (count + 1, idx))
            
        # 4. Sort the characters to ensure the final string is lexicographically smallest
        # The total cost is invariant to the order of characters, only their frequencies matter.
        chars_to_add.sort()
        
        # 5. Reconstruct the string
        s_list = list(s)
        add_ptr = 0
        for idx in question_indices:
            s_list[idx] = chars_to_add[add_ptr]
            add_ptr += 1
            
        return "".join(s_list)
```
