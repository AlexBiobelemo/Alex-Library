## Calculator With Method Chaining
**Language:** typescript
**Tags:** typescript,oop,method chaining

### Description:
This `Calculator` class provides a simple, chainable interface for performing basic arithmetic operations on a numerical value.

---

### 1. Overview & Intent

*   **What it is:** A TypeScript class named `Calculator` designed to hold a single numerical value and perform common arithmetic operations on it.
*   **Purpose:** To provide a fluent API for performing sequential calculations, where each operation modifies the internal state (`result`) and returns the `Calculator` instance itself, enabling method chaining.

### 2. How It Works

*   **Initialization:** The `constructor` takes an initial `value` and stores it in the private `result` property.
*   **Arithmetic Operations:**
    *   `add`, `subtract`, `multiply`, `divide`, and `power` methods take a `value` argument.
    *   They update the `this.result` property by applying the respective mathematical operation.
    *   Crucially, each of these methods returns `this` (the current instance of `Calculator`), allowing for subsequent method calls to be chained.
*   **Error Handling:** The `divide` method specifically checks if the divisor `value` is `0`. If it is, it throws an `Error` to prevent division by zero, which would result in `Infinity` or `NaN`.
*   **Result Retrieval:** The `getResult()` method provides a way to access the final computed value stored in `this.result`.

### 3. Key Design Decisions

*   **Method Chaining (Fluent Interface):** Returning `this` from each operation is a core design choice, making the API expressive and readable (e.g., `calc.add(5).subtract(2).getResult()`).
*   **Mutable State:** The `this.result` property is directly modified by operations. This is common for calculator patterns but has implications for concurrency or immutability needs.
*   **Encapsulation:** The `result` property is `private`, meaning it can only be accessed and modified by methods within the `Calculator` class, promoting data integrity.
*   **Standard Number Type:** It uses JavaScript's built-in `number` type, which is a 64-bit floating-point (IEEE 754) type. This dictates the precision and range of calculations.
*   **Explicit Division by Zero Handling:** Rather than letting JavaScript return `Infinity` or `NaN`, the `divide` method explicitly throws an error, providing clearer feedback.

### 4. Complexity

*   **Time Complexity (Big-O):**
    *   `constructor`: O(1)
    *   `add`, `subtract`, `multiply`, `divide`, `getResult`: O(1) (basic arithmetic operations are constant time).
    *   `power`: O(1) (while `Math.pow` involves more complex computation, for practical purposes in JavaScript/TypeScript, it's considered a constant-time operation relative to input size, as it's a built-in CPU/runtime function).
*   **Space Complexity (Big-O):**
    *   Each `Calculator` instance: O(1) (stores a single `number` and method pointers).
    *   Method calls: O(1) (negligible stack space for arguments).

### 5. Edge Cases & Correctness

*   **Division by Zero:** Correctly handled by throwing an `Error`.
*   **Floating-Point Precision:** As the `number` type is floating-point, this class inherits JavaScript's inherent precision limitations (e.g., `0.1 + 0.2` might not exactly equal `0.3`). This is not an error in the code, but an inherent characteristic of the chosen data type.
*   **Large/Small Numbers:** Handles numbers within the standard JavaScript `Number.MAX_VALUE` and `Number.MIN_VALUE` range. Precision for integers may be lost beyond `Number.MAX_SAFE_INTEGER`.
*   **Negative Values:** All operations correctly handle negative input values and negative intermediate results.
*   **Zero Values:** Operations involving zero (e.g., `add(0)`, `multiply(0)`) behave as expected. `power(0)` behaves according to `Math.pow` rules (e.g., `Math.pow(x, 0)` is 1 for `x != 0`, `Math.pow(0,0)` is 1).

### 6. Improvements & Alternatives

*   **Immutability:** Instead of modifying `this.result`, operations could return *new* `Calculator` instances with the updated value. This makes the class more predictable, especially in concurrent environments or when intermediate states need to be preserved.
    ```typescript
    // Example for immutable add
    add(value: number): Calculator {
        return new Calculator(this.result + value);
    }
    ```
*   **Custom Error Types:** For more specific error handling, define and throw a custom `DivisionByZeroError` instead of a generic `Error`.
*   **Precision Libraries:** For applications requiring exact decimal arithmetic (e.g., financial calculations), integrate a specialized library like `decimal.js` or `big.js` to avoid floating-point inaccuracies.
*   **Input Validation:** Although TypeScript helps at compile time, adding runtime checks for `typeof value !== 'number'` in each operation method could make the class more robust against invalid inputs from untyped JavaScript or external APIs.
*   **More Operations:** Extend with modulo (`%`), square root (`Math.sqrt`), trigonometric functions (`Math.sin`, `Math.cos`), etc.
*   **Method Overloading/Variadic Arguments:** For operations like `add`, allow passing multiple numbers (`add(...values: number[])`) to sum them up.

### 7. Security/Performance Notes

*   **Performance:** The class is highly performant. All operations are O(1), involving direct arithmetic on primitive numbers. There are no loops, complex data structures, or expensive algorithms that would lead to performance bottlenecks.
*   **Security:** This code snippet itself does not introduce any direct security vulnerabilities. It's a purely computational utility. The only "security" aspect is robust error handling for division by zero to prevent unexpected program behavior (like `NaN` propagation). If this `Calculator` were to process untrusted user input, the application using it would need to perform input sanitization and validation *before* passing values to the `Calculator` instance.

### Code:
```typescript
class Calculator {
    private result: number;

    constructor(value: number) {
        this.result = value;
    }
    
    add(value: number): Calculator {
        this.result += value;
        return this;
    }
    
    subtract(value: number): Calculator {
        this.result -= value;
        return this;
    }
    
    multiply(value: number): Calculator {
        this.result *= value;
        return this;
    }
    
    divide(value: number): Calculator {
        if (value === 0) {
            throw new Error("Division by zero is not allowed");
        }
        this.result /= value;
        return this;
    }
    
    power(value: number): Calculator {
        this.result = Math.pow(this.result, value);
        return this;
    }
    
    getResult(): number {
        return this.result;
    }
}
```
