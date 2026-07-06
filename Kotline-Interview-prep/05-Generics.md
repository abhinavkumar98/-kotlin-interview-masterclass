1. **What are generics in Kotlin and why use them?**
   - Generics let you write type-safe, reusable code that works with different types without casting.
   - Example: `class Box<T>(val value: T)` allows `Box<String>` and `Box<Int>`.

2. **Generic classes vs generic functions**
   - Generic classes declare type parameters on the class: `class Result<T>(val value: T)`.
   - Generic functions declare parameters on the function: `fun <T> List<T>.secondOrNull(): T?`.
   - Use function generics for ad-hoc behavior, class generics for stateful containers.

3. **What is variance and why does it matter?**
   - Variance controls how subtyping of generic types relates to subtyping of their type parameters.
   - `out` = covariant (producer-only), `in` = contravariant (consumer-only).
   - Example: `interface Producer<out T>` allows `Producer<String>` to be used where `Producer<Any>` is expected.

4. **Covariance (`out`) explained**
   - Marking a type parameter `out` means the generic type only produces `T` (returns it).
   - Prevents consuming APIs (you cannot accept `T` as a parameter), ensuring type safety.
   - Example: `interface ReadOnlyList<out T> { fun get(i: Int): T }`.

5. **Contravariance (`in`) explained**
   - `in` marks a parameter as consumable: you can pass `T` into functions but not return it as `T`.
   - Useful for callbacks/consumers: `interface Comparator<in T> { fun compare(a: T, b: T): Int }`.

6. **Star-projection (`*`) and when to use it**
   - `List<*>` means a `List` of some unknown type; safe for read-only ops returning `Any?`.
   - Use when you need to work with generic types but the exact parameter is irrelevant.

7. **Reified generics and `inline` functions**
   - Type parameters are erased at runtime; marking them `reified` in `inline` functions lets you access the type at runtime.
   - Example: `inline fun <reified T> Gson.fromJson(json: String) = fromJson(json, T::class.java)`.

8. **Use-site variance vs declaration-site variance**
   - Declaration-site (`out`/`in` where the generic is declared) is preferred for APIs.
   - Use-site variance (`List<out T>` in a variable or parameter) is applied where you use the generic.

9. **Type bounds and constraints**
   - Use `where` or `: UpperBound` to constrain generics: `fun <T: Number> sum(values: List<T>)`.
   - Multiple bounds: `fun <T> f() where T: A, T: B`.

10. **Generic type inference and ambiguity**
    - Kotlin infers generic types in most calls; use explicit type arguments when inference fails: `foo<String>()`.

11. **Variance pitfalls and common interview questions**
    - Pitfall: trying to both consume and produce a generic type marked `out` or `in` (compiler rejects).
    - Interview task: design a producer/consumer API using `in`/`out` correctly.

12. **Examples: implement a type-safe producer/consumer**
    ```kotlin
    interface Producer<out T> { fun produce(): T }
    interface Consumer<in T> { fun consume(item: T) }

    fun copy(from: Producer<Number>, to: Consumer<Number>) {
        to.consume(from.produce())
    }
    ```

13. **Star-projection vs raw types (Java interop)**
    - Kotlin avoids raw types; `*` is the safe alternative to Java raw types with explicit nullability behavior.

14. **Generics and performance**
    - Generics are erased on the JVM; there is no per-instance cost for parametric types.
    - Boxing may occur for primitives, impacting performance — consider specialized primitives if necessary.

15. **Advanced topics: generic reflection and reified inline helpers**
    - Use `reified` to implement `isInstance`, `createInstance`, or serialization helpers without passing `Class<T>`.

16. **Interview follow-ups**
    - Ask to implement a small type-safe collection with `in`/`out` constraints, or to refactor Java code with raw types to Kotlin generics.