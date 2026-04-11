# ======================================================================
# complete_palindrome_demo.py
# Run this file to see all multiprocessing concepts in action
# ======================================================================

import multiprocessing
import time
import datetime
from multiprocessing import Barrier, Lock, Pool, Queue
import os

# ==================== CORE FUNCTION ====================

def reverse_and_check_palindrome(s):
    """Reverse string and check if palindrome"""
    reversed_s = s[::-1]
    is_palindrome = (s == reversed_s)
    return reversed_s, is_palindrome

# ==================== DEMO 1: PROCESS NAMING ====================

def demo_naming():
    print("\n" + "█"*60)
    print("█ DEMO 1: PROCESS NAMING")
    print("█"*60)
    
    def worker():
        strings = ['madam', 'apple', 'racecar']
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            name = multiprocessing.current_process().name
            print(f"  [{name}] {s} → {rev} | Palindrome: {is_pal}")
    
    # Named process
    p1 = multiprocessing.Process(name="🎯 Custom-Worker", target=worker)
    # Auto-named process
    p2 = multiprocessing.Process(target=worker)
    
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    print("\n  ✅ Named and auto-named processes executed successfully\n")

# ==================== DEMO 2: SPAWNING MULTIPLE PROCESSES ====================

def demo_spawning():
    print("\n" + "█"*60)
    print("█ DEMO 2: SPAWNING MULTIPLE PROCESSES")
    print("█"*60)
    
    def worker(pid):
        strings = ['madam', 'racecar', 'noon']
        print(f"  🔄 Process {pid} (PID: {os.getpid()}) started")
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            print(f"     P{pid}: {s} → {rev} | Palindrome: {is_pal}")
        print(f"  ✅ Process {pid} finished\n")
    
    processes = []
    for i in range(3):
        p = multiprocessing.Process(target=worker, args=(i,))
        processes.append(p)
        p.start()
    
    for p in processes:
        p.join()
    
    print(f"  ✅ All {len(processes)} processes completed\n")

# ==================== DEMO 3: PROCESS LIFECYCLE ====================

def demo_lifecycle():
    print("\n" + "█"*60)
    print("█ DEMO 3: PROCESS LIFECYCLE (START, TERMINATE, JOIN)")
    print("█"*60)
    
    def long_worker():
        strings = ['madam', 'apple', 'racecar', 'python', 'level']
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            print(f"     {s} → {rev} | Palindrome: {is_pal}")
            time.sleep(0.3)
    
    p = multiprocessing.Process(target=long_worker)
    
    print(f"  📊 Before start - Alive: {p.is_alive()}")
    p.start()
    print(f"  📊 After start - Alive: {p.is_alive()}")
    
    time.sleep(1.5)
    
    if p.is_alive():
        print("  ✂️ Terminating process...")
        p.terminate()
    
    p.join()
    print(f"  📊 After join - Alive: {p.is_alive()}")
    print(f"  📊 Exit code: {p.exitcode}")
    print("  ✅ Lifecycle management successful\n")

# ==================== DEMO 4: DAEMON VS NON-DAEMON ====================

def demo_daemon():
    print("\n" + "█"*60)
    print("█ DEMO 4: DAEMON VS NON-DAEMON PROCESSES")
    print("█"*60)
    
    def daemon_worker():
        strings = ['madam', 'racecar']
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            print(f"     [DAEMON] {s} → {rev} | Palindrome: {is_pal}")
            time.sleep(0.5)
        print("     [DAEMON] This may not print!")
    
    def normal_worker():
        strings = ['python', 'level', 'noon']
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            print(f"     [NORMAL] {s} → {rev} | Palindrome: {is_pal}")
            time.sleep(0.3)
        print("     [NORMAL] This will always print")
    
    daemon = multiprocessing.Process(target=daemon_worker)
    daemon.daemon = True
    
    normal = multiprocessing.Process(target=normal_worker)
    normal.daemon = False
    
    start = time.time()
    daemon.start()
    normal.start()
    
    normal.join()
    
    print(f"\n  ⏱️ Main finished at {time.time() - start:.2f}s")
    print("  ✅ Daemon stopped with main; normal completed fully\n")

# ==================== DEMO 5: BARRIER SYNCHRONIZATION ====================

def demo_barrier():
    print("\n" + "█"*60)
    print("█ DEMO 5: BARRIER SYNCHRONIZATION")
    print("█"*60)
    
    def worker(barrier, lock, wid):
        print(f"  [{datetime.now().strftime('%H:%M:%S.%f')[:-3]}] P{wid} waiting at barrier")
        barrier.wait()
        print(f"  [{datetime.now().strftime('%H:%M:%S.%f')[:-3]}] P{wid} passed barrier")
        
        strings = ['madam', 'racecar', 'level']
        with lock:
            for s in strings:
                rev, is_pal = reverse_and_check_palindrome(s)
                print(f"     P{wid}: {s} → {rev} | Palindrome: {is_pal}")
    
    barrier = Barrier(3)
    lock = Lock()
    
    processes = []
    for i in range(3):
        p = multiprocessing.Process(target=worker, args=(barrier, lock, i))
        processes.append(p)
        p.start()
    
    for p in processes:
        p.join()
    
    print("  ✅ All processes synchronized with barrier\n")

# ==================== DEMO 6: PROCESS POOL ====================

def demo_pool():
    print("\n" + "█"*60)
    print("█ DEMO 6: PROCESS POOL FOR PARALLEL EXECUTION")
    print("█"*60)
    
    test_strings = [
        'madam', 'apple', 'racecar', 'python',
        'level', 'noon', 'rotor', 'banana',
        'civic', 'world', 'radar', 'hello'
    ]
    
    print(f"\n  Testing {len(test_strings)} strings with 4 processes...")
    print("  " + "-"*45)
    
    start = time.time()
    
    with Pool(processes=4) as pool:
        results = pool.map(reverse_and_check_palindrome, test_strings)
    
    elapsed = time.time() - start
    
    for original, (rev, is_pal) in zip(test_strings, results):
        status = "✓" if is_pal else "✗"
        print(f"  {status} {original:10} → {rev:10} | Palindrome: {is_pal}")
    
    print("  " + "-"*45)
    pal_count = sum(1 for _, (_, is_pal) in zip(test_strings, results) if is_pal)
    print(f"  ✅ Completed {len(test_strings)} tasks in {elapsed:.3f}s")
    print(f"  ✅ Found {pal_count} palindromes out of {len(test_strings)}\n")

# ==================== DEMO 7: QUEUE COMMUNICATION ====================

def demo_queue():
    print("\n" + "█"*60)
    print("█ DEMO 7: QUEUE COMMUNICATION")
    print("█"*60)
    
    def producer(queue, strings):
        for s in strings:
            rev, is_pal = reverse_and_check_palindrome(s)
            queue.put((s, rev, is_pal))
            print(f"     [PRODUCER] Processed: {s}")
            time.sleep(0.2)
        queue.put(None)
    
    def consumer(queue):
        while True:
            item = queue.get()
            if item is None:
                break
            s, rev, is_pal = item
            print(f"     [CONSUMER] {s} → {rev} | Palindrome: {is_pal}")
    
    q = Queue()
    strings = ['madam', 'apple', 'racecar', 'python', 'level']
    
    prod = multiprocessing.Process(target=producer, args=(q, strings))
    cons = multiprocessing.Process(target=consumer, args=(q,))
    
    prod.start()
    cons.start()
    
    prod.join()
    cons.join()
    
    print("  ✅ Queue communication successful\n")

# ==================== MAIN EXECUTION ====================

if __name__ == "__main__":
    print("\n" + "="*70)
    print(" 🚀 CHAPTER 03: PROCESS-BASED PARALLEL PALINDROME ")
    print("="*70)
    
    # Run all demonstrations
    demo_naming()
    demo_spawning()
    demo_lifecycle()
    demo_daemon()
    demo_barrier()
    demo_pool()
    demo_queue()
    
    print("="*70)
    print(" 🎉 ALL DEMONSTRATIONS COMPLETED SUCCESSFULLY! 🎉")
    print("="*70)
    print("\n  📚 Key Takeaways:")
    print("  • Processes can be named and managed individually")
    print("  • Daemon processes die when main process exits")
    print("  • Barriers synchronize multiple processes")
    print("  • Pools provide efficient parallel execution")
    print("  • Queues enable safe inter-process communication\n")