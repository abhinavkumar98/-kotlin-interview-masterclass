1. **What are nullable types in Kotlin and how do you declare them?**
   - A nullable type is declared by appending `?` to the type: `String?`.
   - Variables of nullable types can hold `null`; non-nullable types cannot.
   - Example:
     ```kotlin
     var name: String? = null
     var nickname: String = "Bob"
     ```
   - **Important:** Prefer non-nullable types where possible to avoid NPEs.

2. **How does the safe call operator `?.` work?**
   - `a?.b` returns `b` if `a` is non-null, otherwise returns `null` without throwing.
   - Useful for chaining when intermediate values may be null.
   - Example:
     ```kotlin
     val len = person?.address?.street?.length
     ```

3. **What is the Elvis operator `?:` and when to use it?**
   - `expr ?: default` returns `expr` if non-null, otherwise returns `default`.
   - Often used to provide fallback values or throw exceptions: `val v = a ?: throw()`
   - Example:
     ```kotlin
     val name = maybeName ?: "Unknown"
     ```

4. **How do non-null assertions `!!` work and why avoid them?**
   - `x!!` asserts `x` is non-null, throwing `KotlinNullPointerException` if it is null.
   - Avoid except in exceptional cases; prefer safer alternatives.

5. **What are smart casts and when do they apply?**
   - After a null-check or type-check, the compiler automatically casts to the non-null or target type.
   - Example:
     ```kotlin
     if (obj is String) println(obj.length) // obj is smart-cast to String
     if (s != null) println(s.length) // s is smart-cast to non-null String
     ```
   - **Limitation:** Smart casts don't work for mutable properties that can change between check and use.

6. **How is `let` commonly used with nullable types?**
   - `let` executes a block only when the receiver is non-null and provides it as `it`.
   - Great for scoping and avoiding nested `if` checks.
   - Example:
     ```kotlin
     maybeName?.let { println(it.length) }
     ```

7. **What do `requireNotNull` and `checkNotNull` do?**
   - `requireNotNull(value)` throws `IllegalArgumentException` if `value` is null.
   - `checkNotNull(value)` throws `IllegalStateException` if `value` is null.
   - Use them to enforce contracts with clearer exception semantics.

8. **When to use `lateinit` and what are its constraints?**
   - `lateinit var` allows deferred initialization for non-null `var` properties.
   - Constraints: only for non-primitive `var` properties, not `val`, and not nullable types.
   - Accessing before init throws `UninitializedPropertyAccessException`.

9. **How does `lazy` differ from `lateinit`?**
   - `lazy` is for read-only `val` properties initialized on first access: `val x by lazy { ... }`.
   - `lazy` is thread-safe by default (`LazyThreadSafetyMode.SYNCHRONIZED`) but can be tuned.

10. **How to invoke nullable function types safely?**
    - Use safe-invoke: `callback?.invoke()` to call only when non-null.

11. **What patterns help avoid null-related bugs?**
    - Prefer non-nullable types; use sealed/result types to model absence.
    - Use `Optional`-like wrappers (or `Result`/`Either`) for richer semantics.
    - Use `when`/`sealed` to handle all states explicitly.

12. **How to interoperate with Java nullability?**
    - Java types are platform types in Kotlin and may be treated as nullable or non-nullable; annotate Java with nullability annotations (`@Nullable`, `@NotNull`) for clarity.
    - Always perform null checks or use safe operators when calling Java code.

13. **What are common pitfalls in null-safety interviews?**
    - Overuse of `!!` leading to runtime NPEs.
    - Relying on smart casts for mutable properties.
    - Assuming Java interop guarantees non-null values.

14. **Example: converting nullable chain to a safe result**
    ```kotlin
    data class User(val profile: Profile?)
    data class Profile(val email: String?)

    fun getEmailLength(user: User?): Int =
        user?.profile?.email?.length ?: 0
    ```

15. **Interview follow-ups**
    - Ask to refactor code to avoid `!!`.
    - Demonstrate `lateinit` vs `lazy` with thread-safety considerations.
    - Show wrapping nullable results in a `Result` or sealed type for explicit handling.
