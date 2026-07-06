1. **What is delegation in Kotlin and why is it useful?**
   - Delegation is a design pattern where an object hands off responsibility for a task to another object (the delegate).
   - Kotlin provides first-class support for delegation with `by` for class and property delegation, enabling composition over inheritance.

2. **What is class delegation (`by`) and how do you use it?**
   - Class delegation forwards implementation of an interface to a provided delegate instance using `by`.
   - Example:
     ```kotlin
     interface Logger { fun log(msg: String) }
     class ConsoleLogger : Logger { override fun log(msg: String) = println(msg) }
     class AppLogger(logger: Logger) : Logger by logger
     ```
   - **Important:** The delegating class can still override specific members to customize behavior.

3. **When to prefer delegation over inheritance?**
   - Prefer delegation when you need to reuse behavior without forming a tight coupling of inheritance, or when multiple behaviors must be combined.
   - Delegation supports better encapsulation and avoids the fragile base class problem.

4. **What is property delegation and how does `by` work for properties?**
   - Property delegation allows the getter/setter behavior to be delegated to a helper object that implements `getValue` and `setValue`.
   - Syntax: `var prop by delegate`.
   - Example (lazy):
     ```kotlin
     val value by lazy { expensiveInit() }
     ```

5. **`Lazy` delegate — behavior and thread-safety**
   - `lazy {}` initializes on first access and caches the value.
   - It supports `LazyThreadSafetyMode` options: `SYNCHRONIZED` (default), `PUBLICATION`, and `NONE`.
   - Use `NONE` for single-threaded cases to avoid synchronization overhead.

6. **`observable` and `vetoable` delegates (standard library)**
   - `observable(initial) { prop, old, new -> ... }` runs a callback after a property value changes.
   - `vetoable(initial) { prop, old, new -> Boolean }` allows rejecting a change by returning `false`.
   - Useful for validation, UI updates, or debug logging.

7. **How to implement a custom property delegate?**
   - Provide a class with `operator fun getValue(thisRef, property)` and (for `var`) `operator fun setValue(thisRef, property, value)`.
   - Example:
     ```kotlin
     class TrimmedStringVar(var value: String) {
         operator fun getValue(thisRef: Any?, prop: KProperty<*>) = value
         operator fun setValue(thisRef: Any?, prop: KProperty<*>, v: String) { value = v.trim() }
     }
     class Person { var name by TrimmedStringVar("") }
     ```

8. **Delegation and interfaces with state**
   - Delegates can hold state; ensure they are used carefully when shared between multiple owners to avoid unexpected coupling.

9. **Combining multiple delegates**
   - A class can delegate different interfaces to different instances, enabling composition of behaviors.
   - Example:
     ```kotlin
     class Composite(a: A, b: B) : A by a, B by b
     ```

10. **Performance considerations**
    - Class delegation adds a small indirection cost compared to direct implementation but often negligible compared to design benefits.
    - Property delegates may allocate objects; prefer `val`/`lazy` when caching is required.

11. **Common pitfalls**
    - Sharing a mutable delegate instance across multiple owners can introduce shared-state bugs.
    - Expecting delegates to be transparent: delegates change where logic runs and can affect stack traces and serialization.

12. **Testing with delegation**
    - Use test doubles (mocks/fakes) as delegate instances to verify interactions easily.

13. **Practical examples**
    - Configuration loader: delegate `val config by lazy { loadConfig() }`.
    - Validation: `var email by observable("") { _, _, new -> validateEmail(new) }`.

14. **Interview follow-ups**
    - Implement a thread-safe cache using a custom delegate.
    - Demonstrate combining multiple interface delegates and overriding a delegated method.

15. **Best practices**
    - Keep delegates small and single-responsibility.
    - Avoid sharing mutable delegates unless intentionally coordinating state.
    - Prefer `val` delegates for cached computed values.