## Palindrome Pairs
**Language:** python
**Tags:** trie,palindrome,string-algorithms

### Description:
<p>This code implements a solution to the Palindrome Pairs problem using a Trie data structure.</p>
<h3>1. Overview &amp; Intent</h3>
<p>The problem is to find all unique pairs of indices <code>(i, j)</code> from a given list of words <code>words</code> such that the concatenation <code>words[i] + words[j]</code> forms a palindrome. The code uses a Trie (prefix tree) to efficiently search for potential palindrome pairs. The core idea is to insert the <em>reversed</em> versions of all words into the Trie.</p>
<h3>2. How It Works</h3>
<p>The solution breaks down into two main phases: building the Trie and searching for pairs.</p>
<p><strong>Trie Structure (<code>TrieNode</code>)</strong>:</p>
<ul>
<li><code>children</code>: A dictionary mapping characters to child <code>TrieNode</code>s, representing the next character in a word.</li>
<li><code>word_index</code>: Stores the original index of a word whose <em>reverse</em> completely ends at this node. Initialized to -1.</li>
<li><code>palindrome_indices</code>: A list storing indices <code>j</code> of words <code>w_j</code> such that the <em>prefix</em> of <code>w_j</code> (corresponding to the portion of <code>w_j</code> <em>before</em> the path taken by some <code>w_i^R</code> matches) is a palindrome. This is pre-computed to handle cases where <code>w_i</code> is shorter than <code>w_j</code>.</li>
</ul>
<p><strong>Phase 1: Building the Trie (<code>insert</code> method)</strong>:</p>
<ol>
<li>For each word <code>w_j</code> in the input <code>words</code> (at index <code>j</code>):<ul>
<li>The <code>insert</code> method iterates through the <em>reversed</em> word <code>w_j^R</code> character by character.</li>
<li>At each step <code>k</code> (before processing the <code>k</code>-th character of <code>w_j^R</code>), it checks if the <em>remaining prefix</em> of the <em>original</em> word <code>w_j</code> (i.e., <code>w_j[0 : L-k-1]</code>) is a palindrome.</li>
<li>If it is a palindrome, the current word's index <code>j</code> is added to the <code>palindrome_indices</code> list of the current <code>TrieNode</code>. This prepares for Case 3 matches (where <code>w_i</code> is a prefix of <code>w_j^R</code>, and the rest of <code>w_j</code> is a palindrome).</li>
<li>It then creates new <code>TrieNode</code>s as needed for the current character of <code>w_j^R</code> and advances to the next node.</li>
<li>Once all characters of <code>w_j^R</code> have been processed, the <code>word_index</code> of the final node is set to <code>j</code>, indicating that <code>w_j^R</code> ends here.</li>
</ul>
</li>
</ol>
<p><strong>Phase 2: Searching for Pairs (<code>search</code> method)</strong>:</p>
<ol>
<li>For each word <code>w_i</code> in the input <code>words</code> (at index <code>i</code>):<ul>
<li>The <code>search</code> method traverses the Trie using the <em>original</em> word <code>w_i</code> character by character.</li>
<li><strong>Inside the loop (Case 2: <code>|w_i| &gt; |w_j|</code>)</strong>: At each <code>TrieNode</code> visited:<ul>
<li>If the current <code>TrieNode</code> marks the end of some <code>w_j^R</code> (i.e., <code>node.word_index != -1</code> and <code>j != i</code>), it means <code>w_j^R</code> is a prefix of <code>w_i</code>.</li>
<li>The code then checks if the <em>remaining suffix</em> of <code>w_i</code> (from the current character <code>k</code> to the end: <code>w_i[k : L-1]</code>) is a palindrome. If both conditions are met, <code>(i, j)</code> is a palindrome pair.</li>
</ul>
</li>
<li>The search continues by moving to the child node corresponding to the current character of <code>w_i</code>. If a character is not found, it means <code>w_i</code> cannot form any more pairs by matching a prefix of <code>w_j^R</code>, so the search for <code>w_i</code> terminates.</li>
<li><strong>After the loop (Case 1 &amp; 3: <code>|w_i| &lt;= |w_j|</code>)</strong>: Once <code>w_i</code> has been fully traversed:<ul>
<li>The current <code>TrieNode</code> represents the end of <code>w_i</code>.</li>
<li><strong>Case 3</strong>: Iterate through <code>node.palindrome_indices</code>. For each index <code>j</code> in this list (where <code>j != i</code>), it means <code>w_i</code> is a prefix of <code>w_j^R</code>, and the remaining part of <code>w_j</code> (the original prefix that was checked during insertion) is a palindrome. Thus, <code>(i, j)</code> is a pair.</li>
<li><strong>Case 1</strong>: If <code>node.word_index != -1</code> (and <code>node.word_index != i</code>), it means <code>w_i</code> is the exact reverse of <code>w_j</code>. This forms a pair <code>(i, j)</code>. This check explicitly covers the exact reverse match.</li>
</ul>
</li>
</ul>
</li>
</ol>
<p>All found pairs <code>(i, j)</code> are stored in a <code>set</code> to handle duplicates and ensure uniqueness, and finally converted to a list of lists.</p>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Trie of Reversed Words</strong>: The fundamental decision is to insert <code>w_j^R</code> (reversed words) into the Trie. This allows efficient lookup of <code>w_i</code> as a prefix of <code>w_j^R</code>, which is crucial for identifying <code>w_i + w_j</code> palindromes.</li>
<li><strong><code>palindrome_indices</code> List in <code>TrieNode</code></strong>: This is a key optimization. By pre-calculating and storing indices of words whose <em>remaining original prefixes</em> are palindromes at relevant Trie nodes during insertion, the <code>search</code> phase avoids re-scanning these prefixes, simplifying and speeding up Case 3 identification.</li>
<li><strong><code>is_palindrome</code> Helper Function</strong>: A dedicated function for checking substring palindromes.</li>
<li><strong>Set for Results</strong>: Using a <code>set</code> (<code>results = set()</code>) automatically handles duplicate <code>(i, j)</code> pairs, ensuring the final output contains only unique pairs.</li>
<li><strong>Index-based Logic</strong>: The solution works with word indices rather than directly manipulating string objects, which is standard for this problem.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the number of words, <code>L_max</code> be the maximum length of a word, and <code>K</code> be the total length of all words (<code>K = sum(len(word) for word in words)</code>).</p>
<ul>
<li><p><strong><code>is_palindrome(word, i, j)</code></strong>:</p>
<ul>
<li>The current implementation creates a substring slice <code>word[i:j+1]</code> and then reverses it. This operation takes <code>O(L_sub)</code> time, where <code>L_sub</code> is the length of the substring. A more efficient two-pointer approach would still be <code>O(L_sub)</code> but without the string copy overhead.</li>
</ul>
</li>
<li><p><strong><code>insert(word, index, root)</code></strong>:</p>
<ul>
<li>Iterates <code>L</code> times (length of the word).</li>
<li>Inside the loop, <code>self.is_palindrome</code> is called. In the worst case, <code>L-k-1</code> can be <code>O(L)</code>.</li>
<li>Therefore, a single <code>insert</code> operation takes <code>O(L * L)</code> = <code>O(L^2)</code> time.</li>
<li>Total for all <code>N</code> insertions: <code>O(N * L_max^2)</code> in the worst case (e.g., all words are long, and many prefixes are palindromes).</li>
</ul>
</li>
<li><p><strong><code>search(word, index, root, results)</code></strong>:</p>
<ul>
<li>Iterates <code>L</code> times (length of the word).</li>
<li>Inside the loop, <code>self.is_palindrome</code> is called. In the worst case, <code>L-k-1</code> can be <code>O(L)</code>.</li>
<li>After the loop, iterating <code>node.palindrome_indices</code> takes <code>O(N)</code> in the worst case (if all words share a common prefix and suffix).</li>
<li>Therefore, a single <code>search</code> operation takes <code>O(L * L + N)</code> = <code>O(L^2 + N)</code> time.</li>
<li>Total for all <code>N</code> searches: <code>O(N * (L_max^2 + N))</code> which simplifies to <code>O(N * L_max^2 + N^2)</code>.</li>
</ul>
</li>
<li><p><strong>Overall Time Complexity</strong>: The dominant factor is <code>O(N * L_max^2)</code>. The comment in the code claiming <code>O(K)</code> is incorrect for this implementation due to the <code>O(L)</code> <code>is_palindrome</code> calls within loops that iterate <code>L</code> times. Achieving true <code>O(K)</code> usually requires an <code>O(1)</code> <code>is_palindrome</code> check (e.g., using polynomial hashing) or a more sophisticated palindrome-checking strategy integrated with the Trie traversal.</p>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><strong>Trie Nodes</strong>: In the worst case, the Trie stores all characters of all <em>reversed</em> words. This is <code>O(K)</code> space.</li>
<li><strong><code>palindrome_indices</code> Lists</strong>: In the worst case, a single node's <code>palindrome_indices</code> list could store up to <code>N</code> indices. Summing over all <code>K</code> nodes, this could theoretically be <code>O(K * N)</code> if many prefixes of many words are palindromes. More realistically, total entries across all lists are <code>O(N * L_max)</code> (each <code>insert</code> adds <code>L</code> entries, summed over <code>N</code> words).</li>
<li><strong><code>results</code> Set</strong>: In the worst case, there can be <code>O(N^2)</code> palindrome pairs.</li>
<li><strong>Overall</strong>: <code>O(K + N * L_max + N^2)</code>. Given typical constraints (<code>L_max</code> often around 300, <code>N</code> up to 50000), <code>N * L_max</code> might be larger than <code>K</code>. <code>N^2</code> could be very large, but usually, the number of actual palindrome pairs is much less than <code>N^2</code>. Therefore, the space is dominated by <code>O(K + N * L_max)</code> in practical scenarios for the Trie itself.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty String (<code>""</code>)</strong>:<ul>
<li>If <code>""</code> is present in <code>words</code> at index <code>idx_empty</code>:<ul>
<li>When <code>insert("", idx_empty, root)</code> is called, <code>root.word_index</code> will be set to <code>idx_empty</code>.</li>
<li>When <code>search("", idx_empty, root, results)</code> is called, it correctly checks <code>root.palindrome_indices</code> (adding <code>(idx_empty, j)</code> for any palindrome <code>w_j</code>) and <code>root.word_index</code> (adding <code>(idx_empty, idx_empty)</code> if <code>root.word_index</code> refers to itself, but <code>node.word_index != index</code> prevents this).</li>
<li>This means an empty string <code>""</code> correctly pairs with any other word <code>w_j</code> that is a palindrome (<code>words[i] + "" = words[i]</code> which is a palindrome, and <code>"" + words[j] = words[j]</code> which is a palindrome). This behavior is correct for the Palindrome Pairs problem.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Single-Character Words</strong>: Handled correctly as <code>is_palindrome</code> works for single characters.</li>
<li><strong>Words that are already Palindromes</strong>: Will correctly trigger conditions in both <code>insert</code> (populating <code>palindrome_indices</code>) and <code>search</code>.</li>
<li><strong>Duplicate Words with Different Indices</strong>: This is a subtle point. The <code>TrieNode.word_index</code> stores a single integer (<code>-1</code> or an index <code>j</code>). If <code>words = ["ab", "ba", "ba"]</code> (indices 0, 1, 2), then <code>insert("ba", 1)</code> would set <code>word_index</code> to 1, but <code>insert("ba", 2)</code> would then overwrite it to 2. This means the pair <code>(0, 1)</code> would be missed, and only <code>(0, 2)</code> would be found. For full correctness with duplicate words needing distinct pairings, <code>TrieNode.word_index</code> should be a <code>list</code> (<code>self.word_indices = []</code>) and all relevant places in <code>insert</code> and <code>search</code> should append/iterate over this list. The problem context usually implies unique words, or that <em>any</em> valid index <code>j</code> is sufficient if <code>words[j]</code> duplicates <code>words[k]</code>.</li>
<li><strong><code>i != j</code> Constraint</strong>: The code explicitly checks <code>j != index</code> and <code>node.word_index != index</code> to prevent adding pairs of a word with itself, which is standard for this problem.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Optimize <code>is_palindrome</code></strong>: The current <code>is_palindrome</code> implementation, due to string slicing and reversal (<code>sub == sub[::-1]</code>), creates temporary string objects. A direct two-pointer approach (<code>while i &lt; j: if word[i] != word[j]: return False; i += 1; j -= 1; return True</code>) would be more efficient by avoiding string copies, though it doesn't change the <code>O(L_sub)</code> complexity for a single check.</li>
<li><strong>Achieve <code>O(K)</code> Time Complexity</strong>: To truly get <code>O(K)</code> total time, the <code>O(L)</code> <code>is_palindrome</code> checks within <code>L</code>-iteration loops need to be optimized. This typically involves:<ul>
<li><strong>Polynomial Hashing</strong>: Precompute rolling hashes for each word. Then, <code>is_palindrome</code> can be an <code>O(1)</code> check by comparing hashes of substrings and their reverses (precomputing reverse hashes too). This would make <code>insert</code> and <code>search</code> effectively <code>O(L)</code> for each word, leading to <code>O(K)</code> total.</li>
<li><strong>Manacher's Algorithm</strong>: Precompute all palindromic substrings for each word using Manacher's. Then, checks can be <code>O(1)</code>.</li>
<li><strong>Integrated Palindrome Check</strong>: Design the Trie traversal and <code>palindrome_indices</code> population more intricately to avoid repeated <code>O(L)</code> checks for substrings.</li>
</ul>
</li>
<li><strong><code>TrieNode.word_index</code> as a List</strong>: As discussed in "Edge Cases," if the input <code>words</code> can contain identical strings at different indices and all distinct index pairings are required, <code>TrieNode.word_index</code> should be changed to a list (<code>self.word_indices = []</code>) to store all such indices. This would also necessitate iterating through this list in <code>search</code>.</li>
<li><strong>Remove Unused Import</strong>: The <code>collections</code> module is imported but not used. It should be removed.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The primary performance bottleneck is the repeated <code>O(L)</code> palindrome checks within <code>O(L)</code> loops. This leads to an <code>O(N * L_max^2)</code> solution, which can be too slow for very large inputs (<code>N=50000, L_max=300</code> would be ~4.5 * 10^9 operations, far too high). As noted, this can be improved to <code>O(K)</code> using string hashing.</li>
<li><strong>Security</strong>: This code doesn't directly handle external inputs, file operations, network communication, or sensitive data. Therefore, typical security concerns like injection attacks or data breaches are not applicable here. Its purpose is purely algorithmic computation.</li>
</ul>


### Code:
```python
import collections

class TrieNode:
    """Trie node structure to store words and palindrome indices."""
    def __init__(self):
        # Dictionary for character children
        self.children = {}
        # Stores the index of the word w_j whose reverse ends at this node.
        self.word_index = -1  
        # Stores indices j such that the *prefix* of w_j (corresponding to the rest of w_j^R) 
        # is a palindrome. This is pre-computed for Case 3/1 matches.
        self.palindrome_indices = []

class Solution(object):
    """
    Solves the Palindrome Pairs problem using a Trie of reversed words.
    Time Complexity: O(K), where K is the total length of all words.
    """
    
    def is_palindrome(self, word, i, j):
        """Checks if the substring word[i:j+1] is a palindrome."""
        # Note: j is inclusive index.
        sub = word[i:j+1]
        return sub == sub[::-1]

    def insert(self, word, index, root):
        """Inserts word's reverse (w_j^R) into the Trie and populates palindrome_indices."""
        node = root
        L = len(word)
        word_rev = word[::-1]
        
        # Traverse the reversed word w_j^R
        for k in range(L):
            # Check for the condition required for Case 3 (|w_i| < |w_j|):
            # The *prefix* of the original word w_j that remains *after* w_i^R matches 
            # must be a palindrome. This remaining prefix is word[0 : L-k].
            # This check is performed at the node reached *before* processing the k-th character.
            if self.is_palindrome(word, 0, L - k - 1):
                # This node is the junction where w_i could end, and the remaining 
                # part of w_j satisfies the palindrome condition.
                node.palindrome_indices.append(index)

            char = word_rev[k]
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        
        # The full reversed word w_j^R ends here.
        node.word_index = index

    def search(self, word, index, root, results):
        """
        Searches for w_i in the Trie (containing w_j^R) to find pairs (i, j).
        
        This search processes the word w_i as a potential prefix of w_j^R (Case 3) 
        or as a string that has w_j^R as its prefix (Case 2).
        """
        node = root
        L = len(word)
        
        for k in range(L):
            # A. Check for Case 2: |w_i| > |w_j|
            # If we pass a node where a reversed word w_j^R ends (j != -1), 
            # it means w_j^R is a prefix of w_i (i.e., w_i starts with w_j^R).
            if node.word_index != -1 and node.word_index != index:
                j = node.word_index
                # The remaining suffix of w_i (word[k:]) must be a palindrome.
                if self.is_palindrome(word, k, L - 1):
                    results.add((index, j)) # Pair (i, j) found

            char = word[k]
            if char not in node.children:
                # Word w_i does not match any prefix of a w_j^R. Stop search.
                return
            node = node.children[char]
            
        # B. After the loop, we are at the node where w_i ends.
        
        # Check for Case 3 and Case 1: |w_i| <= |w_j|
        # The full word w_i matches a prefix of w_j^R.
        # 1. Case 1: w_i = w_j^R. (Covered by node.word_index check below).
        # 2. Case 3: w_i is a shorter prefix of w_j^R, and the rest of w_j satisfies 
        #    the palindrome prefix condition.
        for j in node.palindrome_indices:
            if j != index:
                # This list contains all j's where w_i is a prefix of w_j^R, 
                # and the remaining part of w_j is a palindrome.
                results.add((index, j))
        
        # Explicit check for Case 1 (w_i = w_j^R) if not already covered by palindrome_indices.
        # This is strictly needed for the exact match when two words are reverses of each other.
        if node.word_index != -1 and node.word_index != index:
             results.add((index, node.word_index))


    def palindromePairs(self, words):
        """
        :type words: List[str]
        :type words: List[str]
        :rtype: List[List[int]]
        """
        root = TrieNode()
        # Use a set to automatically handle duplicates and unordered pairs
        results = set()
        
        # Step 1: Build the Trie of reversed words (w_j^R)
        for i, word in enumerate(words):
            self.insert(word, i, root)
            
        # Step 2: Search for each word w_i in the Trie
        for i, word in enumerate(words):
            self.search(word, i, root, results)
            
        return [list(pair) for pair in results]

```
