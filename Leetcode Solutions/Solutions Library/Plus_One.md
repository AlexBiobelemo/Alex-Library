## Plus One
**Language:** python
**Tags:** python,array manipulation,arithmetic,digits

### Description:
<p>This <code>plusOne</code> function implements a common algorithm for adding one to a number represented as a list of its digits.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this function is to take a non-negative integer, represented as a list of its individual digits (e.g., <code>[1, 2, 3]</code> for 123), and return a new list representing that number plus one (e.g., <code>[1, 2, 4]</code>). It handles carry-over logic, including the edge case where all digits are 9s (e.g., <code>[9, 9]</code> becomes <code>[1, 0, 0]</code>).</p>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm simulates manual addition from right to left:</p>
<ul>
<li><strong>Initialize</strong>: Get the length <code>n</code> of the <code>digits</code> list.</li>
<li><strong>Iterate from Right</strong>: It loops through the <code>digits</code> list starting from the rightmost digit (<code>n-1</code>) down to the leftmost (<code>0</code>).</li>
<li><strong>Handle Non-9 Digit</strong>: If the current digit encountered is less than 9:<ul>
<li>It's incremented by 1.</li>
<li>Since no carry-over is needed for further digits, the modified <code>digits</code> list is immediately returned.</li>
</ul>
</li>
<li><strong>Handle 9 Digit (Carry-over)</strong>: If the current digit is 9:<ul>
<li>It's reset to 0 (representing the result of 9+1=0 with a carry-over of 1).</li>
<li>The loop continues to the next digit to the left, effectively processing the carry-over.</li>
</ul>
</li>
<li><strong>All Nines Case</strong>: If the loop completes without returning (meaning all digits from right to left were 9s):<ul>
<li>All digits in the original list have now been set to 0 (e.g., <code>[9,9,9]</code> becomes <code>[0,0,0]</code>).</li>
<li>A <code>1</code> needs to be prepended to this list to represent the final carry-over (e.g., <code>[1, 0, 0, 0]</code>). This is done by creating a new list <code>[1] + digits</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>List of Integers for Large Numbers</strong>: Using a list of integers allows representing numbers of arbitrary length, overcoming the limitations of fixed-size integer types (like <code>int</code> in C++/Java) for very large numbers.</li>
<li><strong>Right-to-Left Iteration</strong>: This mimics standard manual arithmetic, where addition begins from the least significant digit (rightmost).</li>
<li><strong>In-Place Modification (Partial)</strong>: For cases where a carry-over doesn't propagate to the leftmost digit, the modification happens in-place within the existing <code>digits</code> list, avoiding unnecessary memory allocation.</li>
<li><strong>Special Handling for All 9s</strong>: The logic <code>return [1] + digits</code> is a concise way to handle the case where adding one increases the number of digits.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><strong>Best Case</strong>: <code>O(1)</code> - If the rightmost digit (or any digit not equal to 9) is incremented and no carry-over propagates further left (e.g., <code>[1,2,3]</code> becomes <code>[1,2,4]</code>). The loop runs only once.</li>
<li><strong>Worst Case</strong>: <code>O(N)</code> - If all digits are 9s (e.g., <code>[9,9,9]</code>). The loop iterates through all <code>N</code> digits, and a new list of <code>N+1</code> elements is created at the end.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><strong>Best/Average Case</strong>: <code>O(1)</code> - When the modification is in-place, no significant extra space is used beyond a few variables.</li>
<li><strong>Worst Case</strong>: <code>O(N)</code> - When all digits are 9s (e.g., <code>[9,9,9]</code>), a new list of size <code>N+1</code> is created (e.g., <code>[1,0,0,0]</code>).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Single Digit Number</strong>:<ul>
<li><code>digits = [7]</code> -&gt; <code>[8]</code> (Correct)</li>
<li><code>digits = [9]</code> -&gt; <code>[1, 0]</code> (Correct)</li>
<li><code>digits = [0]</code> -&gt; <code>[1]</code> (Correct, handles a list representing zero)</li>
</ul>
</li>
<li><strong>Numbers with Trailing Nines</strong>:<ul>
<li><code>digits = [1, 2, 9]</code> -&gt; <code>[1, 3, 0]</code> (Correct)</li>
<li><code>digits = [4, 9, 9]</code> -&gt; <code>[5, 0, 0]</code> (Correct)</li>
</ul>
</li>
<li><strong>Numbers with All Nines</strong>:<ul>
<li><code>digits = [9, 9]</code> -&gt; <code>[1, 0, 0]</code> (Correct)</li>
</ul>
</li>
<li><strong>Empty List</strong>: The problem constraints typically imply <code>digits</code> will not be empty or will represent a non-negative integer. If <code>digits = []</code> were allowed, <code>n</code> would be 0, the <code>for</code> loop wouldn't run, and it would return <code>[1] + []</code> which is <code>[1]</code>. This could be considered incorrect if <code>[]</code> represents 0, as 0+1 should be <code>[1]</code> (or <code>[0]</code> if <code>[]</code> implies an empty number). However, standard problem interpretation is <code>[0]</code> represents 0.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The current code is quite clear and well-commented for its purpose. No major readability improvements needed.</li>
<li><strong>Alternative for <code>[1] + digits</code></strong>: For the all-9s case, an alternative to creating a new list <code>[1] + digits</code> would be to use <code>digits.insert(0, 1)</code>. This modifies the list in-place. While <code>insert(0, ...)</code> can be <code>O(N)</code> in Python, the <code>+</code> operator for list concatenation also creates a new list (and copies elements), so the asymptotic complexity remains the same for the worst-case space and time. The current approach using <code>+</code> is often considered more "Pythonic" for creating new lists.</li>
<li><strong>Converting to Integer (Not Recommended for Arbitrary Precision)</strong>:<ol>
<li>Convert the <code>digits</code> list to an integer (e.g., <code>int("".join(map(str, digits)))</code>).</li>
<li>Add 1.</li>
<li>Convert the result back to a string, then to a list of integers.
This approach is simpler for understanding but breaks down for numbers larger than what can fit in a standard integer type, which this problem often implicitly or explicitly aims to avoid. Python's integers handle arbitrary precision, so this <em>would</em> work in Python, but it's generally less efficient due to string conversions and not the expected algorithmic solution for this type of problem.</li>
</ol>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Large Inputs</strong>: For extremely long digit lists, the <code>O(N)</code> time complexity for the worst case is optimal, as you must touch every digit.</li>
<li><strong>Python's Arbitrary Precision Integers</strong>: In Python, integers automatically handle arbitrary precision, meaning you <em>could</em> convert the list to a single integer, add one, and convert back without overflow issues. However, this often involves string conversions which can be slower for extremely large numbers than the digit-by-digit approach. The provided solution directly manipulates the digits, which is generally more efficient for number-as-list problems.</li>
</ul>


### Code:
```python
class Solution(object):
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        n = len(digits)

        # Iterate from the rightmost digit to the leftmost
        for i in range(n - 1, -1, -1):
            # If the current digit is less than 9, just increment it and return
            if digits[i] < 9:
                digits[i] += 1
                return digits
            # If the current digit is 9, set it to 0 and continue to the next digit (carry-over)
            else:
                digits[i] = 0

        # If we reached here, it means all digits were 9 (e.g., [9,9,9])
        # We need to prepend a 1 to the digits array.
        # For example, [9,9,9] becomes [0,0,0] in the loop, then we add [1] + [0,0,0]
        return [1] + digits
```
