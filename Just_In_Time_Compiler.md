
# JIT - Just-In-Time Compilation

Just-In-Time (JIT) compilation is a technique where code is compiled to machine code at runtime, allowing the system to optimize for actual usage patterns and data. JIT is a key technology in many modern programming languages and virtual machines.

## Main Program Execution Techniques

1. **Interpretation** – Executes source code line by line (e.g., Python, Ruby).
2. **Static (Ahead-of-Time, AOT) Compilation** – Compiles to machine code before execution (e.g., C, C++).
3. **JIT (Just-in-Time) Compilation** – Compiles code at runtime, often for frequently used ("hot") code paths (e.g., Java, .NET, V8 for JavaScript).
4. **Bytecode Execution** – Compiles to portable bytecode interpreted by a VM (e.g., JVM, CPython).
5. **Hybrid Compilation** – Combines bytecode + JIT (e.g., Java HotSpot, .NET CLR).
6. **Transpilation** – Converts code from one high-level language to another (e.g., TypeScript → JS).
7. **AOT-JIT Mixed (Tiered Compilation)** – Starts interpreting, then compiles optimized native code on-the-fly.
8. **Dynamic Recompilation** – Continuously re-optimizes code during execution (used in emulators, VMs).
9. **Partial Evaluation** – Pre-computes known expressions at compile-time for speed.
10. **Self-hosting / Meta-circularity** – Compiler or interpreter written in the same language, used for bootstrapping.

---

## Comparison Table

| Technique         | When Compiled      | Optimization Level | Example Languages/VMs      |
|-------------------|-------------------|--------------------|---------------------------|
| Interpretation    | Never             | Low                | Python, Ruby              |
| AOT Compilation   | Before execution  | High (static)      | C, C++, Rust              |
| JIT Compilation   | At runtime        | High (dynamic)     | Java, .NET, V8, PyPy      |
| Bytecode          | Before execution  | Medium             | Java (JVM), Python (CPython) |
| Hybrid/Tiered     | Mixed             | Very High          | Java HotSpot, .NET CLR    |

---


## Static Compiler (AOT)

The static (AOT) compiler analyzes and optimizes the program before it runs. All optimizations are based on the code itself, not on actual runtime data.

language - self (jit)
eg -
pypy 
spider monkey

## JIT Compiler

The JIT compiler runs the program and optimizes it on the fly, using information gathered at runtime (such as which functions are called most often, or what types of data are used). This allows for aggressive, targeted optimizations that static compilers can't always perform.


**Key points:**

- JIT can optimize based on actual runtime values and usage patterns.
- Especially effective for code that runs repeatedly (loops, hot functions).
- Can de-optimize or re-optimize as program behavior changes.


**Examples of JIT engines:**

- PyPy (Python)
- V8 (JavaScript, used in Chrome/Node.js)
- SpiderMonkey (JavaScript, Firefox)
- Java HotSpot (JVM)
- .NET CLR (CoreCLR, Mono)


**Languages with JIT support:**

- Java, C#, JavaScript, Python (PyPy), Dart, Julia, and more.

---

## Use Cases & Tradeoffs

| Pros of JIT                | Cons of JIT                        |
|----------------------------|------------------------------------|
| Fast execution for hot code| Slower startup (warm-up time)      |
| Adapts to real usage       | More complex implementation        |
| Can use runtime info       | Higher memory usage                |
| Enables dynamic languages  | Platform-specific machine code     |

---

## Summary

- Use **AOT** for fast startup, predictable performance, and smaller binaries.
- Use **JIT** for long-running applications, dynamic languages, and when runtime optimization is valuable.

