## Fancy Sequence
**Language:** python
**Tags:** modular arithmetic,data structure,transformations,algorithms

### Description:
<p>This <code>Fancy</code> class implements a "Fancy Sequence" data structure that supports appending values, applying global additions and multiplications to all existing elements, and retrieving the current value of any element. The key challenge is to perform these operations efficiently while adhering to modular arithmetic.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The <code>Fancy</code> class manages a sequence of integers and allows for two types of global transformations:</p>
<ul>
<li><code>addAll(inc)</code>: Adds <code>inc</code> to every element in the sequence.</li>
<li><code>multAll(m)</code>: Multiplies every element in the sequence by <code>m</code>.</li>
</ul>
<p>It also supports:</p>
<ul>
<li><code>append(val)</code>: Adds a new value <code>val</code> to the end of the sequence.</li>
<li><code>getIndex(idx)</code>: Retrieves the current transformed value of the element at a specific index <code>idx</code>.</li>
</ul>
<p>All operations are performed modulo <code>10^9 + 7</code>. The core intent is to achieve these operations in better than O(N) time for global updates, typically O(1) or O(log MOD), by using a lazy propagation technique for the transformations.</p>
<hr>
<h3>2. How It Works</h3>
<p>The class leverages an affine transformation <code>(A * x + B) % MOD</code> to represent the cumulative effect of <code>addAll</code> and <code>multAll</code> operations.</p>
<ul>
<li><strong>Initialization (<code>__init__</code>)</strong>:<ul>
<li><code>sequence</code>: A list storing the original, untransformed values appended by <code>append()</code>.</li>
<li><code>transformations</code>: A list of tuples <code>(A, B)</code>. <code>transformations[k]</code> stores the global <code>(A, B)</code> transformation state <em>at the exact moment</em> the <code>k</code>-th element (<code>sequence[k]</code>) was appended. It starts with <code>[(1, 0)]</code> representing an identity transformation (1 * x + 0).</li>
<li><code>MOD</code>: The prime modulus <code>10^9 + 7</code> used for all calculations.</li>
</ul>
</li>
<li><strong>Modular Inverse (<code>_mod_inv</code>)</strong>:<ul>
<li>A helper method to compute <code>a^(MOD-2) % MOD</code> using Fermat's Little Theorem, which gives <code>a^-1 % MOD</code> for prime <code>MOD</code>. This is crucial for modular division.</li>
</ul>
</li>
<li><strong>Append (<code>append(val)</code>)</strong>:<ul>
<li>Adds <code>val % MOD</code> to <code>self.sequence</code>.</li>
<li>Crucially, it appends a <em>copy</em> of the <em>current global transformation state</em> (<code>self.transformations[-1]</code>) to <code>self.transformations</code>. This records the state at the time <code>val</code> was added.</li>
</ul>
</li>
<li><strong>Add All (<code>addAll(inc)</code>)</strong>:<ul>
<li>Updates only the <em>latest</em> global transformation <code>(A, B)</code> stored in <code>self.transformations[-1]</code>.</li>
<li>If the current transformation is <code>A * x + B</code>, adding <code>inc</code> makes it <code>A * x + (B + inc)</code>. So, <code>B</code> is updated to <code>(B + inc) % MOD</code>.</li>
</ul>
</li>
<li><strong>Multiply All (<code>multAll(m)</code>)</strong>:<ul>
<li>Updates only the <em>latest</em> global transformation <code>(A, B)</code> stored in <code>self.transformations[-1]</code>.</li>
<li>If the current transformation is <code>A * x + B</code>, multiplying by <code>m</code> makes it <code>m * (A * x + B) = (m * A) * x + (m * B)</code>. So, <code>A</code> becomes <code>(A * m) % MOD</code> and <code>B</code> becomes <code>(B * m) % MOD</code>.</li>
</ul>
</li>
<li><strong>Get Index (<code>getIndex(idx)</code>)</strong>:<ul>
<li>Retrieves the original value <code>v_initial = self.sequence[idx]</code>.</li>
<li>Retrieves <code>(A_i, B_i) = self.transformations[idx]</code>, which is the global transformation state <em>when <code>v_initial</code> was appended</em>.</li>
<li>Retrieves <code>(A_T, B_T) = self.transformations[-1]</code>, which is the <em>current</em> global transformation state.</li>
<li>The core idea: We need to find the effective transformation that occurred <em>between</em> the time <code>v_initial</code> was appended (when the global state was <code>(A_i, B_i)</code>) and the current time (when the global state is <code>(A_T, B_T)</code>).</li>
<li>This "delta" transformation <code>(M, I)</code> must satisfy: <code>A_T * x + B_T = M * (A_i * x + B_i) + I</code> for any <code>x</code>.<ul>
<li>By comparing coefficients, we derive:<ul>
<li><code>M = (A_T * A_i^-1) % MOD</code> (using modular inverse for <code>A_i^-1</code>)</li>
<li><code>I = (B_T - B_i * M) % MOD</code></li>
</ul>
</li>
</ul>
</li>
<li>The final value of <code>v_initial</code> is then <code>(v_initial * M + I) % MOD</code>.</li>
<li><strong>Special Case <code>A_i == 0</code></strong>: If <code>A_i</code> is 0, it means a <code>multAll(0)</code> occurred at or before <code>idx</code>. In this scenario, <code>A</code> will remain 0 for all subsequent operations. Therefore, <code>A_T</code> must also be 0. The effective transformation becomes <code>0 * x + B_T = B_T</code>, so the function directly returns <code>B_T</code>.</li>
<li>Ensures the final result is non-negative by adding <code>MOD</code> and taking modulo again.</li>
</ul>
</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Lazy Propagation of Transformations</strong>: Instead of iterating through <code>self.sequence</code> to update each element on <code>addAll</code> or <code>multAll</code>, the class only updates a single global <code>(A, B)</code> pair (<code>self.transformations[-1]</code>). This is the most critical design choice for efficiency.</li>
<li><strong>History of Transformations</strong>: Storing <code>self.transformations</code> allows <code>getIndex</code> to calculate the <em>net</em> transformation applied to a specific element since its append time, rather than trying to reverse or re-apply all operations.</li>
<li><strong>Modular Arithmetic</strong>: All calculations are performed modulo <code>10^9 + 7</code> to handle potentially large numbers and fit within integer limits.</li>
<li><strong>Fermat's Little Theorem</strong>: Used for modular division (finding <code>A_i^-1</code>), which is essential for calculating the <code>M</code> component of the delta transformation. This relies on <code>MOD</code> being a prime number.</li>
<li><strong>Affine Transformation Model</strong>: The choice of <code>A * x + B</code> to represent cumulative operations is well-suited for this problem, as additions and multiplications compose naturally into this form:<ul>
<li><code>m * (A*x + B) + inc = (m*A)*x + (m*B + inc)</code></li>
<li>This simplifies to a new <code>A'</code> and <code>B'</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>__init__</code>: O(1)</li>
<li><code>_mod_inv(a)</code>: O(log MOD) due to <code>pow(a, MOD-2, MOD)</code>.</li>
<li><code>append(val)</code>: O(1) (list append, single assignment).</li>
<li><code>addAll(inc)</code>: O(1) (single assignment).</li>
<li><code>multAll(m)</code>: O(1) (single assignment).</li>
<li><code>getIndex(idx)</code>: O(log MOD) due to the call to <code>_mod_inv</code>. All other operations are O(1).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li>O(N), where N is the number of elements appended to the sequence. Both <code>self.sequence</code> and <code>self.transformations</code> grow linearly with N.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Sequence / Invalid Index</strong>: <code>getIndex</code> correctly returns -1 if <code>idx</code> is out of bounds.</li>
<li><strong>First Element (<code>idx = 0</code>)</strong>: The logic holds. <code>transformations[0]</code> is <code>(1,0)</code>, correctly representing the state when the first element was appended.</li>
<li><strong><code>multAll(0)</code> / <code>A_i == 0</code></strong>: This is a critical edge case. If <code>A_i</code> becomes 0 (due to a <code>multAll(0)</code>), any subsequent multiplication by <code>m</code> will keep <code>A</code> as 0 (<code>m * 0 = 0</code>). Therefore, if <code>A_i</code> is 0, then <code>A_T</code> must also be 0. In this scenario, the transformation effectively collapses to <code>0 * x + B</code>, meaning the value becomes <code>B</code>. The code correctly handles this by returning <code>B_T</code> if <code>A_i</code> is 0, avoiding division by zero with <code>_mod_inv</code>.</li>
<li><strong>Negative Results</strong>: Modular arithmetic <code>(X % MOD)</code> in Python can yield negative results for negative <code>X</code>. The final <code>(final_val + self.MOD) % self.MOD</code> ensures the result is always in <code>[0, MOD-1]</code>.</li>
<li><strong><code>MOD</code> is Prime</strong>: Fermat's Little Theorem relies on <code>MOD</code> being prime. <code>10^9 + 7</code> is a prime number, so this is valid.</li>
<li><strong>Large Inputs</strong>: Since all operations are modular, numbers won't grow arbitrarily large, preventing overflow issues.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>For Extreme <code>getIndex</code> Performance</strong>: If <code>getIndex</code> were called extremely frequently compared to updates and <code>log MOD</code> was a bottleneck, one could consider storing the <code>A_T * A_i^-1</code> and <code>B_T - B_i * (A_T * A_i^-1)</code> terms directly in <code>self.transformations</code> to avoid repeated <code>_mod_inv</code> calls. However, this would mean <code>addAll</code>/<code>multAll</code> would have to recompute these "delta" transformations for <em>all</em> elements, which defeats the purpose of lazy propagation and would make updates O(N). The current approach is optimal for the problem's likely constraints (many <code>append</code>/updates, some <code>getIndex</code>).</li>
<li><strong>Readability</strong>:<ul>
<li>The comments are very helpful, especially for <code>transformations</code> and <code>getIndex</code>.</li>
<li>Explicitly noting that <code>transformations</code> has <code>N+1</code> entries for <code>N</code> elements could add clarity (e.g., <code>transformations[k]</code> corresponds to <code>sequence[k-1]</code>). The current indexing <code>transformations[idx]</code> for <code>sequence[idx]</code> implies <code>transformations</code> is 0-indexed for elements, which is fine since <code>transformations[0]</code> is a dummy.</li>
</ul>
</li>
<li><strong>Type Hinting</strong>: Adding type hints (<code>val: int</code>, <code>-&gt; None</code>, <code>-&gt; int</code>) would improve code clarity and maintainability, especially for a class with multiple methods.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: No direct security vulnerabilities found. The modular arithmetic correctly handles input ranges, preventing typical integer overflow-based exploits.</li>
<li><strong>Performance</strong>: The chosen approach is efficient. It achieves amortized O(1) for <code>append</code>, <code>addAll</code>, <code>multAll</code>, and O(log MOD) for <code>getIndex</code>. This is highly optimized for the problem constraints where global updates are common. The space complexity is linear, which is unavoidable as we need to store each appended value and its associated transformation context.</li>
</ul>


### Code:
```python
class Fancy(object):
    MOD = 10**9 + 7

    def __init__(self):
        # Stores the sequence of appended values
        self.sequence = []
        # Stores the global transformation (A * x + B) applied to the sequence *since the beginning*
        # T[0] = A (multiplier), T[1] = B (increment)
        # This list tracks the global transformation at the time each element was appended.
        self.transformations = [(1, 0)] 
        
    def _mod_inv(self, a):
        # Computes (a ^ (MOD - 2)) % MOD using Fermat's Little Theorem (a^-1 mod MOD)
        return pow(a, self.MOD - 2, self.MOD)

    def append(self, val):
        """
        :type val: int
        :rtype: None
        """
        # Append the initial value (which is val, modulo MOD)
        self.sequence.append(val % self.MOD)
        
        # Record the current cumulative transformation state (A, B)
        # The latest state is always at the end of the transformations list.
        self.transformations.append(self.transformations[-1])

    def addAll(self, inc):
        """
        :type inc: int
        :rtype: None
        """
        # Transformation: A * x + B
        # New transformation: A * x + (B + inc)
        A, B = self.transformations[-1]
        new_B = (B + inc) % self.MOD
        self.transformations[-1] = (A, new_B)

    def multAll(self, m):
        """
        :type m: int
        :rtype: None
        """
        # Transformation: A * x + B
        # New transformation: m * (A * x + B) = (m * A) * x + (m * B)
        m = m % self.MOD
        A, B = self.transformations[-1]
        
        new_A = (A * m) % self.MOD
        new_B = (B * m) % self.MOD
        self.transformations[-1] = (new_A, new_B)

    def getIndex(self, idx):
        """
        :type idx: int
        :rtype: int
        """
        if idx >= len(self.sequence):
            return -1

        # 1. Get the initial value of the element at index idx.
        v_initial = self.sequence[idx]
        
        # 2. Get the global transformation state (A_i, B_i) when this element was appended.
        # Note: We use idx+1 because self.transformations has a dummy entry at index 0.
        A_i, B_i = self.transformations[idx]
        
        # 3. Get the *current* global transformation state (A_T, B_T).
        A_T, B_T = self.transformations[-1]
        
        # The element's value is v_initial * (A_T/A_i) + (B_T - B_i * A_T/A_i).
        # We need the modular inverse of A_i (A_i^-1) for division.
        # If A_i is 0, the sequence was multiplied by 0 at some point, so A_i is 0.
        # In this case, the value must be B_i, but since A_i=0 is allowed, we must be careful.
        
        # Special case: A_i is 0. If A_i is 0, then v_initial is actually A_i * v_orig + B_i = B_i.
        # The value is simply B_T. 
        if A_i == 0:
            return B_T
            
        # Standard case: Use modular inverse
        A_i_inv = self._mod_inv(A_i)

        # Calculate the multiplier M = A_T / A_i
        M = (A_T * A_i_inv) % self.MOD
        
        # Calculate the increment I = B_T - B_i * (A_T / A_i)
        I = (B_T - (B_i * M) % self.MOD) % self.MOD
        
        # Final Value = v_initial * M + I
        final_val = (v_initial * M + I) % self.MOD
        
        # Ensure the result is non-negative
        return (final_val + self.MOD) % self.MOD
```
