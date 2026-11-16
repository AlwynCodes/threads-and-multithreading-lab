# Threads and Multithreading Laboratory

## Student Information
- **Name:** Reuyan, Alwyn Jade
- **Section:** B181
- **Date:** 11/16/26

## Laboratory Description
This laboratory demonstrates thread creation, multithreading, thread coordination, and shared-resource management using Python.

## Completed Tasks
1. Single Thread
2. Multiple Threads
3. Thread Synchronization using `join()`
4. Shared Resource Management using `Lock()`

## Learning Outcomes
- Create and execute threads.
- Demonstrate concurrent execution.
- Explain thread coordination.
- Apply locks to protect shared resources.

## Project Structure
```
Threads-and-Multithreading-Lab/
│
├── README.md
├── task1_single_thread.py
├── task2_multiple_threads.py
├── task3_thread_synchronization.py
└── task4_thread_lock.py
```

---

# Guide Question Answers

## Task 1 — Creating and Executing a Single Thread

**1. What is the purpose of `threading.Thread()`?**

Think of `threading.Thread()` as **hiring a worker and giving them a job description**. You tell it which function to run (`target=display_numbers`), but the worker hasn't started yet. It just prepares the thread so it's ready to go. It does **not** start running the function — that's what `start()` is for.

**2. What happens when `start()` is executed?**

This is the moment you **tell the worker to begin**. Python hands the thread over to the operating system, which schedules it to run **at the same time** as your main program. `start()` returns immediately — it doesn't wait for the function to finish. So your main program keeps going while the worker does its job in the background.

**3. What is the purpose of `join()`?**

`join()` is like telling the main program: **"Stop here and wait until that worker is completely done."** Without it, the main program might finish and print "Program completed" before the thread even finishes counting. With `join()`, the main program pauses at that line until the thread is finished, keeping things in order.

**4. What happens if `join()` is removed?**

The main program **won't wait**. It'll keep running immediately after `start()`, print "Program completed.", and possibly exit before the thread finishes printing numbers 1–5. The output order becomes unpredictable. In Python, non-daemon threads usually keep the program alive until they finish, but you lose the guarantee that everything completed in the right order.

---

## Task 2 — Implementing Multiple Threads

**1. How many threads are created in the program?**

**Two worker threads** are created — `t1` (running `task1`) and `t2` (running `task2`). Plus, there's always the **main thread** running the script itself, so the program has **three threads total** running at once.

**2. Do the two threads execute independently?**

**Yes.** Each thread has its own execution path. They don't take turns in a strict order — the OS decides who runs when. You'll see their outputs interleaved (like `Thread 1: 1`, `Thread 2: 1`, `Thread 1: 2`...) because both are making progress at the same time.

**3. Why might the output order change when the program is executed again?**

Because **the operating system's scheduler decides** which thread gets CPU time and when. It's not random, but it's **not predictable** either — it depends on system load, timing, and hundreds of tiny factors. So run the program twice and you might get a different interleaving. This is normal and expected with threads.

**4. What is the difference between sequential and concurrent execution?**

- **Sequential:** Task A runs completely, then Task B starts. One after the other. Like waiting in a single-file line.
- **Concurrent:** Task A and Task B make progress during the same time period, overlapping. Like two people cooking in the same kitchen — both are working at the same time, just on different things.

Multithreading gives us concurrency.

---

## Task 3 — Thread Synchronization Using `join()`

**1. Why does the main program wait when `join()` is used?**

Because `join()` **blocks** the main thread — it literally puts the main program on pause at that line until the worker thread finishes. In the download example, the main program says "I'll wait here until the download is done," then continues. This ensures the download completes **before** the main program moves on.

**2. What happens when `join()` is removed?**

The main program **doesn't wait anymore**. It prints "Main program continues." right after starting the thread, then finishes. The download thread is still running in the background, and "Download completed." appears **after** the main program already finished. The order is no longer guaranteed.

**3. In what situations is thread synchronization necessary?**

Whenever the main program **depends on the results** of a thread before continuing. Examples:
- You need to download a file before processing it.
- You want to save results after all threads finish computing.
- You need to close a file or database connection after all writes are done.
- You want a clean, predictable output order.

---

## Task 4 — Managing Shared Resources Using Thread Locks

**1. What is a shared resource?**

A shared resource is **any data or object that more than one thread can access**. In this lab, it's the `counter` variable — both threads try to increment it. Other examples: a file, a list, a database record, or a network connection. When multiple threads touch the same thing, you need to be careful.

**2. What is a race condition?**

A race condition is a **bug that happens when the result depends on the unpredictable timing of threads**. The classic example: `counter += 1` is actually three steps — **read** the value, **add** 1, **write** it back. If two threads do this at the same time, they can both read the same old value, both add 1, and both write the same new value — so **one update gets lost**. The counter ends up lower than it should be.

**3. What is the purpose of `threading.Lock()`?**

A lock is like a **key to a single-occupancy bathroom**. Only one thread can hold the lock at a time. When a thread enters the `with lock:` block, it grabs the key. Any other thread trying to enter has to **wait outside** until the key is returned. This guarantees that only one thread modifies the shared data at a time — no more lost updates.

**4. What is a critical section?**

The **critical section** is the part of the code that touches the shared resource and **must not be run by two threads at once**. In Task 4, `counter += 1` is the critical section. The lock protects it — anything inside `with lock:` is the critical section.

**5. Why is synchronization important when multiple threads access shared data?**

Without synchronization, threads can **step on each other's toes** — reading stale values, overwriting each other's updates, and producing wrong results. These bugs are especially nasty because they're **intermittent** — the program might work fine 99 times and fail on the 100th. Synchronization makes the behavior **predictable and correct**, no matter how the OS schedules the threads.

---

## Section VII — Final Questions

**1. What is the difference between a process and a thread?**

A **process** is an independent program with its **own memory space** — like a separate house. A **thread** is a unit of execution **inside** a process that **shares memory** with other threads in the same process — like roommates sharing the same house. Processes are isolated and heavier; threads are lightweight and share data (which is why they need locks).

**2. What are the advantages of using multithreading?**

- **Responsiveness:** The UI stays usable while background work happens.
- **Efficiency:** Threads share memory, so creating them is cheaper than creating processes.
- **Better CPU use:** Multiple threads can use multiple cores.
- **Overlapping I/O:** While one thread waits for a file or network, another can compute.
- **Simpler modeling:** Some problems are naturally concurrent (e.g., handling multiple clients).

**3. How does multithreading improve application responsiveness?**

By moving **long-running or blocking tasks** (like downloading a file or reading a large database) to a **background thread**, the main thread stays free to respond to user input. So the app doesn't freeze — you can still click buttons, scroll, and type while the background work continues.

**4. What is the difference between concurrency and parallelism?**

- **Concurrency** = multiple tasks **make progress during overlapping time periods**. They can be interleaved on a single core — it *looks* like they're happening at once.
- **Parallelism** = multiple tasks **literally run at the exact same instant**, which requires multiple CPU cores.

Parallelism is a **subset** of concurrency. All parallel execution is concurrent, but not all concurrent execution is parallel.

**5. Why is a lock necessary when multiple threads access shared data?**

Because operations like `x += 1` are **not atomic** — they're really read-modify-write, and threads can interleave those steps. Without a lock, two threads can read the same value, both increment it, and both write back the same result — one update is lost. A lock ensures only one thread enters the critical section at a time, keeping the data **consistent and correct**.

**6. What might happen if a thread is not properly synchronized?**

- **Race conditions** — unpredictable, incorrect results.
- **Lost updates** — some increments disappear.
- **Corrupted data** — lists, files, or objects end up in invalid states.
- **Deadlocks** — if locks are misused, threads can freeze forever waiting for each other.
- **Hard-to-reproduce bugs** — the program might work 99% of the time and fail randomly.

**7. Can multiple threads execute simultaneously on a single-core processor? Explain.**

**No, not truly simultaneously.** A single-core CPU can only run **one instruction at a time**. But the OS switches between threads **very quickly** (time-slicing), giving the **illusion** of simultaneity. This is **concurrency**, not parallelism. True parallel execution requires multiple cores. (In Python, the GIL further restricts parallel execution of Python bytecode even on multi-core machines.)

**8. How does the operating system manage kernel-level threads?**

The OS kernel **creates, schedules, and switches** kernel threads. It uses a **scheduler** (often preemptive, with priorities) to decide which thread runs on which core and for how long. It handles **context switches** (saving one thread's state and loading another's), manages **blocking and waking** on I/O, and enforces **time slices** so no single thread hogs the CPU. The threading library (like Python's `threading` module) maps user-level threads to these kernel threads.

---

## Tools Used
- Python 3.x
- `threading` module
- GitHub
- Visual Studio Code

## How to Run
```bash
python task1_single_thread.py
python task2_multiple_threads.py
python task3_thread_synchronization.py
python task4_thread_lock.py
```
