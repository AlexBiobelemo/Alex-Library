## Execute Asynchronous Functions in Parallel
**Language:** typescript
**Tags:** typescript,promise,asynchronous,functional

### Description:
This JavaScript function provides a custom implementation of the functionality similar to `Promise.all`. It takes an array of functions, where each function is expected to return a Promise.

### 1. Overview & Intent

This code implements a simplified version of `Promise.all`. Its purpose is to:
*   Execute a collection of asynchronous operations (represented by functions returning promises) concurrently.
*   Return a new Promise that resolves with an array of all the individual operation results, in the same order as the input functions.
*   If *any* of the individual operations (promises) reject, the returned Promise immediately rejects with the reason of the first promise to reject.

### 2. How It Works

1.  **Outer Promise Creation**: A new `Promise` is created and returned immediately. This promise's `resolve` and `reject` functions (`resolve` and `reject` in the lambda) control the ultimate outcome of the `promiseAll` operation.
2.  **Empty Input Handling**: If the input `functions` array is empty, the outer promise immediately resolves with an empty array `[]`. This is an important base case.
3.  **State Initialization**:
    *   `results`: An array pre-allocated to the size of `functions` to store the resolved values. Its size ensures consistent ordering.
    *   `resolvedCount`: A counter to track how many promises have successfully resolved.
    *   `hasRejected`: A boolean flag to ensure that the outer promise rejects only once, with the reason from the *first* rejection.
4.  **Concurrent Execution**: The code iterates through each `fn` in the `functions` array using `forEach`.
    *   For each `fn`, it immediately calls `fn()` to obtain a promise.
    *   **`.then()` Handler**: When an individual promise resolves:
        *   It first checks `hasRejected`. If a rejection has already occurred, it bails out, preventing further state updates to the now-rejected aggregate promise.
        *   The resolved `value` is stored in the `results` array at the correct `index`.
        *   `resolvedCount` is incremented.
        *   If `resolvedCount` equals the total number of functions, it means all promises have successfully completed, so the outer promise `resolve`s with the `results` array.
    *   **`.catch()` Handler**: When an individual promise rejects:
        *   It first checks `!hasRejected`. If a rejection has *not* yet occurred, it sets `hasRejected` to `true`.
        *   The outer promise `reject`s with the `reason` from this individual promise. This implements the "fail-fast" behavior.

### 3. Key Design Decisions

*   **New Promise Wrapper**: The core mechanism to turn an array of asynchronous operations into a single aggregate promise. This is fundamental for promise-based APIs.
*   **Pre-allocated `results` Array**:
    *   **Pros**: Ensures the final resolved array maintains the original order of the input functions. Efficient, as it avoids dynamic resizing.
    *   **Cons**: Requires knowing the final size upfront, which is fine here.
*   **`resolvedCount`**: Simple and effective counter to determine when all successful completions have occurred.
*   **`hasRejected` Flag**: Crucial for implementing the `Promise.all` "fail-fast" behavior, where the first rejection dictates the aggregate promise's outcome and prevents subsequent state changes (like resolving) from overriding it.
*   **`forEach` Loop**: Initiates all promises concurrently, which is the standard behavior for `Promise.all`.

### 4. Complexity

*   **Time Complexity**:
    *   **Setup/Orchestration**: O(N), where N is the number of functions in the input array. This involves iterating through the array, creating promises, and setting up their handlers.
    *   **Wall-Clock Execution**: O(max(T_1, T_2, ..., T_N)), where T_i is the time taken for the i-th function's promise to resolve or reject. Since all promises are initiated concurrently, the total time is dominated by the longest-running promise.
*   **Space Complexity**: O(N), for storing the `results` array. Additional space might be consumed by the promises themselves and their execution contexts, but the primary explicit memory usage scales with N.

### 5. Edge Cases & Correctness

*   **Empty `functions` array**: Correctly handled by resolving immediately with `[]`.
*   **All promises resolve**: `resolvedCount` correctly tracks completion, and the outer promise resolves with the ordered results.
*   **A single promise rejects (first to finish or not)**: The `hasRejected` flag ensures the outer promise rejects only once with the reason of the *first* promise to reject. Subsequent rejections or resolutions are ignored for the aggregate promise.
*   **Functions that don't return promises**: The code implicitly assumes `fn()` returns a promise. If a function returns a non-promise value or throws synchronously, `fn().then(...)` would throw a `TypeError` or an unhandled exception. The native `Promise.all` implicitly wraps non-promise values with `Promise.resolve()`. This implementation does not.
*   **Order of results**: Maintained correctly because values are assigned to `results[index]`.

### 6. Improvements & Alternatives

*   **Robustness for non-Promise values**: To fully mimic `Promise.all`, each `fn()`'s result should be wrapped with `Promise.resolve()`.
    ```javascript
    // Inside the forEach loop:
    Promise.resolve(fn()) // ensures fn() can return non-promise values
        .then(value => { /* ... */ })
        .catch(reason => { /* ... */ });
    ```
*   **Alternative for `resolvedCount`**: Could use a `Set` or `Map` to track pending promises, removing them upon resolution, and resolving when the set is empty. However, `resolvedCount` is simpler and efficient for this use case.
*   **Cancellation (Advanced)**: Standard JavaScript Promises are not cancellable. If true cancellation of underlying operations were required (e.g., stopping a network request), a more complex pattern involving `AbortController` or similar would be needed. This is beyond the scope of a basic `Promise.all` implementation.
*   **Concurrency Limiting**: For very large `functions` arrays, initiating all promises at once might exhaust system resources (e.g., too many open file descriptors or network connections). For such cases, a concurrency limiter (e.g., processing only `k` promises at a time) would be a more robust solution, but this changes the fundamental concurrent nature of `Promise.all`.

### 7. Security/Performance Notes

*   **Concurrency**: By initiating all promises concurrently, the function leverages parallelism (if I/O bound) or multi-core processors (if CPU bound) effectively, leading to optimal wall-clock performance for the task.
*   **Resource Usage**: While concurrent execution is generally good, be mindful of the number of promises launched if `functions.length` is extremely large. Each promise consumes some memory, and if they represent heavy I/O operations (e.g., network requests), too many concurrent operations can saturate network bandwidth, exhaust system resources, or even lead to denial-of-service if external services have rate limits.
*   **Untrusted Input**: If the `functions` array comes from untrusted user input, there's a risk of executing malicious or overly resource-intensive code. This is a general concern when executing arbitrary code, not specific to this `promiseAll` implementation itself.

### Code:
```typescript
/**
 * @param {Array<Function>} functions
 * @return {Promise<any>}
 */
var promiseAll = function(functions) {
    return new Promise((resolve, reject) => {
        if (functions.length === 0) {
            resolve([]);
            return;
        }

        const results = new Array(functions.length);
        let resolvedCount = 0;
        let hasRejected = false;

        functions.forEach((fn, index) => {
            fn()
                .then(value => {
                    if (hasRejected) {
                        return;
                    }
                    results[index] = value;
                    resolvedCount++;

                    if (resolvedCount === functions.length) {
                        resolve(results);
                    }
                })
                .catch(reason => {
                    if (!hasRejected) {
                        hasRejected = true;
                        reject(reason);
                    }
                });
        });
    });
};
```
