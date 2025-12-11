## Group By
**Language:** javascript
**Tags:** javascript,array,prototype,groupby

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code snippet extends the built-in <code>Array.prototype</code> to add a <code>groupBy</code> method.</p>
<ul>
<li><strong>What it does:</strong> The <code>groupBy</code> method takes a <code>fn</code> (function) as an argument. It iterates over the array elements, applies <code>fn</code> to each element to compute a grouping key, and then organizes the elements into a new object. The keys of this new object are the computed grouping keys, and the values are arrays containing all elements that generated that specific key.</li>
<li><strong>Why:</strong> It provides a convenient, functional way to aggregate or categorize data within an array based on a derived property, similar to the <code>GROUP BY</code> clause in SQL or the concept in functional programming libraries.</li>
</ul>
<h3>2. How It Works</h3>
<p>The implementation follows a straightforward aggregation pattern:</p>
<ul>
<li><strong>Initialization:</strong> An empty plain JavaScript object, <code>grouped</code>, is created. This object will store the final grouped data.</li>
<li><strong>Iteration:</strong> The code iterates through each <code>item</code> in the array (<code>this</code>) using a traditional <code>for</code> loop.</li>
<li><strong>Key Generation:</strong> For each <code>item</code>, the provided callback function <code>fn(item)</code> is executed to compute the <code>key</code> under which the item should be grouped.</li>
<li><strong>Grouping Logic:</strong><ul>
<li>It checks if an entry for the <code>key</code> already exists in the <code>grouped</code> object.</li>
<li>If not, a new empty array is initialized for that <code>key</code> (e.g., <code>grouped[key] = []</code>).</li>
<li>The current <code>item</code> is then pushed into the array associated with its <code>key</code>.</li>
</ul>
</li>
<li><strong>Return Value:</strong> After processing all items in the array, the fully populated <code>grouped</code> object is returned.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Grouping Data Structure (<code>grouped</code>):</strong><ul>
<li><strong>Decision:</strong> A plain JavaScript object (<code>{}</code>) is used to store the groups.</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Pros:</strong> Simple, widely understood, and efficient for string-based keys, which JavaScript objects inherently use.</li>
<li><strong>Cons:</strong> Keys are always coerced to strings (or Symbols). If <code>fn</code> returns non-primitive values (like other objects or arrays) as keys, they will all coerce to the same string (e.g., <code>"[object Object]"</code>) leading to incorrect grouping for distinct non-primitive keys. For primitive keys (numbers, booleans, null, undefined), coercion to string is usually acceptable and expected (e.g., <code>1</code> becomes <code>"1"</code>).</li>
</ul>
</li>
</ul>
</li>
<li><strong>Iteration Method:</strong><ul>
<li><strong>Decision:</strong> A standard <code>for (let i = 0; i &lt; this.length; i++)</code> loop.</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Pros:</strong> Performant and works reliably across all JavaScript environments and array-like objects.</li>
<li><strong>Cons:</strong> Less idiomatic "functional" style compared to <code>reduce</code> or <code>for...of</code> in modern JavaScript.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Prototype Extension:</strong><ul>
<li><strong>Decision:</strong> Extends <code>Array.prototype</code> directly.</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Pros:</strong> Makes the <code>groupBy</code> method directly available on all array instances (<code>myArray.groupBy(...)</code>), offering a clean API.</li>
<li><strong>Cons:</strong> Can lead to "prototype pollution" if multiple libraries define methods with the same name, potentially causing conflicts. It also makes the method enumerable, meaning it can appear when iterating over array properties with <code>for...in</code> (though <code>for...in</code> is generally not recommended for arrays).</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the number of elements in the array.
Let <code>C_fn</code> be the time complexity of the provided callback function <code>fn</code>.</p>
<ul>
<li><strong>Time Complexity:</strong> <code>O(N * C_fn)</code><ul>
<li>The loop iterates <code>N</code> times, once for each element.</li>
<li>Inside the loop, <code>fn(item)</code> is called once. Assuming <code>fn</code> performs constant-time operations (<code>O(1)</code>, e.g., <code>String</code>, <code>Math.floor</code>, property access), the overall time complexity is <code>O(N)</code>.</li>
<li>Object property access and array <code>push</code> operations are typically <code>O(1)</code> on average.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> <code>O(N)</code><ul>
<li>In the worst case, all <code>N</code> elements are unique and result in <code>N</code> distinct keys. The <code>grouped</code> object will store all <code>N</code> elements across its various arrays. The overhead for the keys themselves (number of unique keys, <code>K</code>) is at most <code>N</code>. Therefore, the total space required is proportional to the number of elements in the original array.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty Array:</strong> If the input array is empty (<code>[]</code>), the loop won't execute, and an empty object <code>{}</code> will be returned, which is correct.</li>
<li><strong><code>fn</code> returning <code>null</code>/<code>undefined</code>/numbers/booleans:</strong> These primitive values will be coerced into their string representations (e.g., <code>null</code> becomes <code>"null"</code>, <code>1</code> becomes <code>"1"</code>, <code>true</code> becomes <code>"true"</code>) and used as keys. The grouping will function correctly under these string keys.</li>
<li><strong><code>fn</code> returning objects/arrays as keys:</strong> This is a critical edge case. If <code>fn</code> returns distinct non-primitive values (e.g., <code>{ id: 1 }</code>, <code>{ id: 2 }</code>), JavaScript's object key coercion will convert them to the same string (e.g., <code>"[object Object]"</code>). This will lead to all such distinct objects/arrays being grouped under a <em>single key</em>, which is likely unintended and incorrect for distinct grouping.</li>
<li><strong>Array contains <code>null</code> or <code>undefined</code> elements:</strong> These elements are processed normally; <code>fn</code> is called with them, and they are grouped based on <code>fn</code>'s return value.</li>
<li><strong>Correctness:</strong> The code correctly implements the grouping logic based on the chosen data structure (plain object) and its characteristic key coercion. The behavior with non-primitive keys is a limitation of the chosen data structure rather than an algorithmic flaw.</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Modern JavaScript Syntax &amp; Readability:</strong><ul>
<li><strong><code>for...of</code> loop:</strong> More concise and often preferred for iterating over iterable objects like arrays.<pre><code class="language-javascript">Array.prototype.groupBy = function(fn) {
    const grouped = {};
    for (const item of this) { // Modern iteration
        const key = fn(item);
        (grouped[key] = grouped[key] || []).push(item); // Shorter initialization
    }
    return grouped;
};
</code></pre>
</li>
<li><strong><code>reduce</code> method:</strong> A more functional approach for aggregation.<pre><code class="language-javascript">Array.prototype.groupBy = function(fn) {
    return this.reduce((acc, item) =&gt; {
        const key = fn(item);
        (acc[key] = acc[key] || []).push(item);
        return acc;
    }, {});
};
</code></pre>
</li>
</ul>
</li>
<li><strong>Handling Non-Primitive Keys (e.g., Objects as Keys):</strong><ul>
<li>If keys need to maintain their identity (e.g., grouping by specific object instances), a <code>Map</code> should be used instead of a plain object. A <code>Map</code> allows any value (including objects) as a key. If the final output <em>must</em> be a plain object, <code>Object.fromEntries(map)</code> can convert it back, though this would reintroduce the string coercion issue if object keys were used. For the shown output, a plain object with string keys is expected.<pre><code class="language-javascript">// Returns a Map, not a plain object
Array.prototype.groupByMap = function(fn) {
    const grouped = new Map();
    for (const item of this) {
        const key = fn(item);
        if (!grouped.has(key)) {
            grouped.set(key, []);
        }
        grouped.get(key).push(item);
    }
    return grouped; // Returns a Map
};
</code></pre>
</li>
</ul>
</li>
<li><strong>Robustness (Avoiding Prototype Pollution):</strong><ul>
<li>For libraries or shared code, extending built-in prototypes is generally discouraged due to potential conflicts. A standalone utility function is safer.<pre><code class="language-javascript">function groupBy(arr, fn) {
    const grouped = {};
    for (const item of arr) {
        const key = fn(item);
        (grouped[key] = grouped[key] || []).push(item);
    }
    return grouped;
}
// Usage: groupBy([1,2,3], String);
</code></pre>
</li>
<li>If prototype extension is required, consider making the property <strong>non-enumerable</strong> to prevent it from showing up in <code>for...in</code> loops.<pre><code class="language-javascript">Object.defineProperty(Array.prototype, 'groupBy', {
    value: function(fn) { /* ... implementation ... */ },
    writable: true,
    configurable: true,
    enumerable: false // Key improvement for prototype extension
});
</code></pre>
</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> The current implementation is efficient (<code>O(N)</code> time and space) for typical use cases with primitive keys. There are no obvious performance bottlenecks. The choice of <code>for</code> loop vs. <code>for...of</code> vs. <code>reduce</code> has minor, often negligible, performance differences in modern engines.</li>
<li><strong>Security (Prototype Pollution Risk):</strong> Extending <code>Array.prototype</code> can be a security concern. Malicious code could potentially override or modify this <code>groupBy</code> method if it's not adequately protected, leading to unexpected behavior or denial of service in applications that rely on it. While this specific method isn't inherently vulnerable to typical <em>object</em> prototype pollution attacks, it falls under the general best practice of avoiding direct prototype modification in shared environments. Using <code>Object.defineProperty</code> with <code>enumerable: false</code> mitigates some of the risks but doesn't eliminate the possibility of malicious overwrite entirely. A standalone function is the safest approach.</li>
</ul>


### Code:
```javascript
/**
 * @param {Function} fn
 * @return {Object}
 */
Array.prototype.groupBy = function(fn) {
    const grouped = {};
    for (let i = 0; i < this.length; i++) {
        const item = this[i];
        const key = fn(item);
        if (!grouped[key]) {
            grouped[key] = [];
        }
        grouped[key].push(item);
    }
    return grouped;
};

/**
 * [1,2,3].groupBy(String) // {"1":[1],"2":[2],"3":[3]}
 */
```
