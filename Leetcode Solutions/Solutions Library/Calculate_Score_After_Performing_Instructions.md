## Calculate Score After Performing Instructions
**Language:** python
**Tags:** python,oop,set,simulation,cycle detection

### Description:
### 1. Overview & Intent

This code simulates the execution of a simple instruction set. Its primary goal is to calculate a final score by processing a sequence of instructions, while also detecting and preventing infinite loops in the instruction flow.

**Intent:**
*   Process a list of `instructions` (e.g., "add", "jump") and corresponding `values`.
*   Maintain a running `score`.
*   Navigate through instructions based on their type.
*   Terminate execution if an instruction is encountered twice (indicating a cycle/infinite loop) or if the instruction pointer moves out of bounds.

### 2. How It Works

The `calculateScore` method operates like a virtual machine for a basic instruction set:

1.  **Initialization:**
    *   `score` is set to `0`.
    *   `current_index` starts at `0`.
    *   A `visited` set is initialized to keep track of instruction indices that have already been executed.
2.  **Execution Loop:**
    *   A `while True` loop continuously executes instructions until a termination condition is met.
    *   **Termination Conditions:**
        *   If `current_index` goes below `0` or exceeds `n-1` (out of bounds), the loop breaks.
        *   If `current_index` is already in the `visited` set (meaning we're about to execute an instruction we've already run, indicating a cycle), the loop breaks.
    *   **Mark as Visited:** The `current_index` is added to the `visited` set to record its execution.
    *   **Instruction Execution:**
        *   If the instruction is `"add"`, the `values[current_index]` is added to `score`, and `current_index` increments by `1`.
        *   If the instruction is `"jump"`, `current_index` is updated by adding `values[current_index]` to it, effectively jumping to a new position.
3.  **Result:** Once the loop terminates, the final `score` is returned.

### 3. Key Design Decisions

*   **`visited` Set for Cycle Detection:** This is the most critical design choice. Using a `set` allows for efficient (average O(1)) checking if an instruction has been executed before and adding new indices. This prevents the simulation from entering an infinite loop.
*   **Parallel Lists (`instructions`, `values`):** The instruction type and its associated operand are stored in separate lists at corresponding indices. This is a common pattern for simple, fixed-structure data.
*   **`while True` with Multiple `break` Conditions:** This structure clearly delineates all possible exit points for the simulation loop (out of bounds, cycle detected).

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The `while` loop iterates at most `N` times, where `N` is the number of instructions. This is because each instruction index can be added to the `visited` set only once. Once an index is revisited, the loop terminates.
    *   Inside the loop, set operations (`add`, `in`) take O(1) on average. Arithmetic and list access are also O(1).
    *   Therefore, the overall time complexity is linear with respect to the number of instructions.
*   **Space Complexity: O(N)**
    *   The `visited` set can store up to `N` distinct instruction indices in the worst case (e.g., if all instructions are "add" and the program terminates naturally).
    *   Other variables (`score`, `current_index`, `n`) consume O(1) space.
    *   Thus, the overall space complexity is linear with respect to the number of instructions.

### 5. Edge Cases & Correctness

The code handles several edge cases correctly:

*   **Empty Instructions (`n=0`):** The loop condition `current_index >= n` (or `n=0`) will immediately cause a break if `current_index` is `0`, and the initial `score` of `0` will be returned, which is correct.
*   **Jumping Out of Bounds:** The checks `current_index < 0 or current_index >= n` explicitly handle jumps that would take the instruction pointer outside the valid range, terminating execution.
*   **Infinite Loops/Cycles:** The `visited` set correctly detects when the instruction pointer returns to a previously executed instruction, preventing an infinite loop and terminating the simulation.
*   **Negative/Zero `values`:**
    *   For "add", negative values correctly decrease the score.
    *   For "jump", negative values correctly move the instruction pointer backward, and zero values cause the pointer to stay put (which will quickly be detected as a cycle if not part of a larger loop).
*   **All "add" or All "jump" instructions:** The simulation correctly follows the flow, whether it's linear or involves complex jumps, until termination.

### 6. Improvements & Alternatives

*   **Robustness: Unknown Instruction Types:**
    *   Currently, if `instructions[current_index]` is neither "add" nor "jump", the `if/elif` block is skipped, and `current_index` is effectively incremented by `1` due to the lack of an `else` branch (this is a subtle behavior, as it *doesn't* increment explicitly but the loop continues, and it is assumed to be an instruction not defined in the problem context). It would be more robust to add an `else` block to handle unknown instruction types, either by raising an error or logging a warning:
        ```python
        else:
            # raise ValueError(f"Unknown instruction: {instructions[current_index]}")
            # print(f"Warning: Unknown instruction '{instructions[current_index]}' at index {current_index}. Skipping and advancing.")
            current_index += 1 # Or terminate
        ```
*   **Data Structure for Instructions:**
    *   Instead of two parallel lists, consider a list of tuples or custom objects for better encapsulation and type safety, especially as instruction types become more complex:
        ```python
        # Alternative: List of tuples
        # instructions_data = [("add", 10), ("jump", 2), ("add", 5)]
        # For execution: instruction_type, value = instructions_data[current_index]

        # Alternative: Custom Instruction object
        # class Instruction:
        #     def __init__(self, type, value):
        #         self.type = type
        #         self.value = value
        # instructions_obj = [Instruction("add", 10), ...]
        ```
        This improves readability and reduces the chance of index misalignment.
*   **Clarity: Enum for Instruction Types:**
    *   Using string literals ("add", "jump") can be prone to typos. An `Enum` could improve readability and maintainability:
        ```python
        from enum import Enum

        class InstructionType(Enum):
            ADD = "add"
            JUMP = "jump"

        # ...
        # if instructions[current_index] == InstructionType.ADD.value:
        # elif instructions[current_index] == InstructionType.JUMP.value:
        ```
*   **Loop Termination Condition Refinement:** While `while True` with breaks is fine, some might prefer a more explicit `while` condition if possible, though in this case, the `while True` is appropriate due to multiple distinct break conditions.

### 7. Security/Performance Notes

*   **Performance:** The O(N) time and space complexity is optimal for this problem, as in the worst case, every instruction might need to be visited and stored. There are no obvious performance bottlenecks that could be significantly improved without altering the problem's requirements.
*   **Security:** This isolated code snippet has no direct security implications. It processes internal data (`instructions`, `values`) and doesn't interact with external systems, user input, or sensitive resources. If `values` were to come from an untrusted source, the integer additions could lead to very large numbers, but Python's arbitrary-precision integers handle this gracefully, preventing overflow issues common in other languages.

### Code:
```python
class Solution:
    def calculateScore(self, instructions: List[str], values: List[int]) -> int:
        n = len(instructions)
        score = 0
        current_index = 0
        visited = set() # Stores indices of instructions already executed

        while True:
            # Check if current_index is out of bounds
            if current_index < 0 or current_index >= n:
                break

            # Check if current_index has been visited before
            if current_index in visited:
                break

            # Mark the current instruction as visited
            visited.add(current_index)

            # Execute the instruction based on its type
            if instructions[current_index] == "add":
                score += values[current_index] # Add value to score
                current_index += 1 # Move to the next instruction
            elif instructions[current_index] == "jump":
                current_index += values[current_index] # Jump to a new instruction index
        
        return score
```
