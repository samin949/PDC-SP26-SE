# Chapter 01 — Getting Started with Parallel Computing and Python

This chapter covers the foundational concepts of parallel computing and introduces Python as a tool for parallel programming. The code files demonstrate core Python concepts and compare serial vs multithreading vs multiprocessing execution.

---

## Files Overview

| File | Concept | Description |
|------|---------|-------------|
| `dir.py` | Help & Flow | Checks if a number is positive/negative/zero; sums a list using a for loop |
| `flow.py` | Flow Control | Demonstrates `if/elif/else`, `for` loop, and `while` loop |
| `lists.py` | Data Types | Shows usage of lists, dictionaries, tuples, and function references |
| `classes.py` | OOP / Classes | Demonstrates class variables, instance variables, and inheritance |
| `do_something.py` | Functions | Helper function that generates random numbers into a list |
| `file.py` | File I/O | Writes and reads a text file using `open()` |
| `serial_test.py` | Serial Execution | Runs `do_something()` 10 times sequentially and measures time |
| `multithreading_test.py` | Multithreading | Runs `do_something()` across 10 threads and measures time |
| `multiprocessing_test.py` | Multiprocessing | Runs `do_something()` across 10 processes and measures time |
| `thread_and_processes.py` | Combined Comparison | Compares multithreading vs multiprocessing in a single script |

---

## 1. `dir.py` — Help Functions & Basic Flow

### Description
Checks whether a number is positive, negative, or zero using `if/elif/else`. Also sums a list of numbers using a `for` loop.

### Key Points
- Demonstrates Python's `if/elif/else` conditional structure
- Shows iteration over a list with `for`
- Named `dir.py` as it relates to Python's built-in exploration tools (`dir()`, `help()`)

### Sample Output
```
Positive number
The sum is 83
```

---

## 2. `flow.py` — Flow Control

### Description
Covers all three major flow control structures in Python: `if/elif/else`, `for` loop, and `while` loop.

### Key Points
- `if/elif/else` — conditional branching
- `for` loop — iterates over a list to compute sum
- `while` loop — adds natural numbers from 1 to n

### Sample Output
```
Positive number
The sum is 83
The sum is 55
```

---

## 3. `lists.py` — Data Types (Lists, Dicts, Tuples)

### Description
Demonstrates Python's core data structures: lists (mutable), dictionaries (key-value), tuples (immutable), and assigning functions to variables.

### Key Points
- Lists support indexing and modification (`mylist[0] = ...`)
- Negative indexing (`mylist[-1]`) accesses from the end
- Dictionaries store key-value pairs; values can be updated
- Tuples are immutable — cannot be changed after creation
- Functions are first-class objects: `myfunc = len`

### Sample Output
```
yet element 1
3.15
{'Key 1': 'value 1', 2: 3, 'pi': 3.14}
3.15
(1, 2, 3)
3
```

---

## 4. `classes.py` — OOP and Inheritance

### Description
Demonstrates Python classes with class-level variables, instance variables, a method, and inheritance via `AnotherClass`.

### Key Points
- `common` is a class variable (shared across all instances)
- Changing `Myclass.common` affects all instances unless overridden at instance level
- Setting `instance.common = 10` creates an instance-level copy — does not affect other instances
- `AnotherClass` inherits from `Myclass` and reuses `myfunction()`
- Dynamic attributes can be added to instances at runtime: `instance.test = 10`

### Sample Output
```
instance.myfunction(1, 2) 3
instance.common  10
instance2.common  10
instance.common  30
instance2.common  30
instance.common  10
instance2.common  30
instance.common  10
instance2.common  50
hello
instance.myfunction (1, 2)  3
instance.test  10
```

---

## 5. `do_something.py` — Helper Function

### Description
A reusable function that fills a list with `count` random floating-point numbers between 0 and 1. Used by all three performance test scripts.

### Key Points
- Uses `random.random()` to generate floats in range [0.0, 1.0)
- Appends results to a passed-in list
- Imported by `serial_test.py`, `multithreading_test.py`, and `multiprocessing_test.py`

---

## 6. `file.py` — File I/O

### Description
Opens a file in write mode, writes two lines, closes it, reopens in read mode, and prints the content.

### Key Points
- `open('test.txt', 'w')` — creates or overwrites a file
- `f.write(...)` — writes a string to the file
- `f.read()` — reads entire file content as a string
- Always call `f.close()` after use, or use `with open(...) as f:` for safer handling

### Sample Output
```
first line of file 
second line of file 
```

---

## 7. `serial_test.py` — Serial Execution

### Description
Runs `do_something()` 10 times in a simple `for` loop with no threads or processes. Measures total execution time as a baseline.

### Key Points
- Each iteration creates a new list and fills it with 10 million random numbers
- Completely sequential — one task finishes before the next begins
- Used as the performance baseline for comparison

### Sample Output
```
List processing complete.
serial time= 38.45
```

---

## 8. `multithreading_test.py` — Multithreading

### Description
Creates 10 threads, each running `do_something()` with 10 million numbers, and measures total time.

### Key Points
- Uses `threading.Thread(target=...)`
- Due to Python's **GIL (Global Interpreter Lock)**, CPU-bound threads cannot run truly in parallel
- Time is similar to or even slightly worse than serial for this CPU-bound task
- Threading is better suited for **I/O-bound** tasks (file reads, network calls)

### Sample Output
```
List processing complete.
multithreading time= 40.12
```

---

## 9. `multiprocessing_test.py` — Multiprocessing

### Description
Creates 10 separate processes, each running `do_something()` with 10 million numbers. Each process has its own memory and Python interpreter.

### Key Points
- Uses `multiprocessing.Process(target=..., args=...)`
- Bypasses the GIL — achieves true parallelism on multi-core systems
- Each process has its own `out_list` — results are not shared back to the main process
- Significantly faster than serial and threading for CPU-bound tasks

### Sample Output
```
List processing complete.
multiprocesses time= 9.87
```

---

## 10. `thread_and_processes.py` — Combined Comparison

### Description
Runs multithreading and multiprocessing back-to-back in a single script for direct side-by-side comparison. Serial section is included but commented out.

### Key Points
- Serial section is wrapped in `""" ... """` — uncomment to enable it
- Defines its own `do_something()` locally (independent of `do_something.py`)
- Directly shows the performance gap between threading and multiprocessing
- Best file to run when demonstrating the GIL's impact in class

### Sample Output
```
List processing complete.
threading time= 41.23
List processing complete.
processes time= 10.05
```

---

## Performance Comparison Summary

| Method | GIL Affected? | True Parallelism? | Best For | Expected Speed |
|--------|--------------|-------------------|----------|----------------|
| Serial | N/A | No | Baseline | Slowest |
| Multithreading | Yes | No (CPU-bound) | I/O-bound tasks | Similar to serial |
| Multiprocessing | No | Yes | CPU-bound tasks | Fastest |

> **Key Insight:** For CPU-bound tasks like generating millions of random numbers, multiprocessing is significantly faster than multithreading because it bypasses Python's GIL by using separate processes.

---

## Theory Concepts Covered

- **Flynn's Taxonomy** — SISD, MISD, SIMD, MIMD architectures
- **Memory Organization** — Shared memory, distributed memory, NUMA, clusters, heterogeneous systems
- **Parallel Programming Models** — Shared memory, multithread, message passing, data-parallel
- **Program Design Steps** — Task decomposition, assignment, agglomeration, static/dynamic mapping
- **Performance Metrics** — Speedup `S(p) = T(1)/T(p)`, efficiency `E(p) = S(p)/p`, Amdahl's Law, Gustafson's Law
- **GIL** — Why Python threads don't speed up CPU-bound code

---

## How to Run

```bash
python dir.py
python flow.py
python lists.py
python classes.py
python file.py
python serial_test.py
python multithreading_test.py
python multiprocessing_test.py
python thread_and_processes.py
```

> **Note:** `serial_test.py`, `multithreading_test.py`, and `multiprocessing_test.py` all require `do_something.py` to be present in the same folder.