# Compiler Self-Hosting, JavaScript Engines, and Native Compilation Models
---
## 1. Self-Hosting Explained
**Definition:**  
A language is *self-hosted* when its compiler is written in the same language it compiles.
**Purpose:**
- Demonstrates the language is powerful and stable enough to implement complex systems.
- Removes dependency on foreign toolchains.
- Enables direct optimization using the language’s own features.
**Advantages:**
1. Proof of maturity and completeness.  
2. Simplified maintenance (only one language for compiler and ecosystem).  
3. Improved dogfooding — compiler evolves with real usage.  
4. Easier portability and long-term sustainability.
**Examples:**
| Language | First Compiler | Self-Hosted Since | Initial Host Language |
|-----------|----------------|-------------------|------------------------|
| C | Assembly | Early 1970s | Assembly |
| Go | `6g`, `8g`, `5g` | Go 1.5 (2015) | C |
| Rust | rustc (OCaml) | ~2013 | OCaml |
| Swift | LLVM-based | In transition | C++ |
| TypeScript | tsc (JS/TS) | N/A (depends on JS runtime) | TypeScript |
---
## 2. Why Self-Host
| Benefit | Description |
|----------|--------------|
| **Independence** | No need for another compiler to build yours. |
| **Consistency** | Only one syntax and runtime for entire toolchain. |
| **Symbolic strength** | Proves compiler-quality stability. |
| **Faster evolution** | Features tested on the compiler itself. |
**Performance impact:** Minimal.  
Compiler speed is algorithm-dependent, not language-dependent.
---
## 3. JavaScript Engine Architecture
### Overview
JavaScript doesn’t compile to native machine code ahead of time. It runs **inside an engine** written in C++ (sometimes Rust). The engine interprets and JIT-compiles JS dynamically.
**Major Engines:**
| Engine | Language | Used In | Characteristics |
|---------|-----------|---------|-----------------|
| **V8** | C++ | Chrome, Node.js, Deno | Fastest, aggressive JIT (Ignition + TurboFan). |
| **SpiderMonkey** | C++ / Rust | Firefox | Memory-safe, moderate speed. |
| **JavaScriptCore** | C++ | Safari | Efficient, low memory focus. |
### Execution Pipeline (V8 Example)
``` md
JS Source → Parser → AST → Bytecode (Ignition) → Optimized Machine Code (TurboFan)
```
### Why JS Needs an Engine
- Browser sandbox security (no direct system access).
- Portability across OS/CPU.
- Dynamic typing and runtime reflection require interpretation or JIT.
---
## 4. Compilation Models
| Model | Description | Example |
|--------|--------------|---------|
| **Interpreted** | Executes source directly | Early JavaScript |
| **JIT (Just-In-Time)** | Compiles hot code at runtime | Modern JS engines |
| **AOT (Ahead-Of-Time)** | Compiles fully before execution | C, Go |
| **Hybrid** | Combines JIT and AOT | JVM, .NET, PyPy |
| **Self-hosted** | Compiler written in its own language | Go, Rust |
---
## 5. WebAssembly (Wasm)
**Purpose:**  
To run safe, fast, portable binaries in browsers.
**Properties:**
- Binary, typed format.  
- Sandboxed like JS.  
- Near-native execution speed.  
- Language-agnostic (C, C++, Rust, Zig, Grain, MoonBit, etc.).
**Pipeline Example:**
``` md
Rust Source → LLVM → Wasm Binary (.wasm) → Browser Executes
```
**JS Relationship:**  
JS loads and calls Wasm modules — Wasm handles compute-heavy logic.
---
## 6. TypeScript’s Native Compiler (Go Port)
**Project Name:** Corsa  
**Goal:** Rewrite TypeScript compiler (`tsc`) in Go for native performance.
**Why Go:**
- Native compilation to machine code.
- Strong concurrency model for faster type-checking.
- Built-in GC fits compiler workloads.
- Easier parallel builds than Node’s single-thread loop.
**Benefits:**
- ~10× faster type-checking.  
- No Node.js dependency.  
- Precompiled binary distribution.  
**Status (2025):**
- CLI and core type system implemented.
- Project builds and language service in progress.
- Aiming for TypeScript 7.0 feature parity.
---
## 7. JS vs Native Compilers
| Feature | JavaScript Compiler | Native (Go/Rust/C++) Compiler |
|----------|--------------------|-------------------------------|
| Execution | Requires engine (V8) | Direct binary execution |
| Speed | JIT optimized | AOT compiled |
| Portability | High (any browser) | OS/arch dependent |
| Safety | Sandboxed | Depends on language |
| Self-hosting | Limited (depends on JS engine) | Full |
---
## 8. Future Direction
- **JS as orchestration layer**  
  WebAssembly handles computation; JS handles interactivity and glue code.
- **Language flexibility**  
  Any language compiling to Wasm can run on the web safely.
- **Post-JS evolution**  
  Newer Wasm-first languages (MoonBit, Grain, Lys) aim to replace JS for performance-critical web apps.
---
## 9. References
- [ECMAScript Spec](https://tc39.es/ecma262/)
- [V8 Docs](https://v8.dev/docs)
- [SpiderMonkey Internals](https://firefox-source-docs.mozilla.org/js/)
- [WebAssembly Spec](https://webassembly.github.io/spec/core/)
- [TypeScript Native Port](https://devblogs.microsoft.com/typescript/typescript-native-port/)
- [Go 1.5 Self-Hosting](https://go.dev/doc/go1.5)
- [Rust Bootstrap](https://doc.rust-lang.org/nightly/rustc/bootstrap.html)
---
Assembly is not written **in** any language.
It **is** a low-level human-readable representation of machine code.
However, assemblers (the programs that convert assembly into machine code) are written in higher-level languages, usually:
- **C** (e.g., GNU `as`, NASM)
- **C++** (e.g., MASM parts)
- **Rust** (some modern experimental assemblers)
So:
- Assembly → instruction set for CPU
- Assembler → software tool written in C/C++ that translates it to binary machine code.
