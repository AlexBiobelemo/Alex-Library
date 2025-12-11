## Satisfiability of Equality Equarions
**Language:** python
**Tags:** python,oop,disjoint set union

### Description:
This code effectively solves the problem of determining if a given set of equality and inequality equations involving single-character variables ('a' through 'z') can all be satisfied simultaneously. It leverages the Disjoint Set Union (DSU) data structure, also known as Union-Find.

---

### 1. Overview & Intent

The primary goal of this code is to check the consistency of a list of equations. Each equation involves two single-letter variables (e.g., "a==b", "c!=d"). The core idea is to group variables that must be equal into "sets" and then verify that no two variables within the same set are also constrained to be unequal.

### 2. How It Works

The solution employs the Disjoint Set Union (DSU) data structure, which is ideal for managing elements grouped into a number of disjoint (non-overlapping) sets.

*   **Initialization:**
    *   A `parent` array of size 26 is created. Each index `i` (0-25) represents a variable ('a' through 'z'). Initially, `parent[i] = i`, meaning each variable is in its own distinct set.

*   **`find` Operation (with Path Compression):**
    *   This function determines the representative (root) of the set that an element `i` belongs to.
    *   If `parent[i]` is `i`, then `i` is the root of its set.
    *   Otherwise, it recursively calls `find` on `parent[i]` and then **path compresses** by setting `parent[i]` directly to the found root. This flattens the tree structure, making future `find` operations faster.

*   **`union` Operation:**
    *   This function merges the sets containing elements `i` and `j`.
    *   It finds the roots of `i` and `j` using the `find` function.
    *   If the roots are different, it means they are in different sets, so it merges them by setting one root's parent to the other root. (E.g., `parent[root_i] = root_j`).

*   **Two-Pass Processing:**
    *   **First Pass (Equalities `==`):** The code iterates through all equations. If an equation is `var1 == var2`, it calls `union(var1, var2)`. This process groups all variables that are transitively equal into the same set.
    *   **Second Pass (Inequalities `!=`):** After all equalities have been established, the code iterates through the equations again. If an equation is `var1 != var2`:
        *   It finds the roots of `var1` and `var2` using `find`.
        *   If `find(var1) == find(var2)`, it means `var1` and `var2` are in the same set (i.e., they are considered equal based on the first pass). This contradicts the `var1 != var2` constraint, so the function immediately returns `False`.

*   **Result:** If the second pass completes without finding any contradictions, it means all equations can be satisfied, and the function returns `True`.

### 3. Key Design Decisions

*   **Disjoint Set Union (DSU):**
    *   **Why DSU?** DSU is perfectly suited for problems involving partitioning elements into sets and efficiently checking if two elements are in the same set or merging sets.
    *   **Operations:** The `find` and `union` operations are core to DSU's efficiency.
*   **Path Compression:** This optimization within the `find` operation dramatically improves the performance of DSU by flattening the tree structure, reducing the depth of elements and speeding up subsequent `find` calls.
*   **Two-Pass Approach (Equalities then Inequalities):**
    *   This order is critical for correctness. All equality relationships (`==`) must be fully processed first to determine the final equivalence classes. Only then can inequalities (`!=`) be checked against these established equivalence classes to detect contradictions.
    *   Processing `!=` first could lead to incorrect results (e.g., if `a!=b` is processed, then `a==c` and `b==c` are processed, making `a` and `b` equal, the initial `a!=b` check would have been premature).
*   **Character to Integer Mapping:**
    *   `ord(char) - ord('a')` is used to convert characters 'a'-'z' into integer indices 0-25. This allows for direct indexing into the `parent` list, which is an efficient way to represent the DSU structure for a fixed, small alphabet.
*   **Fixed Size Array for `parent`:** Since the variables are strictly 'a' through 'z', a fixed-size list of 26 elements is sufficient and efficient, avoiding the overhead of dynamic data structures like hash maps for this specific constraint.

### 4. Complexity

Let `M` be the number of equations and `N` be the number of distinct variables (N=26 for 'a'-'z').

*   **Time Complexity:**
    *   **DSU Initialization:** `O(N)` for creating the `parent` array.
    *   **`find` and `union` Operations:** With path compression (and optionally union by rank/size, though not explicitly used here), the amortized time complexity for `k` operations is nearly constant, specifically `O(k * α(N))`, where `α` is the inverse Ackermann function, which grows extremely slowly and is practically a constant less than 5 for any realistic `N`.
    *   **Two Passes:** Each pass iterates `M` times. Each iteration involves string parsing (constant time for fixed-length strings) and a few `find` or `union` operations.
    *   **Overall:** `O(M * α(N))`. Since `N=26` is a very small constant, `α(N)` is also a constant. Therefore, the effective time complexity is `O(M)`.

*   **Space Complexity:**
    *   **`parent` array:** `O(N)` to store the parent pointers for `N` variables.
    *   **Recursion Stack for `find`:** In the worst case (a long chain before path compression), it could be `O(N)`. However, path compression generally keeps the recursion depth shallow, making it practically `O(log N)` or even `O(α(N))` amortized.
    *   **Overall:** `O(N)`. Given `N=26`, this is effectively `O(1)` (constant space).

### 5. Edge Cases & Correctness

*   **No equations:** The loops won't execute, and `True` will be returned. Correct, as there are no constraints to violate.
*   **Only `==` equations:** All `union` operations complete. The second pass checks for `!=` and finds none, returning `True`. Correct.
*   **Only `!=` equations:** The first pass does nothing. The second pass checks `!=`. If, for instance, `a!=b` is given, `find(a)` will not equal `find(b)` (since no `==` operations occurred), and `True` will be returned. Correct.
*   **Redundant equations:** E.g., `a==b`, `b==a`, `a==b`. DSU handles redundancy naturally; `union` operations on already-merged sets have no effect.
*   **Direct contradiction:** E.g., `a==b`, `a!=b`. The first pass merges `a` and `b`. The second pass finds `find(a) == find(b)` for `a!=b`, returning `False`. Correct.
*   **Transitive contradiction:** E.g., `a==b`, `b==c`, `a!=c`.
    1.  First pass: `union(a,b)` makes `a` and `b` roots the same. `union(b,c)` makes `b` and `c` roots the same, effectively merging `a`, `b`, and `c` into one set.
    2.  Second pass: For `a!=c`, `find(a)` will be equal to `find(c)`. Returns `False`. Correct.
*   **Variables not involved in any equations:** They remain in their own sets (e.g., `parent[i]=i`). This is correct as their status doesn't impact other variables or generate contradictions.

### 6. Improvements & Alternatives

*   **Union by Rank or Size:** While path compression alone gives excellent performance for DSU, combining it with "union by rank" or "union by size" (where the smaller tree is attached to the root of the larger tree) can theoretically further optimize the tree structure and ensure the amortized time complexity is truly `O(α(N))`. For `N=26`, the practical gain is minimal, but it's a standard DSU best practice.
    *   Example: Add a `rank` array, and in `union`, attach the root of the smaller rank tree to the root of the larger rank tree. Update ranks if ranks are equal.
*   **Error Handling/Input Validation:** The problem implicitly assumes valid input (e.g., equations are always 4 chars long, first and fourth are 'a'-'z', middle two are "==" or "!="). In a real-world scenario, robust input validation would be beneficial.
*   **Explicit Graph Representation:** One could model variables as nodes in a graph. Equal relationships (`==`) would be edges, and connected components would represent sets of equal variables. Then, for `!=` relationships, check if the two variables are in the same connected component. DSU is essentially an optimized way to manage this graph connectivity.

### 7. Security/Performance Notes

*   **Performance:** The DSU approach is highly efficient for this problem, offering nearly constant-time operations for `find` and `union` amortized. For the given constraints (26 variables), the solution is optimal in practice. The two-pass structure is essential for correctness but does not negatively impact the overall linear time complexity relative to the number of equations.
*   **Security:** This specific code snippet, being a pure algorithmic problem without external interactions (file I/O, network, user input beyond the `equations` list), does not have direct security implications. It's safe in its context.

### Code:
```python
class Solution:
    def equationsPossible(self, equations: List[str]) -> bool:
        # Initialize parent array for DSU, mapping 'a'->0, 'b'->1, ..., 'z'->25
        parent = list(range(26))

        # Find operation with path compression
        def find(i):
            if parent[i] == i:
                return i
            parent[i] = find(parent[i])
            return parent[i]

        # Union operation
        def union(i, j):
            root_i = find(i)
            root_j = find(j)
            if root_i != root_j:
                parent[root_i] = root_j

        # First pass: Process all equality equations ("==")
        for eq in equations:
            var1 = ord(eq[0]) - ord('a') # Convert char 'a'-'z' to int 0-25
            op = eq[1:3]
            var2 = ord(eq[3]) - ord('a')

            if op == "==":
                union(var1, var2)

        # Second pass: Process all inequality equations ("!=")
        for eq in equations:
            var1 = ord(eq[0]) - ord('a')
            op = eq[1:3]
            var2 = ord(eq[3]) - ord('a')

            if op == "!=":
                # If two variables are supposed to be unequal but are in the same set (equal),
                # then it's a contradiction, and the equations are not satisfiable.
                if find(var1) == find(var2):
                    return False

        # If all inequalities are consistent with the equalities, then it's possible.
        return True
```
