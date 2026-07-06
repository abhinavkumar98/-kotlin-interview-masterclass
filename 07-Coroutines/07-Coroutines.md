````markdown
# 07. Coroutines — Questions & Answers

1. **What is a coroutine in Kotlin?**
   - A coroutine is a lightweight, suspendable computation. It enables asynchronous and non-blocking code using `suspend` functions and coroutine builders.
   - Coroutines are scheduled by `CoroutineDispatcher` and run inside a `CoroutineScope`.

2. **Coroutine builders: `launch` vs `async`**
   - `launch` starts a coroutine that returns a `Job` and is used for fire-and-forget work.
   - `async` starts a coroutine that returns a `Deferred<T>` and is used when you need a result (use `await()` to get it).

3. **Dispatchers: `Dispatchers.Main`, `Default`, `IO`, `Unconfined`**
   - `Main`: UI thread (Android/JS); `Default`: CPU-bound work; `IO`: blocking IO optimized dispatcher; `Unconfined`: starts in caller thread and resumes in appropriate thread — use sparingly.

4. **CoroutineScope and structured concurrency**
   - `CoroutineScope` binds coroutines to a lifecycle; canceling the scope cancels its coroutines.
   - Structured concurrency requires launching coroutines inside a scope so parent/child relationships ensure predictable cancellation and error propagation.

5. **Job, SupervisorJob, and exception handling**
   - `Job` represents coroutine lifecycle and cancellation.
   - `SupervisorJob` isolates failures: child failure does not cancel siblings; used where independent child failure is acceptable.
   - Use `CoroutineExceptionHandler` for top-level uncaught exceptions; prefer `try/catch` inside coroutines for fine-grained handling.

6. **Cancellation and cooperative cancellation**
   - Cancellation is cooperative: suspend functions check for cancellation and throw `CancellationException` when cancelled.
   - Ensure long-running loops call a cancellable suspend function (e.g., `yield()`, `delay()`, or check `isActive`).

7. **`suspend` functions and blocking**
   - `suspend` marks a function that can suspend without blocking a thread.
   - Do not call blocking APIs inside coroutines; wrap blocking calls with `withContext(Dispatchers.IO)` or use non-blocking equivalents.

8. **`withContext` and switching dispatchers**
   - `withContext(dispatcher) { ... }` switches coroutine context for the block and returns the result.
   - Use it to confine blocking or CPU-bound work to appropriate dispatchers.

9. **`launch` exception propagation vs `async`**
   - Exceptions in `launch` are delivered to its `CoroutineExceptionHandler` or parent scope immediately.
   - Exceptions in `async` are captured and rethrown when `await()` is called.

10. **Channels, Mutex, and shared mutable state**
    - `Channel` is a coroutine-friendly queue for producer-consumer patterns.
    - `Mutex` provides mutual exclusion for suspending code; use it for fine-grained synchronization.
    - Prefer message-passing (channels/actors) for concurrency instead of shared mutable state when possible.

11. **`select` expression**
    - `select` lets you await multiple suspending operations (channels, deferred) and handle whichever completes first.

12. **`supervisorScope` vs `coroutineScope`**
    - `coroutineScope` cancels all children if any child fails.
    - `supervisorScope` prevents child failure from cancelling siblings; use with caution to avoid unobserved failures.

13. **Performance considerations**
    - Coroutines are lightweight (millions can be created) but creating many `Job` hierarchies or allocating many `Deferred` excessively can cost memory.
    - Avoid switching dispatchers too often; batch small tasks when possible.

14. **Testing coroutines**
    - Use `runTest` from kotlinx-coroutines-test to control virtual time and test suspending code deterministically.
    - Use `TestCoroutineScheduler` and `StandardTestDispatcher` for more control of scheduling.

15. **Common interview pitfalls**
    - Blocking the main thread inside a coroutine (e.g., `Thread.sleep`) instead of `delay()`.
    - Forgetting to cancel scopes or jobs, leading to leaks.
    - Misunderstanding exception propagation for `async` vs `launch`.

16. **Example: structured concurrency pattern**
    ```kotlin
    suspend fun fetchAll(ids: List<String>): List<Result> = coroutineScope {
      ids.map { id -> async(Dispatchers.IO) { fetch(id) } }.awaitAll()
    }
    ```

17. **Interview follow-ups**
    - Implement a safe retry with exponential backoff using suspending retries.
    - Compare channels and flows for streaming data and discuss backpressure handling.
18. **Additional practical examples**
    - Basic `launch` vs `async` example:
    ```kotlin
    fun main() = runBlocking {
      launch { // fire-and-forget
        delay(100)
        println("Launched task finished")
      }

      val deferred = async { // returns Deferred
        delay(50)
        "result"
      }

      println("Async result: ${deferred.await()}")
    }
    ```

    - Cancellation with cooperative checks and cleanup:
    ```kotlin
    suspend fun doWork() {
      try {
        repeat(1000) { i ->
          if (!currentCoroutineContext().isActive) return
          delay(100)
          println("Working $i")
        }
      } finally {
        // cleanup
        println("Cleanup on cancellation")
      }
    }

    fun main() = runBlocking {
      val job = launch { doWork() }
      delay(250)
      job.cancelAndJoin()
    }
    ```

    - `withTimeout` / `withTimeoutOrNull` example:
    ```kotlin
    suspend fun shortTask() { delay(200) }

    fun main() = runBlocking {
      val r = withTimeoutOrNull(100) { // returns null on timeout
        shortTask()
        "done"
      }
      println(r) // null
    }
    ```

    - `supervisorScope` example (isolate child failure):
    ```kotlin
    fun main() = runBlocking {
      supervisorScope {
        val child1 = launch {
          throw RuntimeException("fail")
        }
        val child2 = launch {
          delay(100)
          println("child2 completed")
        }
        // child2 still runs despite child1 failure
      }
    }
    ```

    - Producer/consumer with `Channel`:
    ```kotlin
    fun main() = runBlocking {
      val channel = Channel<Int>(capacity = 10)

      launch { // producer
        repeat(5) { i ->
          channel.send(i)
          println("sent $i")
        }
        channel.close()
      }

      launch { // consumer
        for (v in channel) {
          println("received $v")
        }
      }
    }
    ```

    - `Mutex` example for suspending mutual exclusion:
    ```kotlin
    val mutex = Mutex()
    var counter = 0

    suspend fun inc() {
      mutex.withLock {
        counter++
      }
    }

    fun main() = runBlocking {
      val jobs = List(1000) { launch { repeat(100) { inc() } } }
      jobs.forEach { it.join() }
      println(counter) // 100000
    }
    ```

19. **Advanced patterns & tips**
    - Prefer message-passing (channels/actors) to reduce shared mutable state.
    - Use `Dispatchers.IO` for blocking I/O and `Dispatchers.Default` for CPU work.
    - Keep coroutine lifecycles tied to scopes that represent component lifetimes (UI, service, etc.).
````
