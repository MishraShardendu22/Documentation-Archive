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

Language - Self (JIT)

eg -

- pypy
- spider monkey

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

Below is the **complete and updated technical documentation** on **Compilation Models**, with JavaScript engine history and the Self language details included.

---

# **Compilation Models**

## 1. Static Compilation

**Definition:**
Entire source code is compiled ahead of time into native machine code before execution.

**Characteristics:**

- Produces standalone binaries.
- No runtime compilation overhead.
- Requires recompilation for each target architecture.

**Pros:**

- Fast runtime performance.
- No interpreter or runtime dependency.
- Ideal for production and embedded environments.

**Cons:**

- Slower compile time.
- Larger binaries.
- No runtime adaptivity.

**Examples:**
C, C++, Rust (default), Go.

---

## 2. Interpreted Execution

**Definition:**
Source or bytecode is read and executed directly by an interpreter, line by line or instruction by instruction.

**Characteristics:**

- No separate compilation stage.
- Portable across architectures.
- Slower due to instruction dispatch overhead.

**Pros:**

- Fast iteration during development.
- Easy debugging and dynamic evaluation.
- Simple deployment (only interpreter needed).

**Cons:**

- Slow runtime execution.
- High CPU usage.
- Cannot exploit hardware-level optimizations.

**Historical Context:**
Before 2008, most JavaScript engines were purely interpreted.

- **SpiderMonkey (Netscape/Firefox)** – interpreted until TraceMonkey (2008).
- **Rhino (Mozilla, Java-based)** – interpreted.
- **Chakra (Internet Explorer pre-9)** – interpreted.
- **SquirrelFish (Safari pre-2008)** – interpreted until SquirrelFish Extreme introduced JIT.
- These engines executed code directly from source or simple bytecode.

**Examples:**
Python (CPython), Ruby (MRI), PHP, Early JavaScript engines.

---

## 3. JIT (Just-In-Time) Compilation

**Definition:**
Code is compiled at runtime into machine code, often from an intermediate bytecode form.

**Characteristics:**

- Compiles only the parts of code actually executed ("hot" code).
- Performs runtime profiling for optimization.
- Uses caches for already compiled sections.

**Pros:**

- Adaptive optimization for runtime conditions.
- Balances startup speed and performance.
- Efficient memory usage compared to AOT-only systems.

**Cons:**

- Compilation overhead during execution.
- Slower startup due to runtime analysis.
- Complex implementation.

**Examples:**

- **Java (JVM HotSpot)** – classic tiered JIT with profile-driven optimization.
- **C# (.NET CLR)** – uses JIT per method invocation.
- **JavaScript (modern engines):**

  - **V8 (Chrome)** – baseline interpreter + optimizing JIT (TurboFan, Maglev).
  - **SpiderMonkey (Firefox)** – interpreter + JIT tiers (IonMonkey, WarpMonkey).
  - **JavaScriptCore (Safari)** – LLInt + DFG JIT + FTL JIT tiers.
- **Self programming language** – origin of many modern adaptive JIT concepts; introduced dynamic, type-feedback-driven optimization in the 1980s.

---

## 4. AOT (Ahead-Of-Time) Compilation

**Definition:**
Code is compiled into native machine code before execution, without runtime compilation.

**Characteristics:**

- Build-time generation of platform-specific binaries.
- Eliminates runtime compiler overhead.
- Can use PGO for optimization based on previous runs.

**Pros:**

- Fast startup.
- Predictable performance.
- Reduced runtime dependencies.

**Cons:**

- No dynamic optimization.
- Larger executable size.
- Must recompile for each platform or environment.

**Examples:**
Rust, Swift, C, GraalVM Native Image, .NET Native.

---

## 5. Hybrid and Advanced Compilation Models

### a. Tiered Compilation

**Definition:**
Starts with interpretation or baseline compilation, then dynamically recompiles frequently executed ("hot") sections using JIT.

**Process:**

1. Interpret code initially.
2. Profile execution frequency.
3. Promote hot code paths to optimized machine code.
4. Deoptimize if assumptions fail.

**Used In:**
JVM HotSpot, .NET CLR, JavaScript V8, and SpiderMonkey.

**Advantages:**

- Quick startup.
- Adaptive optimization of critical code paths.
- Balances performance and responsiveness.

---

### b. Tracing JIT

**Definition:**
Monitors program execution, identifies hot traces (linear execution paths), and compiles them into optimized native code.

**Characteristics:**

- Works on traces instead of methods or functions.
- Specially suited for dynamic languages.

**Used In:**
PyPy (Python), LuaJIT, TraceMonkey (Firefox 2008).

**Advantages:**

- Very efficient for tight loops.
- Reduces redundant optimizations.
- Introduced key innovations for modern adaptive JIT.

---

### c. Profile-Guided Optimization (PGO)

**Definition:**
Combines runtime profiling data with AOT compilation to optimize layout, inlining, and branch prediction.

**Process:**

1. Collect runtime behavior statistics.
2. Feed data into compiler.
3. Recompile for optimized binary.

**Used In:**
LLVM, GCC, MSVC, Rust (with `-C profile-use`).

**Advantages:**

- Near JIT-level optimization for static binaries.
- Retains AOT predictability.

---

### d. Hybrid AOT + JIT

**Definition:**
Combines precompiled native code with runtime JIT for flexibility and speed.

**Characteristics:**

- Core system code compiled AOT.
- Dynamic or user-level code compiled JIT.
- Used in large VMs and mobile runtimes.

**Used In:**
GraalVM, Android ART, .NET Core ReadyToRun.

**Advantages:**

- Fast startup.
- Adaptive runtime performance.
- Ideal for large, long-lived applications.

---

## 6. Historical Evolution Summary

| Era          | Model               | Example Systems           | Notes                                                       |
| ------------ | ------------------- | ------------------------- | ----------------------------------------------------------- |
| 1970s–1980s  | Interpreted         | Lisp, BASIC               | Early simplicity favored interpretive models.               |
| 1980s–1990s  | Static              | C, C++                    | Performance-focused static compilation dominates.           |
| 1990s        | JIT Emerges         | Self, Smalltalk, Java     | Self pioneers adaptive JIT optimizations.                   |
| 2000s        | Tiered JIT          | JVM, .NET CLR             | Mainstream adoption of hybrid models.                       |
| 2008–Present | Hybrid & Multi-tier | V8, SpiderMonkey, GraalVM | Mix of interpretation, JIT, and AOT for optimal efficiency. |

---

## Summary Table

| Model            | Compile Time    | Runtime Optimization | Startup Speed | Portability | Used By              |
| ---------------- | --------------- | -------------------- | ------------- | ----------- | -------------------- |
| Static           | Build-time      | None                 | Fast          | Low         | C, C++, Go           |
| Interpreted      | None            | None                 | Slow          | High        | Python, Early JS     |
| JIT              | Runtime         | High                 | Medium        | Medium      | JVM, CLR, JS engines |
| AOT              | Build-time      | None                 | Fast          | Medium      | Rust, Swift          |
| Tiered           | Mixed           | High                 | Medium        | Medium      | JVM, CLR, V8         |
| Tracing JIT      | Runtime         | Very High            | Medium        | Medium      | PyPy, TraceMonkey    |
| PGO              | Build + Profile | Medium               | Fast          | Medium      | GCC, LLVM            |
| Hybrid (AOT+JIT) | Mixed           | High                 | Fast          | Medium      | GraalVM, ART         |
