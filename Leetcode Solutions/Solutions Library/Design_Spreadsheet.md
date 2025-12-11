## Design Spreadsheet
**Language:** python
**Tags:** python,oop,2d-array,string-parsing

### Description:
This Python code defines a `Spreadsheet` class that simulates a very basic spreadsheet. It allows setting and resetting integer values in cells, and evaluating simple formulas that sum two operands.

### 1. Overview & Intent

The `Spreadsheet` class is designed to represent a grid of cells, similar to a simplified Excel or Google Sheets. Its core functionalities include:

*   **Initialization**: Creating a spreadsheet with a specified number of rows and a fixed set of 26 columns (A-Z).
*   **Cell Manipulation**: Setting and resetting integer values in individual cells using string references (e.g., "A1", "B10").
*   **Formula Evaluation**: Calculating the sum of two operands, where operands can be either direct integer values or references to other cells.

The primary intent is to provide a fundamental model for cell storage and basic arithmetic evaluation within a spreadsheet context.

### 2. How It Works

1.  **Initialization (`__init__`)**:
    *   Takes `rows` as input.
    *   Initializes `self.grid` as a 2D list (list of lists) with `self.rows` rows and 26 columns.
    *   All cells are initialized to `0`.

2.  **Cell Reference Parsing (`_parse_cell_ref`)**:
    *   A helper method that takes a cell string (e.g., "A1", "Z10").
    *   Parses the first character to determine the 0-indexed column (e.g., 'A' -> 0, 'B' -> 1).
    *   Parses the rest of the string as the row number, converting it to a 0-indexed row (e.g., '1' -> 0, '10' -> 9).
    *   Returns a `(row_idx, col_idx)` tuple.

3.  **Setting Cell Values (`setCell`)**:
    *   Uses `_parse_cell_ref` to get the 0-indexed coordinates from the `cell` string.
    *   Assigns the given `value` to the corresponding cell in `self.grid`.

4.  **Resetting Cell Values (`resetCell`)**:
    *   Similar to `setCell`, but always sets the cell's value back to `0`.

5.  **Getting Value/Evaluating Formula (`getValue`)**:
    *   Assumes the `formula` string starts with `=` (which is stripped) and is in the format `X+Y` (e.g., "=A1+B2", "=10+C3", "=5+10").
    *   Splits the formula into two `operand` strings based on the `'+'` character.
    *   Defines an inner helper function `_get_operand_value` to determine the value of a single operand:
        *   If the operand starts with an alphabet character (e.g., "A1"), it's treated as a cell reference. `_parse_cell_ref` is used, and the value is fetched from `self.grid`. Crucially, if the cell reference is *out of bounds*, its value is treated as `0`.
        *   Otherwise, it's treated as an integer literal and converted using `int()`.
    *   Calls `_get_operand_value` for both `operand1_str` and `operand2_str`.
    *   Sums their results and returns the `total_sum`.

### 3. Key Design Decisions

*   **Grid Data Structure**: A `list[list[int]]` is used for `self.grid`. This is a straightforward and efficient choice for a dense, fixed-size 2D grid where direct indexing is common.
*   **Column Representation**: Fixed 26 columns (A-Z) simplifies column indexing using `ord(char) - ord('A')`.
*   **Row Indexing**: External cell references (e.g., "A1") use 1-based indexing for rows, while internal storage uses 0-based indexing (`int(row_num_str) - 1`).
*   **Formula Scope**: Formulas are limited to a simple binary addition (`X+Y`) with no support for other operations, multiple operands, or parentheses. This keeps the parsing logic very simple.
*   **Out-of-Bounds Cell Handling in Formulas**: A specific decision is made to treat out-of-bounds cell references within a formula as having a value of `0`. This provides robustness for formula evaluation.
*   **Helper Method for Parsing**: `_parse_cell_ref` centralizes the logic for converting cell reference strings, promoting reusability and reducing duplication.
*   **Nested Helper for Operand Value**: `_get_operand_value` within `getValue` encapsulates the logic for resolving an operand, making the `getValue` method cleaner.

### 4. Complexity

*   **Time Complexity**:
    *   `__init__(self, rows)`: `O(rows)` to initialize the 2D grid.
    *   `_parse_cell_ref(self, cell_str)`: `O(1)` – String operations (indexing, slicing, `int` conversion) are constant time for typical, short cell reference strings (e.g., "A1", "Z99").
    *   `setCell(self, cell, value)`: `O(1)` – Dominated by `_parse_cell_ref` and direct grid access.
    *   `resetCell(self, cell)`: `O(1)` – Same as `setCell`.
    *   `getValue(self, formula)`: `O(1)` – Formula parsing (string slicing and splitting for `"=X+Y"`) is constant time for a fixed, small number of parts. `_get_operand_value` calls are also `O(1)`.

*   **Space Complexity**:
    *   `__init__(self, rows)`: `O(rows * 26)` = `O(rows)` to store the grid.
    *   All other methods: `O(1)` auxiliary space, as they only use a few local variables and string slices/copies of fixed small size.

### 5. Edge Cases & Correctness

*   **Invalid Cell References (`setCell`, `resetCell`)**:
    *   **"A0" (row 0)**: `row_idx` becomes -1, causing `IndexError` on `self.grid[-1]`.
    *   **"A" (missing row number)**: `row_num_str` is empty, `int('')` raises `ValueError`.
    *   **"1A" (invalid format)**: `col_char` is '1', `ord('1') - ord('A')` is an invalid column index, causing `IndexError` on `self.grid[row_idx][col_idx]`.
    *   **"AA1" (multi-char column)**: `col_char` is 'A', `col_idx` is 0, so it's treated as "A1".
    *   **Cell references beyond `self.rows` or `Z` (e.g., "A100" if `rows=10`, "BA1")**: `setCell`/`resetCell` will raise an `IndexError`.

*   **Invalid Formulas (`getValue`)**:
    *   **Missing '='**: `formula[1:]` would be the whole string, `split('+')` might still work but the interpretation is wrong.
    *   **No `+`**: `parts` will have only one element, `parts[1]` will raise `IndexError`.
    *   **Invalid operand (e.g., "A!" or "hello")**: `_get_operand_value` will attempt to call `_parse_cell_ref` or `int()`, likely resulting in `IndexError` (for `cell_str[1:]` if only one char) or `ValueError`.
    *   **Valid but out-of-bounds cell in formula (e.g., "=A100+B1" where `rows=10`)**: Handled correctly by `_get_operand_value` which returns `0` for such cell references. This makes formula evaluation robust against referring to non-existent cells.
    *   **Negative `rows` during initialization**: `range(self.rows)` would result in an empty grid, and any subsequent `setCell` or `resetCell` call would raise an `IndexError`.

### 6. Improvements & Alternatives

1.  **Robust Error Handling**:
    *   Instead of letting `IndexError` or `ValueError` propagate, validate inputs for `cell` references and `formula` structure explicitly.
    *   Raise custom exceptions (e.g., `InvalidCellReferenceError`, `InvalidFormulaError`) to provide clearer feedback to users of the class.
    *   Use regular expressions in `_parse_cell_ref` to strictly match valid cell patterns (e.g., `^[A-Z][1-9]\d*$`).

2.  **Extended Formula Parsing**:
    *   **More Operations**: Support `-`, `*`, `/`.
    *   **Multiple Operands**: Allow `A1+B2+C3`.
    *   **Parentheses/Operator Precedence**: This would require a more sophisticated parsing algorithm (e.g., Shunting-yard algorithm to convert to RPN, then evaluate).
    *   **Functions**: `SUM(A1:A5)`, `AVERAGE(B1,B2)`.
    *   **Cell Ranges**: Implement parsing for `A1:A5` syntax.

3.  **Scalability for Columns**:
    *   The current design is limited to 26 columns (A-Z). To support "AA", "AB", etc., `_parse_cell_ref` would need to be updated to handle multi-character column names using a base-26 conversion.
    *   For very sparse spreadsheets or a dynamic number of columns, consider a `dict` where keys are `(row_idx, col_idx)` tuples, instead of a 2D list. This saves memory if most cells are empty.

4.  **`_parse_cell_ref` Validation**: Add checks for `row_idx` being non-negative before returning. Currently, "A0" results in `row_idx = -1`, which is then used in `self.grid[-1]`, potentially leading to confusion if not caught by bounds checks.

5.  **Readability/Clarity**:
    *   Add comments to clarify the assumptions made in `getValue` (e.g., formula format, default value for out-of-bounds cells).
    *   Consider making `_get_operand_value` a standalone helper or static method if it didn't strictly need `self` to access `_parse_cell_ref`.

### 7. Security/Performance Notes

*   **Security**: No direct security vulnerabilities are apparent. The code deals with internal integer data. Input validation (as suggested above) would prevent potential Denial-of-Service if malformed strings cause unhandled exceptions and crashes.
*   **Performance**:
    *   For the current limited scope, the performance is excellent, with most operations being O(1).
    *   Memory usage scales linearly with `rows` (`O(rows * 26)`). For extremely large numbers of rows with sparse data, a dictionary-based approach (`dict[tuple[int, int], int]`) might be more memory-efficient by only storing non-zero cells.
    *   The string parsing is efficient due to the fixed, small length of cell references and formula parts. Extending formulas significantly would add parsing overhead, but for realistic spreadsheet usage, even complex parsing is usually fast enough.

### Code:
```python
class Spreadsheet:

    def __init__(self, rows: int):
        self.rows = rows
        self.grid = [[0] * 26 for _ in range(self.rows)]

    def _parse_cell_ref(self, cell_str: str) -> tuple[int, int]:
        """Helper to parse a cell reference string like 'A1' into 0-indexed (row, col)."""
        col_char = cell_str[0]
        col_idx = ord(col_char) - ord('A')

        row_num_str = cell_str[1:]
        row_idx = int(row_num_str) - 1

        return row_idx, col_idx

    def setCell(self, cell: str, value: int) -> None:
        row_idx, col_idx = self._parse_cell_ref(cell)
        self.grid[row_idx][col_idx] = value

    def resetCell(self, cell: str) -> None:
        row_idx, col_idx = self._parse_cell_ref(cell)
        self.grid[row_idx][col_idx] = 0

    def getValue(self, formula: str) -> int:
        parts = formula[1:].split('+')
        operand1_str = parts[0]
        operand2_str = parts[1]

        total_sum = 0

        def _get_operand_value(operand_str: str) -> int:
            if operand_str[0].isalpha():
                row_idx, col_idx = self._parse_cell_ref(operand_str)
                # If the cell reference is out of bounds, its value is considered 0.
                if 0 <= row_idx < self.rows and 0 <= col_idx < 26:
                    return self.grid[row_idx][col_idx]
                else:
                    return 0
            else:
                return int(operand_str)

        total_sum += _get_operand_value(operand1_str)
        total_sum += _get_operand_value(operand2_str)

        return total_sum
```
