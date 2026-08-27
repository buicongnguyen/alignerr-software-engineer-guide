# Alignerr Software Engineer Interview and Assessment Guide

> **Purpose:** Preparation for an Alignerr Software Engineer / AI Training role.
>
> These are representative questions inferred from Alignerr's public role description and public candidate reports. They are not leaked or guaranteed assessment questions. Current assessments are role-specific and may change.

## Contents

1. [How to use this guide](#1-how-to-use-this-guide)
2. [Resume and project questions](#2-resume-and-project-questions)
3. [C++ fundamentals](#3-c-fundamentals)
4. [Stack and heap memory](#4-stack-and-heap-memory)
5. [Python fundamentals](#5-python-fundamentals)
6. [Algorithms and coding problems](#6-algorithms-and-coding-problems)
7. [AI-generated code evaluation](#7-ai-generated-code-evaluation)
8. [Edge-case and test-design questions](#8-edge-case-and-test-design-questions)
9. [System-design questions](#9-system-design-questions)
10. [Ten-minute Zara interview simulation](#10-ten-minute-zara-interview-simulation)
11. [Last-minute checklist](#11-last-minute-checklist)
12. [Tailored questions for your compiler, NPU, AI, and EDA background](#12-tailored-questions-for-your-compiler-npu-ai-and-eda-background)

---

## 1. How to use this guide

For spoken questions, aim for a clear answer lasting 45–90 seconds. A useful structure is:

1. State the main point first.
2. Give one concrete example.
3. Explain the trade-off or result.
4. Stop instead of adding unrelated details.

For project questions, use:

> **Problem → responsibility → design → difficult issue → validation → measurable result**

Replace placeholders such as `[project]`, `[metric]`, and `[tool]` with facts from your own experience. Do not claim results you cannot defend in a follow-up question.

---

## 2. Resume and project questions

### Q1. Explain your compiler project from source input to generated output.

**Model answer:**

The compiler accepts source code, tokenizes it in the lexer, and converts the token stream into an abstract syntax tree in the parser. Semantic analysis then resolves symbols, checks types, and reports invalid programs. The front end lowers valid programs into an intermediate representation. Optimization passes transform that IR while preserving program behavior, and the back end performs instruction selection, scheduling, register allocation, and code generation for the target architecture. My main responsibility was `[your component]`. I validated it with unit tests for individual passes, end-to-end programs, negative tests for invalid input, and differential tests against `[reference implementation]`.

**What the interviewer is testing:** Whether you understand the complete system and can distinguish your own contribution from the team's work.

### Q2. What part of the compiler did you personally implement?

**Model answer:**

I owned `[component]`. I designed `[data structure or interface]`, implemented `[key behavior]`, and added tests covering `[important cases]`. One important decision was `[decision]`; I chose it because `[reason]`, although the trade-off was `[cost]`. I worked with `[adjacent component]` through a documented interface, so I can clearly separate my implementation from the rest of the compiler.

### Q3. Describe the hardest compiler bug you encountered. How did you isolate it?

**Model answer:**

The failure appeared only for `[specific condition]`, so I first minimized the input until I had the smallest reproducible case. I inspected the IR after each pass and found that the program was correct before `[pass]` but incorrect afterward. The pass made an invalid assumption about `[aliasing/liveness/dominance/type/overflow]`. I added an assertion to expose the violated invariant, corrected the transformation, and added both a regression test and nearby boundary cases. This process was faster and more reliable than debugging the final machine code directly.

### Q4. How did you represent the intermediate representation, and why?

**Model answer:**

I would choose an IR based on the transformations it must support. For data-flow optimization, SSA form is useful because each value has one definition and def-use relationships are explicit. For control-flow analysis, I would organize instructions into basic blocks connected by a control-flow graph. I would also define verification rules—valid types, valid terminators, consistent predecessor lists, and dominance requirements—so every pass can check that it has preserved IR invariants. The cost of SSA is that control-flow merges require phi-like values and the representation eventually has to be lowered out of SSA.

### Q5. Which optimizations did you implement, and how did you prove they preserved correctness?

**Model answer:**

I implemented `[constant folding/dead-code elimination/common-subexpression elimination/etc.]`. The transformation was guarded by explicit preconditions. For example, constant folding must respect the source language's overflow and floating-point rules; mathematically equivalent expressions are not always semantically equivalent. I tested the pass with unit cases, randomized inputs, and differential execution before and after optimization. I also verified IR invariants after every transformation. In production I would combine tests with code review and, for critical transformations, property-based or formal verification.

### Q6. How would you decide whether an operation should execute on a CPU, GPU, or NPU?

**Model answer:**

I would consider operation support, tensor shape and data type, arithmetic intensity, memory bandwidth, transfer overhead, latency constraints, batch size, and device availability. An NPU may be fastest for a supported dense neural-network operator, but moving a small tensor to it can cost more than executing on the CPU. I would begin with a cost model, then benchmark representative workloads. The scheduler should also account for graph-level effects because assigning one operator to a different device may introduce extra conversions or synchronization.

### Q7. Describe a performance bottleneck you found in an AI or NPU system.

**Model answer:**

I first measured the pipeline rather than guessing. Profiling showed that `[component]` consumed `[percentage]` of runtime because of `[memory copies/poor locality/device synchronization/kernel-launch overhead/etc.]`. I changed `[implementation]` to `[optimization]`. I compared identical workloads before and after the change, including warm-up and repeated runs, and reported median and tail latency rather than one favorable measurement. The result was `[measured improvement]`, with the trade-off of `[memory/complexity/portability cost]`.

### Q8. How did you prove that an optimization actually improved performance?

**Model answer:**

I defined the target metric first—for example throughput, median latency, p99 latency, memory use, or energy. I used representative fixed workloads, controlled relevant variables, warmed caches or runtimes when appropriate, and ran enough repetitions to show variance. I compared against a recorded baseline and checked that output correctness remained unchanged. I also profiled the new version to confirm that the original bottleneck disappeared rather than merely moving elsewhere.

### Q9. Why did you use C++ instead of Python, or Python instead of C++?

**Model answer:**

C++ is a strong choice when predictable performance, low-level memory control, deterministic resource management, or integration with systems APIs is central. Python is a strong choice when development speed, experimentation, readability, and its library ecosystem matter more than interpreter overhead. In a mixed system, I often prefer Python for orchestration and testing and C++ for measured hotspots. The important point is to choose from actual requirements and profiling data, not the assumption that every component must use the fastest language.

### Q10. Tell me about AI-generated code you would reject even if it passes common examples.

**Model answer:**

I would reject code if it relies on an unjustified assumption, fails boundary cases, has unsafe behavior, or has unacceptable complexity. For example, an implementation may pass small examples but use an `O(n²)` nested scan where the input limit requires `O(n log n)` or `O(n)`. I would give a concrete counterexample, explain the root cause, state the complexity, and propose a corrected implementation. I would also check whether the explanation matches the code, because a plausible explanation does not compensate for incorrect behavior.

### Q11. Describe a technical decision you would change today.

**Model answer:**

In `[project]`, I chose `[decision]` because it reduced initial implementation time. As the system grew, it caused `[specific problem]`. Today I would use `[better design]` and introduce it behind `[interface or migration plan]`. The important lesson was to identify the condition under which a simple design stops scaling; I would not automatically replace every simple solution with a more complicated one.

### Q12. Explain a complex technical topic to a non-specialist.

**Model answer:**

An optimizing compiler is like a translator and editor. It first understands what a program means, then rewrites it into a form a processor understands. While rewriting, it may remove unnecessary work or choose faster equivalent instructions, but it must never change the program's intended result. The difficult part is proving that every shortcut remains correct for unusual inputs as well as ordinary ones.

---

## 3. C++ fundamentals

### Q1. What problem does RAII solve?

**Answer:**

RAII binds a resource's lifetime to an object's lifetime. The constructor acquires or initializes the resource, and the destructor releases it. Because destructors run during normal scope exit and stack unwinding, RAII prevents leaks and simplifies exception safety. It applies to memory, files, locks, sockets, and other resources—not only heap allocation.

```cpp
#include <fstream>
#include <mutex>

void write_file(std::mutex& mutex) {
    std::lock_guard<std::mutex> lock(mutex);
    std::ofstream file("output.txt");
    file << "data\n";
} // file closes and mutex unlocks automatically
```

### Q2. When must a base-class destructor be virtual?

**Answer:**

If an object may be deleted through a pointer to its base class, the base destructor must be virtual. Otherwise, deleting a derived object through that pointer has undefined behavior. If polymorphic deletion is not allowed, another valid design is a protected non-virtual base destructor.

```cpp
class Base {
public:
    virtual ~Base() = default;
};
```

### Q3. Compare `std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr`.

**Answer:**

- `unique_ptr` represents exclusive ownership. It is move-only and normally has the lowest overhead.
- `shared_ptr` represents shared ownership through reference counting. The object is destroyed when the last owning pointer is destroyed.
- `weak_ptr` observes an object managed by `shared_ptr` without extending its lifetime. It is commonly used to break ownership cycles.

Prefer `unique_ptr` unless the design genuinely requires shared ownership. A raw pointer or reference can express non-owning access when lifetime safety is otherwise guaranteed.

### Q4. Explain copy construction versus move construction.

**Answer:**

A copy constructor creates an independent object from an existing object, normally duplicating its owned state. A move constructor transfers resources from an expiring object and leaves the source valid but in an unspecified state. Moving can avoid expensive allocation and copying, but it does not guarantee zero cost. `std::move` is only a cast that permits moving; the selected move operation performs the transfer.

### Q5. What are the Rule of Zero and Rule of Five?

**Answer:**

The Rule of Zero says that a class should use RAII member types so it does not need to define its own destructor, copy constructor, copy assignment, move constructor, or move assignment. If a class directly manages a resource and defines one of those special operations, it often needs to consider all five—the Rule of Five. Rule of Zero is generally safer and easier to maintain.

### Q6. When does `std::vector` invalidate iterators, pointers, and references?

**Answer:**

An operation that reallocates the vector's storage invalidates all iterators, pointers, and references to its elements. Without reallocation, insertion or erasure generally invalidates positions at or after the modification point; `erase` also invalidates the erased elements. `reserve` can reduce reallocations, but calling `reserve` itself invalidates references if it changes capacity.

### Q7. What is wrong with returning a reference to a local variable?

**Answer:**

The local variable's lifetime ends when the function returns, so the returned reference dangles. Using it causes undefined behavior. Return by value, refer to an object whose lifetime outlives the call, or return an owning/smart-pointer type when dynamic lifetime is required.

```cpp
int& bad() {
    int value = 42;
    return value; // dangling reference
}

int good() {
    return 42;
}
```

### Q8. What is object slicing?

**Answer:**

Object slicing occurs when a derived object is copied into a base object by value. The derived portion is discarded, so derived state and behavior are lost. Use references or pointers for runtime polymorphism, or deliberately use value semantics with a design that does not depend on derived state.

### Q9. Compare a mutex, an atomic operation, and a condition variable.

**Answer:**

- A mutex provides mutual exclusion for a critical section containing one or more related operations.
- An atomic provides indivisible operations on a particular value and may be suitable for counters or carefully designed lock-free algorithms.
- A condition variable lets threads sleep until shared state may satisfy a predicate; it is normally used with a mutex and a predicate loop.

Atomics are not an automatic replacement for locks. Multi-variable invariants and compound operations often still need synchronization.

### Q10. Give the average complexities of common containers.

**Answer:**

| Operation | `vector` | `map` | `unordered_map` |
|---|---:|---:|---:|
| Indexed access | `O(1)` | N/A | N/A |
| Search by key/value | `O(n)` | `O(log n)` | Average `O(1)`, worst `O(n)` |
| Insert at end | Amortized `O(1)` | N/A | N/A |
| Insert/erase by key | N/A | `O(log n)` | Average `O(1)`, worst `O(n)` |
| Insert/erase in middle | `O(n)` | N/A | N/A |

`map` is ordered and has predictable logarithmic operations. `unordered_map` is hash-based and offers average constant-time lookup but depends on hashing quality and load factor.

### Q11. What is undefined behavior, and why is it dangerous?

**Answer:**

Undefined behavior means the C++ standard imposes no requirements on what happens. Examples include signed-integer overflow, out-of-bounds access, data races, and dereferencing dangling pointers. A program may appear to work in one build and fail after optimization because the compiler is allowed to assume undefined behavior never occurs. Tests and sanitizers help detect it, but sound ownership and bounds design are the primary defense.

### Q12. What is the difference between a reference and a pointer?

**Answer:**

A reference is an alias that must be initialized and cannot be reseated; normal references are intended to refer to valid objects. A pointer stores an address, may be null, and may be reseated. Neither expresses ownership by itself. In interfaces, a reference often communicates required non-owning access, while a pointer can communicate optional access when null is meaningful.

---

## 4. Stack and heap memory

### Q1. What is the difference between stack and heap memory?

**Answer:**

The **stack** usually stores function call frames, including return information and many local variables. Allocation and deallocation follow scope and call order, making them fast and automatic. Each thread normally has its own limited stack.

The **heap**—more precisely, dynamic storage—supports objects whose size or lifetime is determined at runtime. Allocation is performed through an allocator, such as with `new` or `std::make_unique` in C++, and has more management overhead. Heap objects remain alive until their owning mechanism releases them.

The key distinction is storage duration and lifetime management, not simply “small versus large.” Language standards do not require every local variable to occupy a physical stack; compilers may place values in registers or optimize them away.

### Q2. Does a pointer live on the stack or the heap?

**Answer:**

Either is possible. A local pointer variable may be stored in a stack frame while pointing to an object in dynamic storage. A pointer can also be a member of a heap-allocated object. The pointer's storage location and the pointee's storage location are independent.

```cpp
void example() {
    auto value = std::make_unique<int>(42);
    // The unique_ptr is a local object, commonly in the stack frame.
    // The int it owns is in dynamic storage.
}
```

### Q3. What causes a stack overflow?

**Answer:**

A stack overflow occurs when a thread exceeds its available stack space. Typical causes are unbounded or excessively deep recursion and very large automatic local objects. Possible fixes include correcting the recursion base case, using an iterative algorithm, storing large buffers in dynamic storage, or increasing the stack only when the algorithm genuinely requires it.

### Q4. What is a memory leak?

**Answer:**

A memory leak occurs when dynamically allocated memory remains allocated but the program has lost the ability or responsibility to release it. Repeated leaks increase memory use and may eventually degrade or terminate the process. In C++, prefer containers and RAII smart pointers. Note that memory still reachable but retained unnecessarily can also cause practical memory-growth problems even if a leak detector does not call it unreachable.

### Q5. What are a dangling pointer and a use-after-free error?

**Answer:**

A dangling pointer refers to an object whose lifetime has ended. A use-after-free occurs when the program accesses that invalid object. The behavior is undefined and can cause corruption or security vulnerabilities. Ownership types, clear lifetime rules, and sanitizers reduce this risk.

```cpp
int* pointer = new int(42);
delete pointer;
pointer = nullptr; // prevents this variable from retaining the old address
```

Setting one pointer to null does not repair other aliases that still point to the destroyed object, so the real solution is sound ownership.

### Q6. Is stack allocation always faster than heap allocation?

**Answer:**

Stack-style allocation is usually cheaper because it often amounts to adjusting a pointer, while a general-purpose heap allocator must track blocks and synchronization. However, overall performance depends on object initialization, cache behavior, allocator implementation, escape analysis, and access patterns. The correct interview answer is “usually, but measure the complete workload.”

### Q7. How does RAII relate to heap memory?

**Answer:**

RAII places ownership of a dynamic resource inside an object with deterministic cleanup. For example, `unique_ptr` releases its heap object in its destructor. When the owner leaves scope, cleanup happens automatically even during exception propagation. This converts manual lifetime management into normal object lifetime management.

### Q8. What happens to memory in this function?

```cpp
int* create_value() {
    int local = 7;
    return &local;
}
```

**Answer:**

`local` has automatic storage duration and its lifetime ends when `create_value` returns. The returned pointer is dangling, and dereferencing it is undefined behavior. Return the integer by value:

```cpp
int create_value() {
    return 7;
}
```

If an object truly needs dynamic lifetime, return an owning type such as `std::unique_ptr<T>`, although dynamic allocation is unnecessary for an integer in this example.

### Q9. What is fragmentation?

**Answer:**

External fragmentation means free memory is divided into blocks that may be too small or poorly placed for a requested allocation. Internal fragmentation is unused space inside allocated blocks because of size classes, alignment, or metadata. Allocators use strategies such as pools, arenas, size classes, and compaction where possible to manage these costs.

### Q10. In Python, are objects on the stack or heap?

**Answer:**

Python language semantics should be described in terms of objects, references, and lifetimes rather than relying on a C++-style stack/heap model. In CPython, Python objects are generally dynamically allocated, while execution frames contain references to them. Assignment binds a name to an object; it does not copy the object. CPython mainly uses reference counting plus a cyclic garbage collector, but those are implementation details rather than guarantees of every Python implementation.

---

## 5. Python fundamentals

### Q1. What is the mutable-default-argument problem?

**Answer:**

Default arguments are evaluated once when the function is defined, not on every call. A mutable default such as a list is therefore shared across calls.

```python
def append_value(value, values=None):
    if values is None:
        values = []
    values.append(value)
    return values
```

Using `None` as a sentinel creates a fresh list when the caller does not provide one.

### Q2. How do generators differ from lists?

**Answer:**

A list eagerly stores all its elements and supports repeated iteration and indexing. A generator produces values lazily, normally one at a time, which can reduce memory use and allow streaming or infinite sequences. A generator is usually consumed once and does not provide random access. Laziness may also defer exceptions until iteration.

### Q3. What is a decorator, and how do you preserve metadata?

**Answer:**

A decorator takes a function or class and returns a replacement, commonly adding behavior such as logging, authorization, caching, or instrumentation. `functools.wraps` copies important metadata and exposes the original wrapped function.

```python
from functools import wraps

def trace(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        print(f"calling {function.__name__}")
        return function(*args, **kwargs)
    return wrapper
```

### Q4. What is a context manager?

**Answer:**

A context manager defines setup and cleanup around a block controlled by `with`. It normally implements `__enter__` and `__exit__`, or is built with `contextlib.contextmanager`. Files, locks, transactions, and temporary resources are common examples. Cleanup runs even when the body raises an exception.

### Q5. Explain late binding in Python closures.

**Answer:**

Closures capture variables by reference-like binding, so the value is looked up when the inner function runs. Functions created in a loop can therefore all observe the loop variable's final value.

```python
functions = [lambda i=i: i for i in range(3)]
```

Here `i=i` stores the current value as a default argument for each function.

### Q6. What does the GIL prevent, and what does it not prevent?

**Answer:**

In standard GIL-enabled CPython builds, the Global Interpreter Lock allows only one thread at a time to execute Python bytecode within a process. It often limits CPU-bound pure-Python thread parallelism, but threads can still help with blocking I/O because the GIL may be released. The GIL does not make application-level compound operations automatically thread-safe, and native extensions may release it. Processes or native/vectorized code are common choices for CPU-bound parallelism.

### Q7. When should you use threads, processes, or `asyncio`?

**Answer:**

- Use threads for blocking I/O or libraries with synchronous interfaces, while protecting shared state.
- Use processes for CPU-bound Python work when process overhead and serialization are acceptable.
- Use `asyncio` for high-concurrency I/O when the libraries are asynchronous and tasks cooperate by awaiting instead of blocking.

The choice depends on workload, library support, failure isolation, communication cost, and operational complexity.

### Q8. Compare instance methods, class methods, and static methods.

**Answer:**

An instance method receives `self` and operates on a particular instance. A class method receives `cls` and is often used for alternate constructors or behavior that depends on the subclass. A static method receives neither automatically and is a namespaced utility related to the class. A module-level function may be clearer if the function does not belong conceptually to the class.

### Q9. What is a metaclass, and when would you avoid it?

**Answer:**

A metaclass controls class creation; a class is normally an instance of a metaclass such as `type`. Metaclasses can register subclasses, validate class definitions, or generate class-level behavior. They are powerful but can obscure control flow and complicate inheritance. Class decorators, `__init_subclass__`, descriptors, or ordinary composition are often simpler alternatives.

### Q10. What happens when an awaitable passed to `asyncio.gather` fails?

**Answer:**

With default arguments, the first raised exception is propagated to the task awaiting `gather`. Other submitted awaitables are not automatically guaranteed to be cancelled merely because one failed. With `return_exceptions=True`, exceptions are collected into the result list. Production code should make cancellation and cleanup behavior explicit, and modern structured-concurrency tools such as `TaskGroup` may provide more suitable failure semantics.

### Q11. What is the difference between `is` and `==`?

**Answer:**

`==` tests value equality through an object's equality behavior. `is` tests object identity—whether two references point to the same object. Use `is` for singletons such as `None`: `value is None`. Do not rely on interning behavior to compare strings or integers with `is`.

### Q12. What is the difference between shallow and deep copying?

**Answer:**

A shallow copy creates a new outer container but retains references to nested objects. A deep copy recursively copies nested state where supported. Deep copying can be expensive or semantically wrong for objects such as files, locks, or shared resources, so explicit copying is often preferable for complex domain objects.

---

## 6. Algorithms and coding problems

The examples use Python for brevity. In an interview, first clarify input constraints and expected behavior, then explain the approach before coding.

### Q1. Rotate an `N × N` matrix 90 degrees counterclockwise in place.

**Approach:** Transpose the matrix, then reverse the order of the rows. This is a publicly reported example of an Alignerr interview question, but it is not guaranteed to recur.

```python
def rotate_counterclockwise(matrix: list[list[int]]) -> None:
    n = len(matrix)
    if any(len(row) != n for row in matrix):
        raise ValueError("matrix must be square")

    for row in range(n):
        for col in range(row + 1, n):
            matrix[row][col], matrix[col][row] = (
                matrix[col][row],
                matrix[row][col],
            )

    matrix.reverse()
```

**Complexity:** `O(n²)` time and `O(1)` auxiliary space.

**Important tests:** Empty matrix, `1 × 1`, `2 × 2`, negative values, and a non-square input if validation is required.

### Q2. Find the length of the longest substring without repeated characters.

**Approach:** Maintain a sliding window and the most recent index of each character.

```python
def longest_unique_substring(text: str) -> int:
    last_seen: dict[str, int] = {}
    left = 0
    best = 0

    for right, character in enumerate(text):
        if character in last_seen and last_seen[character] >= left:
            left = last_seen[character] + 1
        last_seen[character] = right
        best = max(best, right - left + 1)

    return best
```

**Complexity:** `O(n)` time and `O(k)` space, where `k` is the number of distinct characters tracked.

### Q3. Merge overlapping intervals.

**Approach:** Sort by start position, then extend or append to the result.

```python
def merge_intervals(intervals: list[list[int]]) -> list[list[int]]:
    if not intervals:
        return []

    ordered = sorted(intervals, key=lambda interval: interval[0])
    merged = [ordered[0][:]]

    for start, end in ordered[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])

    return merged
```

**Complexity:** `O(n log n)` time for sorting and `O(n)` output space.

**Clarify:** Whether touching intervals such as `[1, 2]` and `[2, 3]` count as overlapping.

### Q4. Return the `k` most frequent values.

**Approach:** Count values and retain the largest `k` frequency entries with a heap.

```python
from collections import Counter
import heapq

def top_k_frequent(values: list[int], k: int) -> list[int]:
    if k < 0:
        raise ValueError("k must be non-negative")
    counts = Counter(values)
    if k > len(counts):
        raise ValueError("k exceeds the number of distinct values")
    return [value for value, _ in heapq.nlargest(
        k,
        counts.items(),
        key=lambda item: item[1],
    )]
```

**Complexity:** `O(n + m log k)` time and `O(m)` space, where `m` is the number of distinct values.

### Q5. Explain a heap data structure and implement the `k`th-largest element.

**Answer:**

A binary heap is a complete binary tree usually stored in an array. In a min-heap, every parent is no greater than its children, so the minimum is available in `O(1)`. Insertion and removal of the root take `O(log n)`. A heap is not fully sorted; it guarantees only the parent-child ordering needed for priority-queue operations.

Maintain a min-heap containing the largest `k` values seen. Its root is the `k`th largest.

```python
import heapq

def kth_largest(values: list[int], k: int) -> int:
    if not 1 <= k <= len(values):
        raise ValueError("k is out of range")

    heap: list[int] = []
    for value in values:
        if len(heap) < k:
            heapq.heappush(heap, value)
        elif value > heap[0]:
            heapq.heapreplace(heap, value)
    return heap[0]
```

**Complexity:** `O(n log k)` time and `O(k)` space.

**Common follow-up:** A heap data structure is unrelated to the general-purpose heap-memory region despite sharing the word “heap.”

### Q6. Detect a cycle in a directed graph.

**Approach:** Use three DFS states: unvisited, visiting, and visited. Reaching a visiting node reveals a back edge.

```python
def has_directed_cycle(graph: dict[int, list[int]]) -> bool:
    state: dict[int, int] = {}  # 0=unvisited, 1=visiting, 2=visited

    def visit(node: int) -> bool:
        if state.get(node, 0) == 1:
            return True
        if state.get(node, 0) == 2:
            return False

        state[node] = 1
        for neighbor in graph.get(node, []):
            if visit(neighbor):
                return True
        state[node] = 2
        return False

    nodes = set(graph)
    nodes.update(neighbor for values in graph.values() for neighbor in values)
    return any(visit(node) for node in nodes if state.get(node, 0) == 0)
```

**Complexity:** `O(V + E)` time and `O(V)` space.

### Q7. Find the shortest path through an unweighted grid.

**Approach:** BFS explores states in increasing distance order.

```python
from collections import deque

def shortest_grid_path(
    grid: list[list[int]],
    start: tuple[int, int],
    goal: tuple[int, int],
) -> int:
    if not grid or not grid[0]:
        return -1

    rows, cols = len(grid), len(grid[0])
    if any(len(row) != cols for row in grid):
        raise ValueError("grid must be rectangular")

    def open_cell(row: int, col: int) -> bool:
        return 0 <= row < rows and 0 <= col < cols and grid[row][col] == 0

    if not open_cell(*start) or not open_cell(*goal):
        return -1

    queue = deque([(start[0], start[1], 0)])
    visited = {start}

    while queue:
        row, col, distance = queue.popleft()
        if (row, col) == goal:
            return distance

        for row_delta, col_delta in ((1, 0), (-1, 0), (0, 1), (0, -1)):
            next_cell = (row + row_delta, col + col_delta)
            if next_cell not in visited and open_cell(*next_cell):
                visited.add(next_cell)
                queue.append((*next_cell, distance + 1))

    return -1
```

**Complexity:** `O(rows × columns)` time and space.

### Q8. Implement an LRU cache.

**Approach:** A hash map provides lookup, and a doubly linked ordering provides `O(1)` movement and eviction. Python's `OrderedDict` already combines these behaviors.

```python
from collections import OrderedDict
from typing import Generic, TypeVar

K = TypeVar("K")
V = TypeVar("V")

class LRUCache(Generic[K, V]):
    def __init__(self, capacity: int) -> None:
        if capacity <= 0:
            raise ValueError("capacity must be positive")
        self.capacity = capacity
        self.data: OrderedDict[K, V] = OrderedDict()

    def get(self, key: K) -> V | None:
        if key not in self.data:
            return None
        self.data.move_to_end(key)
        return self.data[key]

    def put(self, key: K, value: V) -> None:
        if key in self.data:
            self.data.move_to_end(key)
        self.data[key] = value
        if len(self.data) > self.capacity:
            self.data.popitem(last=False)
```

**Complexity:** Average `O(1)` for `get` and `put`; `O(capacity)` space.

**Clarify:** Returning `None` cannot distinguish a missing key from a cached `None`; a sentinel or exception may be preferable.

### Q9. Find a boundary with binary search.

**Problem:** Return the first index for which a monotonic predicate is true, or `len(values)` if it is never true.

```python
from collections.abc import Callable, Sequence
from typing import TypeVar

T = TypeVar("T")

def first_true(values: Sequence[T], predicate: Callable[[T], bool]) -> int:
    left, right = 0, len(values)

    while left < right:
        middle = left + (right - left) // 2
        if predicate(values[middle]):
            right = middle
        else:
            left = middle + 1

    return left
```

**Complexity:** `O(log n)` predicate evaluations and `O(1)` extra space.

**Important invariant:** All indices before `left` are known false, and all indices at or after `right` are known true.

### Q10. Return a valid task order or report a dependency cycle.

**Approach:** Use Kahn's topological-sort algorithm with indegrees.

```python
from collections import deque

def task_order(task_count: int, dependencies: list[tuple[int, int]]) -> list[int]:
    # (prerequisite, dependent)
    graph = [[] for _ in range(task_count)]
    indegree = [0] * task_count

    for prerequisite, dependent in dependencies:
        graph[prerequisite].append(dependent)
        indegree[dependent] += 1

    queue = deque(index for index, degree in enumerate(indegree) if degree == 0)
    order: list[int] = []

    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == task_count else []
```

**Complexity:** `O(V + E)` time and space.

### Q11. When should you use a stack, a queue, or a heap?

**Answer:**

- A stack is LIFO and fits nested structure, undo operations, expression evaluation, and DFS.
- A queue is FIFO and fits arrival-order processing and BFS.
- A heap is a priority queue and fits repeatedly retrieving the smallest or largest priority, scheduling, top-`k` problems, and algorithms such as Dijkstra's.

Their names describe access behavior, not where their elements physically reside in memory.

### Q12. Compare DFS and BFS.

**Answer:**

Both visit a graph in `O(V + E)` time when implemented with adjacency lists. BFS uses a queue and finds shortest paths by edge count in an unweighted graph. DFS uses recursion or an explicit stack and is useful for cycle detection, topological reasoning, and exhaustive path exploration. Their peak memory depends on graph shape: BFS can store a wide frontier, while recursive DFS can overflow the call stack on a very deep graph.

---

## 7. AI-generated code evaluation

### Q1. How would you compare two AI-generated solutions?

**Answer:**

I would evaluate them in this order:

1. **Requirement compliance:** Do they solve the requested problem without inventing assumptions?
2. **Correctness:** Can I prove the algorithm, and can I find a counterexample?
3. **Edge cases:** Empty input, boundaries, duplicates, invalid data, overflow, concurrency, and scale.
4. **Complexity:** Does time and memory usage fit the constraints?
5. **Safety:** Resource lifetime, injection, unsafe deserialization, races, and error handling.
6. **Maintainability:** Clear names, appropriate abstraction, minimal duplication, and tests.
7. **Explanation quality:** Does the explanation accurately describe the submitted code?

I would rank correctness above style. A readable wrong answer is still wrong, while an initially correct but opaque answer may be fixable through refactoring.

### Q2. Critique these two implementations of duplicate detection.

```python
def solution_a(values):
    for i in range(len(values)):
        for j in range(i + 1, len(values)):
            if values[i] == values[j]:
                return True
    return False

def solution_b(values):
    return len(values) != len(set(values))
```

**Answer:**

Both are correct for finite inputs whose elements support equality; `solution_b` additionally requires hashable elements. `solution_a` uses `O(n²)` comparisons and `O(1)` auxiliary space. `solution_b` normally uses `O(n)` time and `O(n)` space, so it is preferable for a large list of hashable values. However, `solution_a` is applicable to unhashable values such as lists, while `solution_b` raises `TypeError`. The correct ranking therefore depends on the input contract rather than syntax alone.

### Q3. Find the bug in this binary search.

```python
def contains(values, target):
    left, right = 0, len(values) - 1
    while left < right:
        middle = (left + right) // 2
        if values[middle] < target:
            left = middle + 1
        else:
            right = middle - 1
    return values[left] == target
```

**Answer:**

There are two bugs. First, an empty list causes an invalid access to `values[0]`. Second, setting `right = middle - 1` discards `middle` even when it could contain the target; combined with `left < right`, the invariants are inconsistent. A corrected closed-interval search is:

```python
def contains(values, target):
    left, right = 0, len(values) - 1
    while left <= right:
        middle = left + (right - left) // 2
        if values[middle] == target:
            return True
        if values[middle] < target:
            left = middle + 1
        else:
            right = middle - 1
    return False
```

### Q4. What security problems do AI coding assistants commonly miss?

**Answer:**

Common failures include SQL or command injection, missing authorization checks, path traversal, unsafe deserialization, hard-coded secrets, weak randomness, permissive CORS, server-side request forgery, insecure temporary files, and leaking sensitive data through logs. I would not merely name a vulnerability; I would identify the trust boundary, provide an attack input, assess impact, and propose a framework-appropriate fix plus a regression test.

### Q5. How do you write useful feedback on an incorrect answer?

**Answer:**

I state the verdict, identify the exact faulty assumption, provide the smallest counterexample, explain expected versus actual behavior, and suggest the necessary correction. For example: “The answer is incorrect because it assumes all values are positive. For `[-5, -2]`, it returns `0`, although the maximum is `-2`. Initialize the running result from the first element or negative infinity, and add an all-negative regression test.” This is more actionable than saying only “edge cases are missing.”

### Q6. How would you design a prompt that stress-tests a coding model?

**Answer:**

I would specify an unambiguous task with constraints that expose likely failure modes. For example, request an LRU cache that supports a cached `None`, has fixed capacity, is safe under concurrent reads and writes, and must provide average `O(1)` operations. I would include observable acceptance criteria but avoid revealing the implementation. Hidden tests would cover eviction order, updates, zero or invalid capacity, concurrency, and the distinction between missing and cached values.

---

## 8. Edge-case and test-design questions

### Q1. What cases would you test for a binary search?

**Answer:**

- Empty input
- One element, both present and absent
- Target at the first and last positions
- Target below the minimum and above the maximum
- Even and odd lengths
- Duplicate values, with a clarified requirement for any/first/last occurrence
- Very large input
- Invalid or unsorted input if validation is part of the contract

I would also verify termination and index bounds, because many binary-search bugs come from mixing closed and half-open intervals.

### Q2. What cases would you test for matrix rotation?

**Answer:**

Test an empty matrix if permitted, `1 × 1`, `2 × 2`, odd and even dimensions, repeated and negative values, and the expected result after four rotations. If the function promises in-place behavior, verify object identity and ensure it does not allocate another `N × N` matrix. Decide explicitly whether non-square or ragged input should be rejected.

### Q3. How would you test an LRU cache?

**Answer:**

Test insertion and retrieval, eviction at capacity, refreshing recency on `get`, refreshing or replacing on `put`, repeated updates to one key, capacity one, missing keys, and cached null-like values. If concurrency is required, test simultaneous access and use a race detector where available. Also verify that memory use remains bounded by capacity.

### Q4. How would you test an API endpoint generated by an AI?

**Answer:**

I would test valid requests, required and optional fields, type and size boundaries, malformed input, authentication, authorization between users, duplicate/idempotent requests, rate limits, timeouts, dependency failures, and safe error messages. I would also check injection, logging of secrets, transaction rollback, concurrency, pagination, and whether the documented response schema matches the implementation.

### Q5. What is property-based testing?

**Answer:**

Property-based testing generates many inputs and verifies general invariants instead of checking only hand-selected examples. For sorting, useful properties include that the output is ordered, is a permutation of the input, and sorting twice produces the same result. It complements example-based tests and is especially effective for parsers, serializers, numeric code, and compiler transformations.

### Q6. What is differential testing?

**Answer:**

Differential testing runs the same inputs through two implementations and compares their observable behavior. A compiler can compare optimized and unoptimized execution, or compare its output with a trusted reference compiler within the common language subset. Differences reveal bugs, although the reference may also be wrong, and undefined or implementation-specific behavior must be excluded or normalized.

---

## 9. System-design questions

### Q1. Design a service that evaluates AI-generated code safely.

**Model answer:**

I would separate the control plane from isolated execution workers. The API authenticates the caller, validates a submission, stores metadata, and publishes a job to a durable queue. A worker launches an ephemeral sandbox with a read-only base image, strict CPU, memory, process, filesystem, and wall-clock limits, no network by default, and a low-privilege identity. Tests and static checks run inside the sandbox, and structured results are stored before the job is acknowledged.

For scale, workers can autoscale from queue depth. Job identifiers make retries idempotent, and leases recover jobs from failed workers. I would record image and test-suite versions for reproducibility, keep untrusted output separate from service logs, limit output size, scan supplied artifacts, and destroy the sandbox after each run. Metrics would include queue latency, execution latency, timeout rate, infrastructure failures, and result consistency.

### Q2. How would you design a distributed task queue?

**Model answer:**

I would first clarify delivery semantics and workload. A practical design uses a durable broker, worker leases or visibility timeouts, idempotent handlers, bounded retries with exponential backoff, and a dead-letter queue. Jobs carry an immutable identifier and versioned payload. Workers acknowledge only after durable result storage. Monitoring covers queue age, retries, failures, processing latency, and poison jobs. Exactly-once execution is rarely realistic end to end, so I would design for at-least-once delivery and idempotent effects.

### Q3. How would you prevent duplicate work after a worker crashes?

**Answer:**

The broker can re-deliver a job after its lease expires, so duplicate execution is possible. I would use a stable job ID and make result commits idempotent, often with a uniqueness constraint or compare-and-set state transition. External side effects require their own idempotency keys. Acknowledging only after the result commits prevents silent loss, while idempotency makes retries safe.

### Q4. REST or GraphQL: how would you choose?

**Answer:**

REST is simple, cache-friendly, and maps naturally to resource-oriented endpoints. GraphQL gives clients flexible selection and a typed schema, which can reduce over-fetching across complex related data. Its costs include resolver performance, authorization complexity, query-cost controls, and caching. I would choose based on client diversity and access patterns, not fashion; either can be designed well or poorly.

### Q5. What metrics would you monitor in an AI-code evaluation platform?

**Answer:**

I would monitor availability, request and queue latency, queue age, worker utilization, execution time, timeout and out-of-memory rates, infrastructure versus candidate-code failures, retry counts, sandbox startup time, and storage errors. For evaluation quality, I would track flaky-test rate, evaluator agreement, benchmark version, unexpected score drift, and reproducibility. Metrics need dimensions such as language and runtime version without including sensitive source code.

---

## 10. Ten-minute Zara interview simulation

These model answers are templates. Personalize them and keep every claim defensible.

### Q1. Introduce yourself and summarize your strongest software-engineering expertise.

**Model answer:**

I am a software engineer focused on C++, Python, algorithms, and performance-sensitive systems. My strongest experience is in compiler development and AI/NPU-related tooling, where correctness and efficiency both matter. I have worked on `[specific component]`, solved `[specific problem]`, and validated the result using `[tests/benchmarks]`. I am particularly comfortable reading unfamiliar code, finding subtle defects, and explaining why a solution is correct or incorrect, which directly matches AI-code evaluation work.

### Q2. Tell me about your compiler or NPU project and your exact contribution.

**Model answer:**

The project addressed `[problem]`. I was responsible for `[specific owned component]`, not the entire system. I designed `[interface/data structure]` and implemented `[behavior]`. The hardest issue was `[technical difficulty]`; I diagnosed it using `[method]` and fixed it by `[solution]`. I verified correctness with `[tests]` and measured `[metric]`, which changed from `[before]` to `[after]`.

### Q3. What was the most difficult technical problem in that project?

**Model answer:**

The most difficult problem was `[problem]` because it appeared only under `[condition]`. I reduced it to a minimal reproducible example and instrumented `[relevant layer]`. That showed the real cause was `[root cause]`, rather than the component where the final failure appeared. I corrected `[invariant or implementation]`, added a regression test, and checked adjacent cases. The main lesson was to debug at the earliest layer where the state becomes incorrect.

### Q4. Explain one C++ or Python concept essential to your solution.

**Model answer:**

RAII was essential because the component owned resources across multiple error paths. I represented each resource with an object whose destructor released it, so normal returns and exceptions used the same cleanup mechanism. That removed manual cleanup duplication and prevented leaks. The broader principle is that ownership should be explicit in types rather than remembered informally by callers.

### Q5. How did you test correctness and evaluate performance?

**Model answer:**

For correctness, I used unit tests for individual transformations, integration tests for the complete pipeline, negative tests, and regression cases for every discovered bug. Where possible, I used differential or property-based testing. For performance, I chose a representative workload, recorded a baseline, controlled warm-up and configuration, ran repeated measurements, and reported median and tail results. I also confirmed that the optimized output remained correct.

### Q6. How would you evaluate code generated by an AI model?

**Model answer:**

I would first restate the contract and constraints. Then I would inspect the algorithm, attempt to prove it, and actively search for a small counterexample. I would test boundaries, analyze time and space complexity, check resource and security behavior, and verify that the explanation matches the code. My feedback would name the exact defect, demonstrate it with an input, and propose a correction rather than giving only a general rating.

### Q7. What edge cases do coding models commonly miss?

**Model answer:**

Common misses include empty and singleton inputs, duplicates, negative values, overflow, malformed input, Unicode, aliasing and mutation, unavailable dependencies, timeouts, concurrency races, and inputs large enough to expose poor complexity. The relevant cases depend on the contract, so I derive them from boundaries, equivalence classes, and implementation assumptions instead of applying one fixed checklist blindly.

### Q8. Why are you interested in this role?

**Model answer:**

The role combines three things I already value: solving technical problems, reviewing code critically, and communicating the reasoning behind a judgment. My compiler and systems background trained me to look for invariants, performance costs, and unusual boundary conditions. I am interested in applying that discipline to improve the correctness and reliability of AI-generated software.

### Q9. Tell me about a time you received technical criticism.

**Model answer:**

During `[project]`, a reviewer pointed out that my design assumed `[assumption]`, which would fail under `[condition]`. I reproduced the concern, agreed it was valid, and revised the design to `[change]`. I added a test that would have caught the original problem and documented the invariant. The feedback improved both the component and my review checklist; I now surface assumptions explicitly before implementation.

### Q10. Why should an evaluator explain a verdict instead of only assigning a score?

**Model answer:**

A score says which response appears better, but it does not reveal the failure mode or provide a useful training signal. A good explanation ties the verdict to a requirement, shows concrete evidence such as a counterexample, and distinguishes a fatal correctness issue from a minor style concern. This also improves consistency between evaluators and makes disputed judgments auditable.

---

## 11. Last-minute checklist

Before beginning the interview or assessment:

- Review every language, framework, and project named on your résumé.
- Prepare a 60-second introduction and two 90-second project stories.
- Quantify results only when you can explain how they were measured.
- Review arrays, hash maps, stacks, queues, heaps, graphs, BFS/DFS, binary search, and basic dynamic programming.
- Review C++ ownership, RAII, move semantics, virtual destructors, iterator invalidation, and undefined behavior.
- Review Python generators, decorators, context managers, closures, concurrency, and mutable defaults.
- Practise distinguishing the heap data structure from heap/dynamic memory.
- For every coding answer, state correctness, complexity, and edge cases.
- For every critique, give a concrete counterexample.
- Speak concisely: answer the question first, then provide supporting detail.

---

## 12. Tailored questions for your compiler, NPU, AI, and EDA background

This final section is tailored to the background described in this conversation: C++, Python, algorithms, compiler development, NPU/AI systems, and EDA. Replace bracketed fields with facts from your résumé. Do not invent a metric or claim that you cannot explain under follow-up questioning.

### Q1. Give me a 60-second introduction connecting your compiler, NPU, and AI experience to this role.

**Model answer:**

I am a software engineer specializing in C++, Python, algorithms, and performance-sensitive AI systems. In `[compiler or NPU project]`, I owned `[specific component]` and worked across `[compiler/runtime/hardware boundary]` to solve `[specific problem]`. I used `[tests, profiling, or differential validation]` to verify correctness and improved `[latency/throughput/memory/compiler quality]` by `[defensible result]`. That background is relevant to Alignerr because I am accustomed to reading unfamiliar code, finding incorrect assumptions and boundary cases, comparing alternative implementations, and explaining technical judgments clearly.

### Q2. Choose one compiler project and trace a single operation from source or model graph to generated execution.

**Model answer:**

I would use `[representative operation]`. It enters the system as `[source AST, framework operator, or graph node]`, is validated and normalized by `[front-end component]`, and is lowered into `[IR name or form]`. Optimization passes apply `[specific transformations]` under `[important invariants]`. The back end then performs `[instruction selection, scheduling, memory planning, register allocation, or code emission]` for `[target]`. At runtime, `[runtime component]` loads the result and coordinates `[buffers, kernels, synchronization, or device execution]`. My personal contribution was `[precise scope]`, and I validated it with `[unit, integration, differential, or hardware tests]`.

### Q3. What compiler invariant would you check first when an AI-generated optimization pass produces incorrect output?

**Model answer:**

I would first identify the earliest pass after which observable state becomes incorrect. Then I would check the invariants relevant to that pass: type and shape consistency, dominance and def-use validity, aliasing assumptions, side-effect ordering, liveness, numerical semantics, and control-flow integrity. I would minimize the failing program, compare IR immediately before and after the pass, and disable or isolate transformations until one rewrite remains. The feedback to the AI should name the violated invariant and provide the minimized counterexample rather than merely saying the optimized output is wrong.

### Q4. How should an NPU compiler handle an unsupported operator?

**Model answer:**

The compiler should detect support during capability analysis, not fail unpredictably during execution. Depending on system requirements, it can reject the model with a precise diagnostic, lower the operation into supported primitives, or partition the graph and fall back to CPU or GPU execution. A fallback decision must include tensor-layout conversion, data-transfer cost, synchronization, numerical compatibility, and graph-level performance. I would test unsupported data types, dynamic shapes, boundary tensor sizes, and chains of supported and unsupported operators.

### Q5. Explain how tiling and memory planning affect NPU performance.

**Model answer:**

NPUs often have fast but limited on-chip memory, so large tensors must be divided into tiles. The tile shape affects data reuse, DMA traffic, alignment, parallel utilization, and the amount of partial state that must be retained. A theoretically efficient kernel can still be slow if it repeatedly transfers the same data or creates synchronization gaps. I would use hardware constraints and a cost model to select candidate tiles, then profile representative shapes and check peak memory, transfer volume, compute utilization, and tail latency.

### Q6. How would you validate an INT8 or mixed-precision optimization?

**Model answer:**

I would validate both numerical quality and system performance. First I would define acceptable error metrics for the model or operator, use representative calibration and evaluation data, and compare against a higher-precision reference. I would inspect sensitive layers, saturation, rounding, scale and zero-point handling, accumulator width, and boundary values. Then I would benchmark latency, throughput, memory, and conversion overhead. The optimization is acceptable only if the measured performance gain is meaningful and the accuracy change remains inside the agreed threshold.

### Q7. A model is correct on the CPU reference but wrong on the NPU. How do you debug it?

**Model answer:**

I would reproduce the issue deterministically and compare intermediate tensors at partition or operator boundaries. A binary-search strategy across graph stages can identify the first divergence. I would then check layout and stride conversions, quantization parameters, broadcasting, padding, shape inference, unsupported corner cases, synchronization, buffer lifetime, and numerical tolerance. I would reduce the graph to the smallest failing subgraph, fix the responsible layer, and add a regression test that runs against both the reference and NPU paths.

### Q8. How does your EDA experience strengthen your software-engineering judgment?

**Model answer:**

EDA work combines large graphs, strict correctness requirements, difficult optimization objectives, and expensive real-world validation. In `[EDA project]`, I used `[graph/search/optimization/data-structure technique]` to address `[placement, routing, timing, verification, simulation, or another problem]`. The important engineering lesson was `[trade-off or invariant]`. That experience transfers to AI-code evaluation because it trained me to distinguish a plausible heuristic from a correct algorithm, reason about scale, and demand evidence for performance and correctness claims.

### Q9. Describe a C++ ownership or lifetime bug you found in a systems project.

**Model answer:**

In `[project]`, `[symptom]` was caused by `[dangling reference, invalidated iterator, double ownership, race, or resource leak]`. I reproduced it with `[minimal test or sanitizer]`, traced the resource lifetime, and found that ownership was ambiguous between `[components]`. I fixed it by expressing ownership with `[value semantics, unique_ptr, shared ownership only if necessary, or an RAII wrapper]` and made non-owning access explicit. I added a regression test and used `[AddressSanitizer, ThreadSanitizer, Valgrind, or another tool]` to verify the fix.

### Q10. Where did Python fit into your compiler, AI, or EDA workflow?

**Model answer:**

I used Python for `[model conversion, orchestration, test generation, log analysis, benchmarking, or data validation]`, while `[C++ or another language]` handled `[performance-critical component]`. Python made iteration and integration faster, but I kept boundaries explicit through `[API, binding, subprocess, file format, or RPC]`. I added schema and type checks at the boundary and measured serialization or transfer overhead rather than assuming it was negligible.

### Q11. Describe a performance optimization you measured across software and hardware layers.

**Model answer:**

The initial symptom was `[latency, throughput, utilization, or memory problem]`. Profiling showed the bottleneck was `[specific component]`, not `[initial suspicion]`. I changed `[implementation or schedule]` to `[optimization]` and benchmarked identical representative workloads with warm-up and repeated runs. I reported `[median/p95/p99/throughput/memory]`, verified output correctness, and checked that the bottleneck did not simply move to another stage. The measured result was `[defensible number]`, with the trade-off of `[complexity, memory, portability, or compilation time]`.

### Q12. Two AI-generated implementations both pass the provided tests. How would your background help you rank them?

**Model answer:**

I would not treat the provided tests as proof. I would identify each implementation's invariants and assumptions, derive boundary and adversarial cases, analyze time and memory complexity, and inspect numerical, ownership, concurrency, and hardware-specific behavior. For compiler or NPU code, I would pay particular attention to shapes, layouts, aliasing, side effects, precision, synchronization, and unsupported operations. I would rank the solutions using concrete evidence and explain any conditional verdict—for example, one may be faster only when inputs satisfy an unstated constraint.

### Q13. What is the strongest technical claim on your résumé, and how would you prove it?

**Model answer:**

My strongest claim is `[claim]`. I can support it by explaining the baseline, workload, environment, measurement method, and my exact contribution. The result changed from `[before]` to `[after]` on `[representative workload]`, while `[correctness or quality measure]` remained within `[threshold]`. I would also disclose limitations such as `[hardware, dataset, model size, or scenario]`, because a narrowly true measured claim is stronger than a broad claim I cannot reproduce.

### Q14. What follow-up questions should you expect after describing a project?

**Answer:**

Expect the interviewer to ask:

- What exactly did you implement yourself?
- Why did you choose that design instead of the main alternative?
- What invariant made the solution correct?
- What input or environment caused it to fail?
- How did you measure the result?
- Which test would have caught the bug earlier?
- What trade-off did the change introduce?
- What would you redesign today?

Prepare one concrete, defensible answer for each of these questions for every major project listed on your résumé.

## Public references

- [Alignerr application process](https://www.alignerr.com/process)
- [Alignerr Software Engineer (AI Training) role](https://www.alignerr.com/jobs/b0a0d4b1-9510-4e22-8c9f-96e12663a94f)
- [Alignerr Senior Software Engineer — AI Evaluation and Benchmarks role](https://www.alignerr.com/jobs/6a214113-5128-4ebb-a746-39dd0224ecb2)
- [Publicly reported matrix-rotation interview question](https://www.glassdoor.com/Interview/I-have-given-a-2d-array-I-need-to-rotate-2d-array-couter-clockwise-in-place-The-last-column-become-the-first-row-The-seco-QTN_7877396.htm)
