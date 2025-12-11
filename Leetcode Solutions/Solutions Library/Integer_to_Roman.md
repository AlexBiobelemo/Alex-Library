## Integer to Roman
**Language:** python
**Tags:** python,greedy,string,conversion

### Description:
<p>This code snippet converts an integer into its Roman numeral representation. It uses a greedy approach, iterating through a predefined list of Roman numeral values from largest to smallest, including special subtractive cases like <code>CM</code> (900) and <code>IV</code> (4).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Purpose</strong>: The <code>intToRoman</code> method's goal is to convert a given positive integer (<code>num</code>) into its equivalent Roman numeral string.</li>
<li><strong>Typical Use</strong>: This type of conversion is a classic programming challenge often found in interviews or coding platforms, testing understanding of algorithms and number systems.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Mapping Initialization</strong>: A list of tuples <code>roman_map</code> is created. Each tuple contains an integer value and its corresponding Roman numeral symbol (e.g., <code>(1000, "M")</code>, <code>(900, "CM")</code>). This map is crucial:<ul>
<li>It includes both standard values (1000, 500, 100, etc.) and subtractive values (900, 400, 90, 40, 9, 4).</li>
<li>It is sorted in <strong>descending order</strong> of integer values.</li>
</ul>
</li>
<li><strong>Greedy Iteration</strong>:<ul>
<li>The code iterates through each <code>(value, symbol)</code> pair in <code>roman_map</code>.</li>
<li>For each pair, it enters a <code>while</code> loop that continues as long as the input <code>num</code> is greater than or equal to the current <code>value</code>.</li>
<li>Inside the <code>while</code> loop:<ul>
<li>The <code>symbol</code> is appended to a <code>result</code> list.</li>
<li>The <code>value</code> is subtracted from <code>num</code>.</li>
</ul>
</li>
<li>This process effectively "consumes" the largest possible Roman numeral symbol(s) at each step until <code>num</code> is less than the current <code>value</code>, then moves to the next smaller value in <code>roman_map</code>.</li>
</ul>
</li>
<li><strong>Result Construction</strong>: Once the <code>for</code> loop completes (meaning <code>num</code> has been reduced to 0), all necessary Roman numeral symbols are in the <code>result</code> list. These are then joined together into a single string and returned.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure (<code>roman_map</code>)</strong>:<ul>
<li><strong>List of Tuples</strong>: Using a list of <code>(value, symbol)</code> tuples is effective. The list maintains the critical descending order, which is essential for the greedy algorithm. Tuples provide immutable pairs.</li>
<li><strong>Inclusion of Subtractive Forms</strong>: Explicitly including values like <code>900 ("CM")</code>, <code>400 ("CD")</code>, <code>90 ("XC")</code>, <code>40 ("XL")</code>, <code>9 ("IX")</code>, and <code>4 ("IV")</code> in the <code>roman_map</code> is a key design choice. This simplifies the logic by allowing a straightforward greedy approach, as these combinations directly represent the most efficient way to form those numbers (e.g., <code>900</code> is <code>CM</code>, not <code>DCCCC</code>).</li>
</ul>
</li>
<li><strong>Algorithm (Greedy)</strong>:<ul>
<li>The algorithm is greedy because at each step, it attempts to use the largest possible Roman numeral value less than or equal to the remaining <code>num</code>.</li>
<li><strong>Trade-off</strong>: This greedy approach works perfectly for Roman numerals <em>because</em> the specific values and subtractive rules are carefully chosen and ordered in <code>roman_map</code>. If the Roman numeral system had more complex rules or if the map wasn't ordered/defined correctly, a simple greedy approach might not yield the shortest or canonical representation. Here, it's optimal and simple.</li>
</ul>
</li>
<li><strong>String Building (<code>result</code> list and <code>"".join()</code>)</strong>: Appending characters to a list and then joining them at the end is the most performant way to build strings iteratively in Python, avoiding repeated string allocations and copying that direct string concatenation (<code>+=</code>) would entail in a loop.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Assuming <code>N</code> is the input integer <code>num</code>, and the typical constraints for <code>N</code> are <code>1 &lt;= N &lt;= 3999</code> (as Roman numerals commonly don't represent 0 or numbers larger than 3999 without further extensions).</p>
<ul>
<li><strong>Time Complexity</strong>: <strong>O(1)</strong><ul>
<li>The <code>roman_map</code> has a fixed size (13 elements).</li>
<li>The outer <code>for</code> loop iterates a fixed number of times (13 times).</li>
<li>The inner <code>while</code> loop's total iterations across all <code>value</code>s are bounded by the maximum number of symbols in a Roman numeral (e.g., <code>MMMCMXCIX</code> for 3999 is 7 characters).</li>
<li>Thus, the total number of operations (comparisons, subtractions, list appends) is constant, independent of the input <code>N</code> within the typical range.</li>
<li>The final <code>"".join(result)</code> operation takes time proportional to the length of the resulting string, which is also bounded by a small constant (max 7 characters).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <strong>O(1)</strong><ul>
<li><code>roman_map</code>: Stores a fixed number of tuples, constant space.</li>
<li><code>result</code>: Stores a list of strings. Its maximum length is bounded by the maximum number of Roman numeral characters (e.g., 7 for 3999), constant space.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Smallest Input (<code>num = 1</code>)</strong>:<ul>
<li>The loop reaches <code>(1, "I")</code>. <code>num</code> is <code>1 &gt;= 1</code>. "I" is appended. <code>num</code> becomes 0. Loop finishes. Correct.</li>
</ul>
</li>
<li><strong>Exact Matches (e.g., <code>num = 10</code>, <code>num = 500</code>)</strong>:<ul>
<li>The corresponding <code>(10, "X")</code> or <code>(500, "D")</code> will be matched directly, added to <code>result</code>, and <code>num</code> will become 0. Correct.</li>
</ul>
</li>
<li><strong>Subtractive Cases (e.g., <code>num = 9</code>, <code>num = 40</code>)</strong>:<ul>
<li>Because <code>(9, "IX")</code> and <code>(40, "XL")</code> are explicitly in <code>roman_map</code> and ordered correctly, these are handled directly and efficiently. For instance, <code>9</code> will directly use <code>IX</code> rather than <code>V</code> and then <code>IIII</code>. This is crucial for obtaining the canonical representation. Correct.</li>
</ul>
</li>
<li><strong>Largest Typical Input (<code>num = 3999</code>)</strong>:<ul>
<li>The algorithm correctly produces "MMMCMXCIX" by iteratively taking <code>M</code> (x3), <code>CM</code> (x1), <code>XC</code> (x1), and <code>IX</code> (x1). Correct.</li>
</ul>
</li>
<li><strong>Input <code>num = 0</code></strong>:<ul>
<li>The <code>while num &gt;= value</code> condition will never be met. The <code>result</code> list remains empty, and <code>"".join([])</code> correctly returns an empty string <code>""</code>. This is generally acceptable, as Roman numerals don't typically represent zero.</li>
</ul>
</li>
<li><strong>Negative Inputs</strong>: The code does not handle negative numbers, as Roman numerals are typically for positive integers. An input validation check could be added if negative inputs are a concern.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Input Validation</strong>: For robustness, one could add a check at the beginning to ensure <code>num</code> is within the expected valid range (e.g., <code>1 &lt;= num &lt;= 3999</code>). If <code>num</code> is outside this range, raise an error or return an appropriate message.</li>
<li><strong>Readability</strong>: The current code is already very readable due to the clear <code>roman_map</code> and straightforward greedy logic. No major improvements are needed here.</li>
<li><strong>Performance</strong>: For the typical constraints of this problem, the code is already O(1) time and space, so further performance optimizations are unnecessary and unlikely to yield significant gains.</li>
<li><strong>Alternative <code>roman_map</code> Representation (Less common/beneficial)</strong>:<ul>
<li>Could use a <code>dict</code> if iteration order wasn't critical or if you wanted to map <em>symbols</em> to <em>values</em> for a different problem (e.g., Roman to int). However, for this problem, the ordered list of tuples is superior.</li>
</ul>
</li>
<li><strong>Alternative Algorithm (Less efficient for this specific problem)</strong>:<ul>
<li>A recursive solution could be formulated, but it would likely be more complex and potentially less efficient (due to function call overhead) than this iterative greedy approach for this problem.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no apparent security vulnerabilities. The code operates on a simple integer input, performs internal calculations, and produces a string. It doesn't interact with external systems, user-supplied complex data structures, or sensitive information.</li>
<li><strong>Performance</strong>: As analyzed, the code is highly performant with O(1) time and space complexity for the typical range of inputs. It efficiently uses Python's string building (<code>list</code> + <code>join</code>) mechanism.</li>
</ul>


### Code:
```python
class Solution(object):
    def intToRoman(self, num):
        """
        :type num: int
        :rtype: str
        """
        roman_map = [
            (1000, "M"), (900, "CM"), (500, "D"), (400, "CD"),
            (100, "C"), (90, "XC"), (50, "L"), (40, "XL"),
            (10, "X"), (9, "IX"), (5, "V"), (4, "IV"),
            (1, "I")
        ]
        
        result = []
        
        for value, symbol in roman_map:
            while num >= value:
                result.append(symbol)
                num -= value
        
        return "".join(result)
```
