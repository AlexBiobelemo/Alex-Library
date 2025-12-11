## Boats to Save People
**Language:** python
**Tags:** python,oop,sorting,two-pointers

### Description:
This is a well-implemented solution to a classic greedy algorithm problem. Let's break it down.

---

### 1. Overview & Intent

*   **Problem**: The code aims to solve the "Rescue Boats" problem. Given a list of `people` (their weights) and a `limit` (maximum weight a boat can carry), determine the minimum number of boats required to transport all people. Each boat can carry at most two people.
*   **Goal**: Minimize boat usage by strategically pairing people.
*   **Intent**: The solution uses a greedy strategy combined with a two-pointer technique on a sorted list of people to efficiently find the minimum boat count.

---

### 2. How It Works

The algorithm proceeds in the following main steps:

1.  **Sort People**: The `people` list is first sorted in non-decreasing order. This is crucial because it allows us to easily identify the lightest and heaviest remaining people.
2.  **Initialize Pointers**: Two pointers, `left` and `right`, are initialized.
    *   `left` points to the lightest person (index 0).
    *   `right` points to the heaviest person (last index).
    *   A `boats` counter is initialized to 0.
3.  **Iterate and Pair**: The code enters a `while` loop that continues as long as `left` is less than or equal to `right` (meaning there are still people to process).
    *   **Increment Boat Count**: In each iteration, one boat is guaranteed to be used, so `boats` is incremented.
    *   **Attempt Pairing**: It checks if the lightest person (`people[left]`) and the heaviest person (`people[right]`) can fit into the same boat (`people[left] + people[right] <= limit`).
    *   **Successful Pair**: If they can fit, both `left` and `right` pointers move inward (`left += 1`, `right -= 1`), signifying that both people have boarded the current boat.
    *   **Unsuccessful Pair**: If they cannot fit together, it means the heaviest person (`people[right]`) is too heavy to be paired with the lightest person. In this greedy strategy, the heaviest person `people[right]` *must* take a boat, possibly alone (or with someone else, but trying to pair with `people[left]` is the most efficient option). Thus, `right` moves inward (`right -= 1`), while `left` stays put, waiting for a potentially lighter partner in the next iteration.
4.  **Return Result**: Once the `while` loop finishes (meaning all people have been assigned a boat), the total `boats` count is returned.

---

### 3. Key Design Decisions

*   **Greedy Approach**: The core strategy is greedy. By always trying to pair the lightest available person with the heaviest available person, the algorithm maximizes the chances of fitting two people per boat. If the heaviest person cannot fit with the lightest, they are sent alone (or with some other heavier person, but that would be less efficient than with the lightest), ensuring progress while using a single boat for that heaviest person.
*   **Sorting**: Sorting the `people` array is fundamental. It enables the two-pointer technique to reliably identify the current lightest (`left`) and heaviest (`right`) individuals, which is critical for the greedy strategy's correctness.
*   **Two-Pointer Technique**: This is an efficient way to traverse a sorted array from both ends simultaneously. It's perfectly suited here because we are concerned with interactions between the smallest and largest elements.

---

### 4. Complexity

*   **Time Complexity**:
    *   `people.sort()`: O(N log N), where N is the number of people. Python's Timsort is used.
    *   `while` loop: The `left` pointer moves from 0 up to `N/2` and the `right` pointer moves from `N-1` down to `N/2`. In each iteration, at least one pointer (the `right` pointer always, sometimes both) moves inward. Therefore, the loop runs at most N times. Each operation inside the loop is O(1).
    *   **Total Time**: O(N log N), dominated by the sorting step.
*   **Space Complexity**:
    *   `people.sort()`: Python's Timsort generally uses O(N) auxiliary space in the worst case for temporary storage during sorting.
    *   Pointers (`left`, `right`) and `boats` variable: O(1) auxiliary space.
    *   **Total Space**: O(N) due to the sorting algorithm's auxiliary space requirements.

---

### 5. Edge Cases & Correctness

The algorithm robustly handles various edge cases:

*   **Empty `people` list**:
    *   `len(people)` is 0. `right` becomes -1. The `while left <= right` condition (0 <= -1) is immediately false. `boats` (0) is returned. **Correct.**
*   **Single person**: `people = [70]`, `limit = 100`
    *   `left = 0`, `right = 0`. Loop runs once. `boats` becomes 1. `70 + 70 <= 100` is false. `right` becomes -1. Loop terminates. `boats` (1) is returned. **Correct.**
*   **All people can share boats (e.g., all light)**: `people = [10, 20, 30, 40]`, `limit = 70`
    *   Sorted: `[10, 20, 30, 40]`.
    *   `10+40 <= 70` (True) -> (10,40) paired. `boats=1`.
    *   `20+30 <= 70` (True) -> (20,30) paired. `boats=2`.
    *   Result: 2 boats. **Correct.**
*   **All people must take individual boats (e.g., all heavy)**: `people = [70, 80, 90]`, `limit = 100`
    *   Sorted: `[70, 80, 90]`.
    *   `70+90 <= 100` (False) -> 90 takes a boat. `boats=1`.
    *   `70+80 <= 100` (False) -> 80 takes a boat. `boats=2`.
    *   `70` (last person) -> 70 takes a boat. `boats=3`.
    *   Result: 3 boats. **Correct.**
*   **Person's weight exceeds limit**: The problem statement typically guarantees `people[i] <= limit`. If not, a person whose individual weight exceeds `limit` would make the problem unsolvable (or require a specific error handling not covered by this logic). Assuming valid inputs, this is not an issue.

The greedy strategy works because by sorting, we ensure that the heaviest person always attempts to pair optimally. If they can pair with the lightest, it's the best use of boat capacity. If they can't, they are sent off, ensuring we don't block other pairings with a person who *must* use a boat anyway.

---

### 6. Improvements & Alternatives

*   **Readability/Comments**: The code is already quite clean and concise. For an educator, adding a brief comment explaining the greedy choice (why `people[left] + people[right]` is checked) could further enhance understanding for beginners, but it's not strictly necessary for experienced developers.
*   **Alternative Algorithms**:
    *   **Dynamic Programming**: A DP approach might be conceivable but would be significantly more complex and have a much worse time complexity (e.g., O(N * limit)) making it impractical for typical constraints where `limit` can be large.
    *   For this problem, the greedy two-pointer approach is widely considered the most optimal and efficient solution. There isn't a significantly better alternative in terms of asymptotic complexity.

---

### 7. Security/Performance Notes

*   **Performance**: The O(N log N) time complexity is generally optimal for problems involving sorting, and the O(N) space complexity is acceptable. For typical competitive programming or real-world constraints (N up to 10^5 or 10^6), this solution performs very well.
*   **Security**: This code processes numerical input internally without external interactions (network, file system, user input validation beyond type hints), thus it does not introduce any specific security vulnerabilities. It's a pure algorithmic problem.

### Code:
```python
class Solution:
    def numRescueBoats(self, people: List[int], limit: int) -> int:
        # Sort people by weight in ascending order
        people.sort()
        
        left = 0  # Pointer for the lightest person
        right = len(people) - 1  # Pointer for the heaviest person
        boats = 0  # Counter for the number of boats required
        
        # Continue as long as there are people left to rescue
        while left <= right:
            boats += 1  # A new boat is always launched
            
            # If the lightest and heaviest person can share a boat
            if people[left] + people[right] <= limit:
                left += 1  # The lightest person is rescued, move to the next lightest
            
            right -= 1  # The heaviest person is always rescued (either alone or with the lightest)
            
        return boats
```
