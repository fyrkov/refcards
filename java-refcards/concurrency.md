#### BLOCKED vs WAITING
Blocked <- thread is waiting to acquire the monitor, i.e. entering `synchronized`\
Waiting <- `Object.wait()` or `Thread.sleep()`

#### On interruption
- There is as `interrupted` status
- If a method is declared throwing `InterruptedException`
  - it is a blockng method 
  - supports stopping blocking, i.e. leaving `BLOCKED|WAITING` state.
- A thread is interrupted by some other thread through a call to `Thread.interrupt()`
  - In general, `interrupt()` merely sets the thread's `interrupted` status. The code running in that thread supposed to poll the interrupted status and handle it appropriately, usually throwing `InterruptedException` in the end.
  - If the thread is blocking by `Object.wait()`, `Thread.sleep()`, `join()`, `java.util.concurrent` structures, IO operations - it unblocks, interrupt status gets cleared and an `InterruptedException` is thrown.
- `InterruptedException` catch block can
  - re-throw (propagate)
  - restore status with `Thread.currentThread().interrupt()` (for Runnables)
- `Thread.interrupted()` checks status and resets it while `Thread.isInterrupted()` does not affect current status.
- The `interrupted` status gets reset by design because Thread should be able to escape call stack of methods step by step, manually handling interruption in every of them.
- Bad practices:
  - swallow `InterruptedException`
  - re-throwing `RuntimeException` in place of `InterruptedException`

  see https://www.ibm.com/developerworks/java/library/j-jtp05236/index.htm


#### Synchronizers

- `CountDownLatch` - one-time, not re-usable. For waiting for some event
- `Semaphore` - to control access to resources. Has a set of N permits. Permits are not associated with threads, i.e. `acquire()` can be in one thread and `release()` can be called from another. In simplest (N=1) case works like non-reentrant `mutex`.
- `Barrier` - allows syncing a fixed number of threads at some point. Works like "we meet at 6PM in the mall and then all go..." Latches are for waiting for events, barriers for waiting other threads.

#### Executors
Executor - primary abstraction for decoupling task submission and task execution.\
`ExecutorService extends Executor = Thread Pool + TaskQueue`.\
Thread pool types:
- `Executors.newFixedThreadPool` - adds up to N worker threads, does not shrink, adds new thread if one dies
- `Executors.newCachedThreadPool` - dynamically adds/removes worker threads, pool has no bounds
- `Executors.newSingleThreadPool` - re-creates a worker thread if one dies
- `Executors.newScheduledThreadPool` - for periodic tasks

`CompletionService = Executor + BlockingQueue`.

