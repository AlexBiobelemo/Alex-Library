## Design Movie Rental System
**Language:** python
**Tags:** python,oop,sortedlist,hashmap

### Description:
This code implements a `MovieRentingSystem` that manages movie rentals across various shops. It provides functionalities to search for available movies, rent a movie, return a movie, and report the currently rented movies. The system prioritizes efficiency for lookups and operations involving sorted lists, primarily leveraging the `sortedcontainers` library.

### 1. Overview & Intent

The `MovieRentingSystem` class simulates a system for renting movies.
Its primary goals are to:

*   **Initialize** with a given set of movies available at different shops with specific prices.
*   **Search** for the cheapest available movies of a given ID across all shops.
*   **Rent** a specific movie from a specific shop, removing it from availability.
*   **Drop (return)** a specific movie to a specific shop, making it available again.
*   **Report** the cheapest 5 currently rented movies.

### 2. How It Works

The system maintains three main data structures:

*   **`self.movie_info`**: A dictionary that maps `(shop_id, movie_id)` tuples to their corresponding `price`. This allows for `O(1)` lookup of a movie's price given its shop and movie ID.
*   **`self.available_movies`**: A `defaultdict` where keys are `movie_id`s and values are `SortedList`s. Each `SortedList` stores `(price, shop_id)` tuples for that specific movie, automatically sorted first by price, then by shop ID. This efficiently keeps track of all unrented movie instances.
*   **`self.rented_movies`**: A single `SortedList` that stores `(price, shop_id, movie_id)` tuples for all currently rented movies. This list is automatically sorted first by price, then by shop ID, then by movie ID, facilitating easy retrieval of the cheapest rented items.

**Method Breakdown:**

*   **`__init__(self, n: int, entries: List[List[int]])`**:
    *   Initializes the `movie_info` dictionary by populating it with `(shop, movie) -> price` from the `entries`.
    *   For each entry, it also adds the `(price, shop)` tuple to the `SortedList` corresponding to the `movie_id` in `available_movies`.
*   **`search(self, movie: int) -> List[int]`**:
    *   Retrieves the `SortedList` for the given `movie_id` from `available_movies`.
    *   Slices the `SortedList` to get the first 5 elements (which represent the cheapest 5 `(price, shop)` pairs due to sorting).
    *   Extracts only the `shop_id` from these pairs and returns them as a list.
*   **`rent(self, shop: int, movie: int) -> None`**:
    *   Looks up the `price` of the `(shop, movie)` using `movie_info`.
    *   Removes the `(price, shop)` tuple from the `SortedList` in `available_movies[movie]`.
    *   Adds the `(price, shop, movie)` tuple to `rented_movies`.
*   **`drop(self, shop: int, movie: int) -> None`**:
    *   Looks up the `price` of the `(shop, movie)` using `movie_info`.
    *   Removes the `(price, shop, movie)` tuple from `rented_movies`.
    *   Adds the `(price, shop)` tuple back to the `SortedList` in `available_movies[movie]`.
*   **`report(self) -> List[List[int]]`**:
    *   Slices `rented_movies` to get the first 5 elements (cheapest 5 rented movies).
    *   Extracts `[shop, movie]` for each of these and returns them as a list of lists.

### 3. Key Design Decisions

*   **`sortedcontainers.SortedList`**: This is the core data structure choice. It automatically maintains sorted order upon insertion and deletion, making it highly efficient for:
    *   Retrieving the cheapest items (e.g., top 5) by simple slicing (`[:5]`).
    *   Adding and removing elements while preserving order.
*   **Tuple Ordering for Sorting**:
    *   `available_movies` stores `(price, shop_id)`: Python's tuple comparison sorts lexicographically, so items are sorted first by `price`, then by `shop_id` for ties. This matches the requirement to find the cheapest movie first, then by shop ID.
    *   `rented_movies` stores `(price, shop_id, movie_id)`: Similar lexicographical sorting ensures cheapest rented movies are first, then by shop, then by movie ID.
*   **`movie_info` dictionary**: Provides `O(1)` average time complexity for price lookups, which is crucial for `rent` and `drop` operations.
*   **`defaultdict` for `available_movies`**: Simplifies handling cases where a movie ID might not have been searched yet, as it automatically creates an empty `SortedList` if the key doesn't exist.

### 4. Complexity

Let `N` be the total number of initial entries (unique shop-movie pairs).
Let `M` be the number of unique movie IDs.
Let `S` be the number of unique shop IDs.

*   `K_m`: Maximum number of shops selling a single movie (`K_m <= S`).
*   `K_r`: Maximum number of concurrently rented movies (`K_r <= N`).

The `sortedcontainers.SortedList` operations have the following complexities:
*   `add()`: `O(log K)`
*   `remove()`: `O(log K)`
*   Slicing `[:k]`: `O(k)`

**Time Complexity:**

*   **`__init__(self, n, entries)`**:
    *   Iterates `N` entries.
    *   `movie_info` insertion: `O(1)`.
    *   `available_movies[movie].add()`: `O(log K_m)`.
    *   Total: `O(N log S)` (since `K_m <= S`).
*   **`search(self, movie)`**:
    *   `available_movies` lookup: `O(1)`.
    *   `SortedList[:5]` slicing: `O(5)`.
    *   Total: `O(1)`.
*   **`rent(self, shop, movie)`**:
    *   `movie_info` lookup: `O(1)`.
    *   `available_movies[movie].remove()`: `O(log K_m)`.
    *   `rented_movies.add()`: `O(log K_r)`.
    *   Total: `O(log S + log N)`.
*   **`drop(self, shop, movie)`**:
    *   `movie_info` lookup: `O(1)`.
    *   `rented_movies.remove()`: `O(log K_r)`.
    *   `available_movies[movie].add()`: `O(log K_m)`.
    *   Total: `O(log N + log S)`.
*   **`report(self)`**:
    *   `rented_movies[:5]` slicing: `O(5)`.
    *   Total: `O(1)`.

**Space Complexity:**

*   **`movie_info`**: `O(N)` for storing `N` unique `(shop, movie) -> price` mappings.
*   **`available_movies`**: `O(N)` for storing all initial `(price, shop)` entries across all movie IDs.
*   **`rented_movies`**: `O(N)` in the worst case where all movies are rented.
*   Total: `O(N)`.

### 5. Edge Cases & Correctness

*   **Empty `entries`**: The `__init__` loop won't run, resulting in empty data structures. Subsequent calls to `search` or `report` will return empty lists, which is correct.
*   **`movie` not found in `search`**: `self.available_movies[movie]` will return an empty `SortedList` (due to `defaultdict`), so `[:5]` will return an empty list, which is correct.
*   **Fewer than 5 items in `search` or `report`**: Python's list slicing `[:5]` gracefully handles this by returning all available items up to the 5th, which is correct behavior.
*   **Renting/Dropping a non-existent `(shop, movie)`**: The problem constraints typically guarantee valid inputs for `rent` and `drop`. If not, `self.movie_info[(shop, movie)]` would raise a `KeyError`, and `remove` operations on `SortedList` would raise `ValueError` if the item isn't found. This highlights that these methods assume the `(shop, movie)` pair legitimately exists and is in the expected state (available for `rent`, rented for `drop`).

### 6. Improvements & Alternatives

*   **Error Handling**: For production systems, `rent` and `drop` methods should include checks to prevent `KeyError` or `ValueError` if inputs are not guaranteed to be valid by problem constraints. For example, check if `(shop, movie)` exists in `movie_info` before proceeding, or if the item to remove actually exists in the `SortedList`.
*   **Readability**:
    *   Add docstrings to explain each method's purpose, parameters, and return value.
    *   More verbose variable names could be considered, though the current ones are clear enough.
*   **Alternative for `SortedList`**:
    *   If `sortedcontainers` wasn't allowed or desired as a dependency, one could implement a custom balanced binary search tree (like an AVL tree or Red-Black tree) or a skip list to achieve similar logarithmic performance for insertions and deletions while maintaining order. However, `SortedList` is highly optimized and often outperforms custom implementations unless specific, highly specialized behaviors are needed.
    *   For `report` and `search`, if only the top `k` elements are ever needed and `k` is small, a min-heap (for available movies) and a max-heap (for rented movies, if finding most expensive was needed, otherwise min-heap for cheapest) could be used to maintain the top `k` elements efficiently, but managing `k` heaps (one per movie) could be complex for `available_movies`. The current `SortedList` approach is simpler and effective.

### 7. Security/Performance Notes

*   **`sortedcontainers` library**: This is a well-regarded, high-performance pure-Python implementation of sorted list-like data structures. It's generally safe and efficient for competitive programming and many production use cases. Its underlying implementation uses a list of lists (a "list of blocks") to achieve `O(log N)` for `add`/`remove` by only shifting elements within a block or moving blocks, and `O(1)` for `__getitem__` (index lookup).
*   **Performance with large datasets**: The logarithmic time complexity for core operations ensures that the system scales well with a large number of movies and shops. The constant factors of `SortedList` are typically low enough for practical use.
*   **Memory Usage**: The overall `O(N)` space complexity is optimal, as we need to store information about all `N` initial entries.

### Code:
```python
from typing import List
from collections import defaultdict
from sortedcontainers import SortedList

class MovieRentingSystem:

    def __init__(self, n: int, entries: List[List[int]]):
        # Stores (shop, movie) -> price for quick lookup
        self.movie_info = {} 
        
        # Stores unrented movies. Key: movie_id, Value: SortedList of (price, shop_id)
        # SortedList ensures elements are sorted by price, then shop_id.
        self.available_movies = defaultdict(SortedList)
        
        # Stores currently rented movies. Value: SortedList of (price, shop_id, movie_id)
        # SortedList ensures elements are sorted by price, then shop_id, then movie_id.
        self.rented_movies = SortedList()

        for shop, movie, price in entries:
            self.movie_info[(shop, movie)] = price
            self.available_movies[movie].add((price, shop))

    def search(self, movie: int) -> List[int]:
        # Get the cheapest 5 shops for the given movie from available_movies.
        # The SortedList is already ordered by (price, shop_id).
        return [shop for price, shop in self.available_movies[movie][:5]]

    def rent(self, shop: int, movie: int) -> None:
        price = self.movie_info[(shop, movie)]
        
        # Remove from available_movies
        self.available_movies[movie].remove((price, shop))
        
        # Add to rented_movies
        self.rented_movies.add((price, shop, movie))

    def drop(self, shop: int, movie: int) -> None:
        price = self.movie_info[(shop, movie)]
        
        # Remove from rented_movies
        self.rented_movies.remove((price, shop, movie))
        
        # Add back to available_movies
        self.available_movies[movie].add((price, shop))

    def report(self) -> List[List[int]]:
        # Get the cheapest 5 rented movies from rented_movies.
        # The SortedList is already ordered by (price, shop_id, movie_id).
        return [[shop, movie] for price, shop, movie in self.rented_movies[:5]]
```
