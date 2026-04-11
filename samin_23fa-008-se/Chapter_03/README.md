# Chapter03: Process-Based Parallel Palindrome 

This chapter demonstrates how Python’s **`multiprocessing`** module works using a real-world string example — checking whether words are palindromes using the helper function **`reverse_and_check_palindrome()`** from `reversed_string.py`.

---

##  1. reversed_string.py

### **Purpose**

Defines the core function that reverses a string and checks if it’s a palindrome.
Serves as the base computational task for all multiprocessing examples.

### **Key Code**

```python
def reverse_and_check_palindrome(s):
    reversed_s = s[::-1]
    is_palindrome = s == reversed_s
    return reversed_s, is_palindrome
```

### **Sample Output**

```
Original strings: ['madam', 'apple', 'racecar', 'python', 'level']

Results:
madam → madam | Palindrome: True
apple → elppa | Palindrome: False
racecar → racecar | Palindrome: True
python → nohtyp | Palindrome: False
level → level | Palindrome: True
```

### **Observation**

The function correctly reverses strings and identifies palindromes.
This function is imported and used in all subsequent multiprocessing examples.

---

##  2. naming_processes.py

### **Purpose**

To demonstrate how to create and name processes explicitly and observe their execution when performing palindrome checks.

### **Key Code**

```python
process_with_name = multiprocessing.Process(
    name="Palindrome Process 1",
    target=myFunc
)
process_with_default_name = multiprocessing.Process(
    target=myFunc
)
```

### **Sample Output**

```
Starting process name = Palindrome Process 1

Original strings: ['madam', 'apple', 'racecar', 'python', 'level']

Results:
madam → madam | Palindrome: True
apple → elppa | Palindrome: False
racecar → racecar | Palindrome: True
python → nohtyp | Palindrome: False
level → level | Palindrome: True
Starting process name = Process-2
...
Exiting process name = Palindrome Process 1
```

### **Observation**

Two processes were started — one explicitly named and one with a default name.
Both executed the palindrome task concurrently, producing identical outputs.

---

##  3. spawning_processes.py

### **Purpose**

To show how multiple processes can be created dynamically in a loop, each performing independent palindrome checks.

### **Key Code**

```python
for i in range(6):
    process = multiprocessing.Process(target=myFunc, args=(i,))
    process.start()
    process.join()
```

### **Sample Output**

```
Process number: 0 started
Process 0: madam → madam | Palindrome: True
Process number: 0 finished

Process number: 1 started
Process 1: madam → madam | Palindrome: True
Process number: 1 finished
...
Process number: 5 finished
```

### **Observation**

Six processes were spawned sequentially.
Each process handled a variable number of strings depending on its index.
Palindrome detection scaled correctly across all instances.

---

##  4. killing_processes.py

### **Purpose**

To demonstrate how to start, terminate, and join a process performing palindrome checking.

### **Key Code**

```python
p.start()
print('Process running:', p.is_alive())
time.sleep(2)
if p.is_alive():
    p.terminate()
p.join()
print('Process joined:', p.is_alive())
```

### **Sample Output**

```
Process before execution: <Process name='Process-1' ...> False

Results:
madam → madam | Palindrome: True
apple → elppa | Palindrome: False
racecar → racecar | Palindrome: True
python → nohtyp | Palindrome: False
level → level | Palindrome: True
Finished palindrome process
Process joined: ... False
Process exit code: 0
```

### **Observation**

The process executed and completed successfully before being joined.
Exit code `0` confirms successful execution and clean termination.

---

##  5. run_background_processes_no_daemons.py

### **Purpose**

To differentiate between background (daemon) and foreground (non-daemon) processes when running palindrome checks.

### **Key Code**

```python
background_process.daemon = False
no_background_process.daemon = False
```

### **Sample Output**

```
Starting background_process
background_process: madam → madam | Palindrome: True
...
Starting NO_background_process
NO_background_process: noon → noon | Palindrome: True
...
Exiting background_process
Exiting NO_background_process
```

### **Observation**

Both processes executed independently and printed their results.
Non-daemon processes completed fully, showing that daemonization did not interrupt execution.

---

## 6. run_background_processes.py

### **Purpose**

To demonstrate daemon vs. non-daemon process behavior and timing when both execute palindrome checks concurrently.

### **Key Code**

```python
background_process.daemon = True
no_background_process.daemon = False
```

### **Sample Output**

```
Starting background_process
background_process: madam → madam | Palindrome: True
Starting NO_background_process
NO_background_process: python → nohtyp | Palindrome: False
...
Exiting NO_background_process
Main process finished.  Ended at 1.22s
```

### **Observation**

The daemon process terminated automatically when the main process ended, while the non-daemon process completed normally.
Execution time demonstrates main-process dependency of daemons.

---

##  7. processes_barrier.py

### **Purpose**

To demonstrate synchronization between processes using **`Barrier`** and **`Lock`** while checking palindromes.

### **Key Code**

```python
synchronizer = Barrier(2)
serializer = Lock()
Process(name='P1 - with_barrier', target=test_with_barrier, args=(synchronizer, serializer)).start()
```

### **Sample Output**

```
 P4 - without_barrier started (no barrier):
noon → noon | Palindrome: True
...
[2025-11-08 01:42:12.749957] P2 - with_barrier started:
madam → madam | Palindrome: True
...
[2025-11-08 01:42:12.750012] P1 - with_barrier started:
madam → madam | Palindrome: True
...
```

### **Observation**

Processes using barriers waited for each other before proceeding, ensuring synchronized starts.
The lock ensured clean, non-overlapping output printing.

---

##  8. process_pool.py

### **Purpose**

To demonstrate parallel palindrome checking using a **`multiprocessing.Pool`** for efficient concurrent execution.

### **Key Code**

```python
pool = multiprocessing.Pool(processes=4)
pool_outputs = pool.map(reverse_and_check_palindrome, strings)
```

### **Sample Output**

```
=== Process Pool Palindrome Results ===
madam → madam | Palindrome: True
apple → elppa | Palindrome: False
racecar → racecar | Palindrome: True
python → nohtyp | Palindrome: False
noon → noon | Palindrome: True
rotor → rotor | Palindrome: True
banana → ananab | Palindrome: False
```

### **Observation**

Workload was evenly distributed among four processes.
Each process computed palindrome checks concurrently, significantly improving efficiency.

---

##  Summary Table

| Script                                 | Purpose                                 | Success | Key Observation                                        |
| :------------------------------------- | :-------------------------------------- | :-----: | :----------------------------------------------------- |
| reversed_string.py                     | Define palindrome function              |    ✅    | Function correctly reverses and compares strings       |
| naming_processes.py                    | Create and name processes               |    ✅    | Both named and unnamed processes executed successfully |
| spawning_processes.py                  | Spawn multiple processes in loop        |    ✅    | Scaled palindrome checks across sequential processes   |
| killing_processes.py                   | Start, terminate, and join processes    |    ✅    | Proper process lifecycle management confirmed          |
| run_background_processes_no_daemons.py | Compare background and foreground tasks |    ✅    | Both non-daemons completed independently               |
| run_background_processes.py            | Demonstrate daemon vs non-daemon timing |    ✅    | Daemon stopped early; non-daemon completed fully       |
| processes_barrier.py                   | Synchronize multiple processes          |    ✅    | Barrier ensured coordinated execution                  |
| process_pool.py                        | Execute tasks in parallel using Pool    |    ✅   | Efficient workload distribution confirmed              |

---

##  Conclusion

Through palindrome-based tasks, this chapter demonstrated key multiprocessing concepts:

* **Process creation, naming, and termination**
* **Sequential vs. parallel execution**
* **Daemon and non-daemon behavior**
* **Process synchronization using barriers and locks**
* **Parallel execution using process pools**

---

## Overall Learning

Python’s `multiprocessing` module provides powerful tools for **parallelizing CPU-bound tasks** like palindrome checking.
Understanding **naming, synchronization, daemonization, and pooling** is essential to designing efficient, scalable multiprocessing applications.
