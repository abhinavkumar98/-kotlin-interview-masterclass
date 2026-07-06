````markdown
# 04. Collections — Questions & Answers

1. **List vs MutableList — what's the difference?**
   - `List` is read-only (immutable view) while `MutableList` supports mutation (`add`, `remove`).
   - Use `List` in APIs to expose immutability and `MutableList` for internal mutable state.
   - Example:
     ```kotlin
     val read: List<Int> = listOf(1,2,3)
     val mut: MutableList<Int> = mutableListOf(1,2,3)
     mut.add(4)
     ```

2. **Set vs HashSet — when to use which?**
   - `Set` is the read-only interface; `HashSet` is a concrete implementation with O(1) average lookup.
   - Choose `LinkedHashSet` to preserve insertion order, `TreeSet` for sorted sets (via Java interop).

3. **Map vs HashMap vs LinkedHashMap**
   - `Map` is read-only; `HashMap` is an unordered mutable map with O(1) access.
   - `LinkedHashMap` preserves insertion order; `TreeMap` (Java) gives sorted order.

4. **ArrayList vs LinkedList — performance tradeoffs**
   - `ArrayList` (backed by array) gives O(1) random access, O(n) inserts at middle.
   - `LinkedList` gives O(1) insert/delete at known node but O(n) random access; prefer `ArrayList` for most cases.

5. **Mutable Collections — best practices**
   - Prefer exposing immutable interfaces (`List`, `Set`, `Map`) and keep `Mutable*` internal.
   - Use `Collections.unmodifiable*` only when interoperating with Java; prefer Kotlin read-only types.

6. **Common collection operations: `map`, `flatMap`, `filter`**
   - `map`: transforms each element: `list.map { it * 2 }`.
   - `flatMap`: maps and flattens nested collections: `list.flatMap { it.items }`.
   - `filter`: returns elements matching predicate.

7. **`associate`, `groupBy`, `partition` — when to use?**
   - `associateBy` builds a map keyed by selector.
   - `groupBy` groups elements into a map of lists.
   - `partition` splits into Pair(listTrue, listFalse) based on predicate.

8. **`zip`, `chunked`, `windowed` usage**
   - `zip` combines two collections element-wise.
   - `chunked(n)` splits into fixed-size lists.
   - `windowed(size, step)` creates sliding windows for time-series or sequence operations.

9. **Sequences vs Collections: difference and when to use**
   - `Sequence` is lazy — operations are computed on-demand and can avoid intermediate allocations.
   - Use `sequence` for large pipelines or heavy transformations; prefer eager collections for small inputs.

10. **Collection performance considerations**
    - Prefer `ArrayList` for random access and memory efficiency.
    - Use `Sequence` to chain many operations lazily and avoid intermediate lists.
    - Be mindful of boxing for primitives — consider specialized libraries if necessary.

11. **Examples: transform large lists efficiently**
    ```kotlin
    val result = largeList.asSequence()
        .map { heavy(it) }
        .filter { it.ok }
        .take(100)
        .toList()
    ```

12. **Mutable vs Immutable copy patterns**
    - Use `toMutableList()` or `toList()` to convert between views and copies.
    - `plus`/`minus` operators return new collections; they do not mutate the original `List`.

13. **Thread-safety and concurrent collections**
    - Kotlin collections are not thread-safe by default; use synchronization or concurrent data structures from `java.util.concurrent` for multi-threaded access.

14. **Extension functions and collection DSLs**
    - Write extension functions to encapsulate common operations: `fun List<User>.active() = filter { it.active }`.

15. **Common interview pitfalls**
    - Assuming `List` is immutable — it is a read-only view.
    - Ignoring intermediate allocations in chained operations — consider `Sequence`.

16. **Interview follow-ups and exercises**
    - Implement `chunked` or `windowed` manually using indices.
    - Compare performance between `map().filter()` and the same pipeline using `Sequence` with benchmarks.

````
