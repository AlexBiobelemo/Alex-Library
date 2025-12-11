## Multiply Strings
**Language:** python
**Tags:** string multiplication,large number arithmetic,mathematical algorithm,digit by digit

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code implements string multiplication for two non-negative integer strings, <code>num1</code> and <code>num2</code>.</p>
<ul>
<li><strong>Purpose:</strong> To multiply large numbers that might exceed the capacity of standard integer data types (like Python's arbitrary-precision integers, but this problem often arises in contexts where that's not available or a specific algorithm is required).</li>
<li><strong>Analogy:</strong> It simulates the "long multiplication" method taught in elementary school, where you multiply each digit of one number by each digit of the other and sum the results, carefully managing carries and positional values.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm processes the multiplication digit by digit, accumulating results in an array.</p>
<ol>
<li><strong>Base Case Handling:</strong> It first checks if either <code>num1</code> or <code>num2</code> is "0". If so, the product is "0", and it returns immediately.</li>
<li><strong>Result Array Initialization:</strong> An array <code>result</code> is created, filled with zeros. Its size is <code>len(num1) + len(num2)</code>, which is the maximum possible length of the product (e.g., 99 * 99 = 9801, length 4 = 2+2).</li>
<li><strong>Digit-by-Digit Multiplication:</strong><ul>
<li>It iterates through <code>num1</code> from right to left (<code>i</code>).</li>
<li>For each digit <code>digit1</code> from <code>num1</code>, it iterates through <code>num2</code> from right to left (<code>j</code>).</li>
<li>For each pair of digits (<code>digit1</code>, <code>digit2</code>), their product <code>prod</code> is calculated.</li>
</ul>
</li>
<li><strong>Placement and Carry Handling:</strong><ul>
<li>The <code>prod</code> contributes to two positions in the <code>result</code> array:<ul>
<li><code>pos2 = i + j + 1</code>: This is the "units" place of the current <code>prod</code>.</li>
<li><code>pos1 = i + j</code>: This is the "tens" place (carry-over) for the current <code>prod</code>.</li>
</ul>
</li>
<li><code>prod</code> is added to the existing value at <code>result[pos2]</code> (which might contain a carry from previous calculations). This <code>temp_sum</code> is then split:<ul>
<li><code>result[pos2]</code> is updated with <code>temp_sum % 10</code> (the units digit).</li>
<li><code>temp_sum // 10</code> (the carry-over) is added to <code>result[pos1]</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>String Conversion and Leading Zeros:</strong><ul>
<li>After all multiplications, the <code>result</code> array (which now contains integer digits) is converted into a string.</li>
<li>A crucial step is finding the <code>first_digit_idx</code> to skip any leading zeros in the <code>result</code> array (e.g., <code>[0, 0, 1, 2, 3]</code> should become "123").</li>
<li>Finally, the relevant slice of the <code>result</code> array is converted to strings and joined.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure: List of Integers (<code>result</code>)</strong><ul>
<li><strong>Choice:</strong> A Python list is used to store the digits of the product. This allows efficient element-wise updates and carry propagation.</li>
<li><strong>Trade-off:</strong> Requires a final conversion to a string, but this is a standard pattern for digit-based arithmetic.</li>
</ul>
</li>
<li><strong>Algorithm: Manual Long Multiplication Simulation</strong><ul>
<li><strong>Choice:</strong> Mimics the pen-and-paper method, processing digits from right to left (least significant to most significant).</li>
<li><strong>Trade-off:</strong> Simple to understand and implement compared to more complex algorithms, but not asymptotically the fastest for extremely large numbers.</li>
</ul>
</li>
<li><strong>Indexing Logic (<code>pos1 = i + j</code>, <code>pos2 = i + j + 1</code>)</strong><ul>
<li><strong>Core Insight:</strong> This mapping correctly places the product of <code>num1[i]</code> and <code>num2[j]</code> into the appropriate positions within the <code>result</code> array. When <code>i</code> and <code>j</code> are indices from the <em>right</em> of their respective strings, <code>i+j</code> and <code>i+j+1</code> (from the <em>left</em> of the <code>result</code> array) represent the correct shifted positions for their product.</li>
</ul>
</li>
<li><strong>Right-to-Left Iteration:</strong><ul>
<li><strong>Choice:</strong> <code>range(m - 1, -1, -1)</code> ensures that we process the least significant digits first, which naturally aligns with how carries propagate in addition.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>m</code> be the length of <code>num1</code> and <code>n</code> be the length of <code>num2</code>.</p>
<ul>
<li><strong>Time Complexity:</strong><ul>
<li><strong>Digit Multiplication:</strong> The nested loops iterate <code>m * n</code> times. Inside the inner loop, operations (integer conversion, multiplication, addition, modulo, integer division, list access) are all <code>O(1)</code>. This dominates the calculation part.</li>
<li><strong>String Conversion:</strong> The <code>while</code> loop to find the first non-zero digit and the <code>map(str, ...)</code> and <code>"".join(...)</code> operations take <code>O(m + n)</code> time.</li>
<li><strong>Overall:</strong> <code>O(m * n)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong><ul>
<li><strong><code>result</code> array:</strong> <code>O(m + n)</code> to store the product digits.</li>
<li><strong>Temporary variables:</strong> <code>O(1)</code>.</li>
<li><strong>Overall:</strong> <code>O(m + n)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Zero Multiplier:</strong> Handled explicitly by <code>if num1 == "0" or num2 == "0": return "0"</code>. This is correct.</li>
<li><strong>Single-Digit Numbers:</strong> Works correctly (e.g., "7" * "8" would correctly produce "56").</li>
<li><strong>Numbers with Internal Zeros:</strong> The digit-by-digit multiplication and carry handling correctly accounts for zeros (e.g., "105" * "2").</li>
<li><strong>Numbers Resulting in Leading Zeros:</strong> The <code>while</code> loop <code>while first_digit_idx &lt; len(result) - 1 and result[first_digit_idx] == 0:</code> correctly identifies and skips leading zeros, ensuring outputs like "1" for "01" (though the internal array would be <code>[0, 1]</code>). For a product like "1" * "1", the <code>result</code> array would be <code>[0, 1]</code>, and the logic correctly returns "1".</li>
<li><strong>Large Numbers:</strong> The approach scales correctly for very long strings as long as memory permits.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability (Minor):</strong><ul>
<li>The comments regarding <code>pos1</code> and <code>pos2</code> are good, but could be slightly expanded to explicitly state <em>why</em> <code>i+j</code> and <code>i+j+1</code> are the correct indices (i.e., mapping reverse string indices to forward result array indices).</li>
</ul>
</li>
<li><strong>Performance (for extremely large numbers):</strong><ul>
<li>For numbers with thousands of digits, <code>O(m*n)</code> can become too slow. Algorithms like <strong>Karatsuba multiplication</strong> (which is a divide-and-conquer approach) achieve <code>O(N^log2(3))</code> or approximately <code>O(N^1.585)</code> complexity. More advanced algorithms like Toom-Cook or Schönhage-Strassen offer even better asymptotic performance but have higher overhead for smaller inputs. For typical competitive programming constraints (e.g., up to 100-200 digits), <code>O(M*N)</code> is usually sufficient.</li>
</ul>
</li>
<li><strong>Robustness (Input Validation):</strong><ul>
<li>The problem implies valid inputs (non-negative digit strings). In a real-world scenario, you might want to add checks to ensure <code>num1</code> and <code>num2</code> contain only digits and are not empty, raising an error or handling invalid characters gracefully.</li>
</ul>
</li>
<li><strong>Alternative Implementation Strategy (Conceptual):</strong><ul>
<li>One could implement this by computing partial products for each digit of <code>num2</code> (e.g., <code>num1 * digit2</code> then shifting zeros), and then summing these partial products. This often involves more complex string addition logic, which can be harder to manage than the current carry-propagation into a pre-allocated array.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The <code>O(M*N)</code> complexity is a known characteristic of this elementary multiplication method. For practical purposes, up to several hundred digits, it performs acceptably. Beyond that, specialized algorithms are needed. Python's built-in integers handle arbitrary precision, but this exercise simulates the underlying logic.</li>
<li><strong>Security:</strong> There are no inherent security vulnerabilities in the arithmetic logic itself. Assuming inputs <code>num1</code> and <code>num2</code> are sanitized and guaranteed to be valid numeric strings (as is common in competitive programming problems), the code is safe. If arbitrary user input were accepted, converting non-digit characters to <code>int()</code> would raise a <code>ValueError</code>, which would need to be handled.</li>
</ul>


### Code:
```python
class Solution(object):
    def multiply(self, num1, num2):
        """
        :type num1: str
        :type num2: str
        :rtype: str
        """
        if num1 == "0" or num2 == "0":
            return "0"

        # Initialize result array with zeros.
        # The maximum length of the product is len(num1) + len(num2).
        m, n = len(num1), len(num2)
        result = [0] * (m + n)

        # Iterate num1 from right to left
        for i in range(m - 1, -1, -1):
            digit1 = int(num1[i])
            # Iterate num2 from right to left
            for j in range(n - 1, -1, -1):
                digit2 = int(num2[j])

                # Calculate the product of current digits
                prod = digit1 * digit2

                # Determine the positions in the result array
                # pos1 is for the tens place of the current product
                # pos2 is for the units place of the current product
                pos1 = i + j
                pos2 = i + j + 1

                # Add the product to the corresponding positions in the result array
                # The sum includes any carry from previous calculations at pos2
                temp_sum = result[pos2] + prod

                # Update the units digit at pos2
                result[pos2] = temp_sum % 10
                # Add the carry to the tens digit at pos1
                result[pos1] += temp_sum // 10

        # Convert the result array to a string
        # Find the first non-zero digit to handle leading zeros
        first_digit_idx = 0
        while first_digit_idx < len(result) - 1 and result[first_digit_idx] == 0:
            first_digit_idx += 1
        
        return "".join(map(str, result[first_digit_idx:]))
```
