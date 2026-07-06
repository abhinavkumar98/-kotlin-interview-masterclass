1. **What is a class in Kotlin and how does it differ from a Java class?**
   - A Kotlin class is a blueprint for objects and can declare properties, methods, constructors, and nested types.
   - **Important:** Kotlin classes are final by default, so you must use `open` to allow inheritance.
   - Example:
     ```kotlin
     class Person(val name: String, var age: Int) {
         fun greet() = "Hello, my name is $name and I am $age years old."
     }
     ```
   - Kotlin simplifies class declarations by combining primary constructor parameters and property declarations.

2. **What is the difference between primary and secondary constructors in Kotlin?**
   - The primary constructor is part of the class header and can directly initialize properties.
   - Secondary constructors are defined inside the class body using `constructor(...)` and must delegate to the primary constructor.
   - Example:
     ```kotlin
     class User(val id: Int, var name: String) {
         constructor(id: Int) : this(id, "Unknown")
     }
     ```
   - **Important:** Use secondary constructors only when needed; the primary constructor is usually enough.

3. **What is an `init` block and when is it executed?**
   - The `init` block is executed immediately after the primary constructor finishes.
   - It is useful for validation, logging, or side effects during object creation.
   - Example:
     ```kotlin
     class Account(val number: String, balance: Double) {
         val balance = balance.coerceAtLeast(0.0)

         init {
             require(number.isNotBlank()) { "Account number cannot be blank" }
         }
     }
     ```
   - **Important:** A class can have multiple `init` blocks, and they run in the order they appear.

4. **Explain `data class`. Why and when should you use it?**
   - A `data class` automatically provides `equals()`, `hashCode()`, `toString()`, `copy()`, and `componentN()` functions.
   - Use it for classes that mainly hold data, like models or DTOs.
   - Example:
     ```kotlin
     data class Book(val title: String, val author: String, val pages: Int)
     val book = Book("Kotlin", "JetBrains", 300)
     println(book) // Book(title=Kotlin, author=JetBrains, pages=300)
     ```
   - **Important:** `data class` requires at least one `val` or `var` in the primary constructor.

5. **What are `object` declarations in Kotlin and how do they relate to singletons?**
   - An `object` declaration creates a singleton instance automatically.
   - It is useful for single-instance managers, utilities, or shared state.
   - Example:
     ```kotlin
     object Logger {
         fun log(message: String) = println("[LOG] $message")
     }
     Logger.log("Application started")
     ```
   - **Important:** No class instance is needed; `Logger` is itself the instance.

6. **What is a `companion object` and when should you use it?**
   - A `companion object` is a singleton object tied to a class and can hold factory methods or constants.
   - It allows calling members using the class name, similar to static members in Java.
   - Example:
     ```kotlin
     class Circle(val radius: Double) {
         companion object {
             const val PI = 3.14159
             fun create(radius: Double) = Circle(radius)
         }
     }
     val circle = Circle.create(5.0)
     println(Circle.PI)
     ```
   - **Important:** `companion object` members can implement interfaces and be accessed via the class name.

7. **What is the difference between `object` expressions and `object` declarations?**
   - `object` declarations create a singleton with a global scope.
   - `object` expressions create anonymous objects at runtime, often used for single-use callbacks or interfaces.
   - Example:
     ```kotlin
     val listener = object : ClickListener {
         override fun onClick() { println("Clicked") }
     }
     ```
   - **Important:** Anonymous objects can access variables from the enclosing scope.

8. **How do `sealed classes` work and why are they useful?**
   - A `sealed class` restricts subclassing to the same file, enabling exhaustive `when` checks.
   - They are often used for representing state machines or result types.
   - Example:
     ```kotlin
     sealed class Result {
         data class Success(val data: String) : Result()
         object Failure : Result()
     }
     fun handle(result: Result) = when (result) {
         is Result.Success -> println(result.data)
         Result.Failure -> println("Error")
     }
     ```
   - **Important:** The compiler knows all subclasses, so `when` expressions can be exhaustive without an `else` branch.

9. **What are `sealed interface` and `sealed class` differences?**
   - `sealed interface` is like a sealed class but can be implemented by multiple classes and interfaces.
   - `sealed class` can hold state and may define constructors, while interfaces cannot.
   - Example:
     ```kotlin
     sealed interface NetworkEvent
     data class Connected(val address: String) : NetworkEvent
     object Disconnected : NetworkEvent
     ```
   - **Important:** Both limit implementations to the same package/file unless using `sealed` with `@JvmSealed` in Kotlin 1.9+.

10. **What is the purpose of `inner` classes in Kotlin?**
    - An `inner` class holds a reference to the outer class instance and can access its members.
    - By default, nested classes are static-like and cannot access outer members unless marked `inner`.
    - Example:
      ```kotlin
      class Outer(val name: String) {
          inner class Inner {
              fun showOuter() = "Outer name is $name"
          }
      }
      val outer = Outer("Kotlin")
      println(outer.Inner().showOuter())
      ```
    - **Important:** Use `inner` only when the nested class logically needs the outer object.

11. **What is the difference between `class` and `object` when defining constants or utilities?**
    - `class` requires instantiation; `object` does not.
    - For utilities or constant holders, `object` is simpler and idiomatic.
    - Example:
      ```kotlin
      object Constants {
          const val MAX_RETRIES = 3
      }
      println(Constants.MAX_RETRIES)
      ```
    - **Important:** `object` can also implement interfaces and hold initialization logic.

12. **How do visibility modifiers work in Kotlin classes?**
    - `public` is default and accessible everywhere.
    - `private` restricts access to the class or file, depending on the declaration.
    - `protected` is visible in subclasses only (not top-level declarations).
    - `internal` is visible within the same module.
    - Example:
      ```kotlin
      open class Vehicle {
          private val id = 1
          protected open fun drive() = println("Driving")
      }
      class Car : Vehicle() {
          override fun drive() = println("Car driving")
      }
      ```
    - **Important:** `protected` is not available for top-level functions or properties.

13. **How do you implement inheritance in Kotlin classes?**
    - Use `open` on the base class and `:` to extend it.
    - Override methods or properties with `override` and use `super` when needed.
    - Example:
      ```kotlin
      open class Animal(val name: String) {
          open fun speak() = println("$name makes a sound")
      }
      class Dog(name: String) : Animal(name) {
          override fun speak() = println("$name barks")
      }
      ```
    - **Important:** `final` is implicit for classes and members unless `open` is used.

14. **What is `lateinit` and when can it be used in classes?**
    - `lateinit` allows non-null `var` properties to be initialized after object construction.
    - It cannot be used with `val`, primitive types, or nullable properties.
    - Example:
      ```kotlin
      class Service {
          lateinit var config: Config
          fun init(config: Config) { this.config = config }
      }
      ```
    - **Important:** Accessing an uninitialized `lateinit` property throws `UninitializedPropertyAccessException`.

15. **What is a `typealias` and can it be used with class types?**
    - `typealias` creates an alias for an existing type, simplifying long class names or function types.
    - It does not create a new type; it is just a shorthand.
    - Example:
      ```kotlin
      typealias StringMap = Map<String, String>
      val headers: StringMap = mapOf("Content-Type" to "application/json")
      ```
    - **Important:** Use `typealias` to improve readability for complex class or generic types.

16. **How do Kotlin classes support delegation and what is `by` used for?**
    - Kotlin supports interface delegation using `by`, which forwards calls to a delegate instance.
    - This enables reuse without inheritance.
    - Example:
      ```kotlin
      interface Logger {
          fun log(message: String)
      }
      class ConsoleLogger : Logger {
          override fun log(message: String) = println(message)
      }
      class AppLogger(logger: Logger) : Logger by logger
      ```
    - **Important:** Delegation can also be used for property delegation with `by lazy`, `by observable`, and custom delegates.

17. **What are `sealed` classes best used for in Kotlin interview answers?**
    - Representing finite states, result types, and event hierarchies.
    - Providing exhaustive `when` checks so the compiler can enforce all cases.
    - Example:
      ```kotlin
      sealed class AuthState {
          object Unauthenticated : AuthState()
          object Loading : AuthState()
          data class Authenticated(val token: String) : AuthState()
      }
      ```
    - **Important:** Use sealed classes for clear state modeling, especially in UI and domain logic.

18. **What is the difference between `data class` and `value class`?**
    - `data class` generates useful methods for classes with state.
    - `value class` (inline class) wraps a single value and can avoid object allocation in some cases.
    - Example:
      ```kotlin
      @JvmInline
      value class UserId(val value: String)
      ```
    - **Important:** A `value class` can only have one property in the primary constructor.

19. **How can Kotlin `objects` be used to implement thread-safe singletons?**
    - `object` declarations are initialized lazily and safely by the Kotlin runtime.
    - No extra synchronization is needed for normal singleton use.
    - Example:
      ```kotlin
      object ConfigManager {
          val settings = loadSettings()
      }
      ```
    - **Important:** Kotlin singleton initialization is thread-safe by default.

20. **What are `nested` classes in Kotlin and how do they differ from `inner` classes?**
    - A `nested` class is static-like and does not hold a reference to the outer class.
    - An `inner` class has an implicit reference to the outer class and uses `outer@this` to access it.
    - Example:
      ```kotlin
      class Outer {
          class Nested {
              fun info() = "Nested class"
          }
          inner class Inner {
              fun info() = "Inner class in ${this@Outer}"
          }
      }
      ```
    - **Important:** Use `nested` when the inner class does not need outer instance state.
