1. **What is a higher-order function?**
   - A higher-order function either takes functions as parameters or returns a function.
   - Use cases: callbacks, function composition, DSLs, and combinators.
   - Example:
     ```kotlin
     fun <T, R> List<T>.mapTransform(transform: (T) -> R): List<R> {
         val result = ArrayList<R>()
         for (item in this) result += transform(item)
         return result
     }
     val doubled = listOf(1,2,3).mapTransform { it * 2 }
     ```
   - **Important:** Function types are first-class citizens in Kotlin.

2. **What is an inline function and why use it?**
   - `inline` requests the compiler to inline the function body at call sites to avoid allocation and lambda capture costs.
   - Useful for small HOFs like `let`, `apply`, or builder-style DSLs.
   - Example:
     ```kotlin
     inline fun measure(block: () -> Unit) {
         val start = System.nanoTime()
         block()
         println("Elapsed: ${System.nanoTime() - start}")
     }
     ```
   - **Important:** Inlining increases bytecode size; use where performance matters.

3. **What do `noinline` and `crossinline` mean?**
   - `noinline` marks a lambda parameter to *not* be inlined (useful when storing or passing it around).
   - `crossinline` disallows non-local returns from the lambda while still allowing inlining.
   - Example:
     ```kotlin
     inline fun runWith(block: () -> Unit, noinline delayed: () -> Unit) { block(); delayed() }
     inline fun callCross(crossinline block: () -> Unit) { Runnable { block() }.run() }
     ```
   - **Important:** Use `noinline` when you need the lambda as a value; `crossinline` when you must prevent `return` from the lambda.

4. **How do extension functions work? Any limitations?**
   - Extension functions allow adding functions to existing types: `fun String.foo() = ...`.
   - They are static dispatch: resolved by the compile-time type, not runtime.
   - Example:
     ```kotlin
     fun String.lastChar() = this[length-1]
     println("Kotlin".lastChar()) // n
     ```
   - **Limitations:** Can't access private members; cannot truly modify the original class; overload resolution is static.

5. **What are infix functions and when to use them?**
   - `infix` lets you call single-argument functions in infix notation: `a to b`.
   - Requirements: member or extension function with one parameter and `infix` modifier.
   - Example:
     ```kotlin
     infix fun Int.timesStr(s: String) = s.repeat(this)
     println(3 timesStr "Hi") // HiHiHi
     ```
   - **Important:** Improves readability in DSLs and builder APIs.

6. **How does operator overloading work?**
   - Mark functions with `operator` and implement predefined names (`plus`, `get`, `invoke`, etc.).
   - Example:
     ```kotlin
     data class Vec(val x:Int, val y:Int) { operator fun plus(o: Vec) = Vec(x+o.x, y+o.y) }
     println(Vec(1,2) + Vec(3,4))
     ```
   - **Important:** Only predefined operator names are allowed; keep semantics intuitive.

7. **What is tail recursion and how to use `tailrec`?**
   - `tailrec` enables the compiler to optimize tail-recursive calls into loops to avoid stack overflow.
   - Requirement: the recursive call must be the last operation in the function.
   - Example:
     ```kotlin
     tailrec fun factorial(n: Int, acc: Long = 1): Long =
         if (n <= 1) acc else factorial(n-1, acc * n)
     ```
   - **Important:** Not all recursive patterns can be made tail-recursive.

8. **What are local functions and when should you use them?**
   - Functions declared inside other functions; they can capture local variables and help structure complex logic.
   - Example:
     ```kotlin
     fun validate(input: String) {
         fun isAlpha(s: String) = s.all { it.isLetter() }
         require(isAlpha(input)) { "Invalid" }
     }
     ```
   - **Important:** Good for encapsulating helper logic and reducing top-level pollution.

9. **Explain lambda syntax, `it`, and labeled returns.**
   - Single-parameter lambdas can use implicit `it`.
   - Use labels and `return@label` to exit the lambda rather than the outer function.
   - Example:
     ```kotlin
     listOf(1,2,3).forEach {
         if (it == 2) return@forEach
         println(it)
     }
     ```
   - **Important:** Non-local `return` from lambdas is allowed only in inlined lambdas.

10. **How do reified generics work with inline functions?**
    - `reified` lets you access the concrete type parameter at runtime, but only in `inline` functions.
    - Example:
      ```kotlin
      inline fun <reified T> Gson.fromJson(json: String) = this.fromJson(json, T::class.java)
      ```
    - **Important:** Use for type checks, reflection-free serialization helpers, or generic instance creation.

11. **What are function types and type aliases for functions?**
    - Function types look like `(A, B) -> R` and can be stored in variables or passed around.
    - Use `typealias` to simplify complex function signatures.
    - Example:
      ```kotlin
      typealias Handler = (Request) -> Response
      val h: Handler = { req -> Response(200) }
      ```

12. **What is SAM-conversion and when is it useful?**
    - Kotlin can convert a single-abstract-method Java interface into a lambda when calling from Kotlin.
    - Useful for Java interop and concise code using listeners or executors.

13. **How do default parameters, named arguments, and varargs work?**
    - Default parameters let callers omit arguments; named args improve clarity.
    - `vararg` allows passing variable number of arguments and spreads with `*`.
    - Example:
      ```kotlin
      fun log(msg: String, level: Int = 1, vararg tags: String) {}
      log("Hi", tags = *arrayOf("ui","debug"))
      ```

14. **How to handle nullable function types and invoke them safely?**
    - Function types can be nullable: `(() -> Unit)?` and invoked with `?.invoke()`.
    - Example:
      ```kotlin
      var onClick: (() -> Unit)? = null
      onClick?.invoke()
      ```

15. **What are common best practices for functions in Kotlin?**
    - Prefer small, single-responsibility functions.
    - Use `inline` for performance-critical small HOFs, but measure impact.
    - Prefer extension functions for utility behavior without inheritance.
    - Use named args and default params for clarity.

16. **Common pitfalls and interview follow-ups**
    - Pitfall: overusing `inline`, causing bytecode bloat.
    - Pitfall: expecting extension functions to be virtual—dispatch is static.
    - Follow-ups: show how `crossinline` prevents non-local returns, demonstrate `reified` use-cases, or implement a small DSL using infix/extension functions.