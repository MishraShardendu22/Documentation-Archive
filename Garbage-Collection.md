# Garbage Collection
When a program runs, it needs memory for data. Since we don’t always know in advance how much memory we’ll need, we use **dynamic memory allocation**.
In low-level languages like **C**, memory management is manual — you allocate and free it yourself.
```c
#include <stdio.h>
#include <stdlib.h>
int main() {
    int *c = malloc(sizeof(int) * 10); // allocate memory
    c[0] = 10;
    printf("%d\n", c[0]);
    free(c); // manually free memory
    return 0;
}
```
Modern languages such as **Go**, **Python**, and **JavaScript** have **automatic garbage collection (GC)**, which frees unused memory automatically.
---
## Common Memory Management Problems
1. **Memory Leak** – memory is allocated but never freed.
2. **Use After Free** – accessing memory after it has been freed.
3. **Double Free** – freeing the same memory block twice.
These can cause crashes, unpredictable behavior, or even security vulnerabilities.
---
## Garbage Collection Approaches
### 1. Reference Counting
- Each object tracks how many references point to it.
- When the count reaches zero, the object’s memory is freed.
**Drawbacks:**
- Adds performance overhead.
- Must carefully increment/decrement references.
- Fails with **cyclic references** (e.g., `A → B` and `B → A` keep each other alive).
---
### 2. Mark-and-Sweep Algorithm
Used by most modern garbage collectors.
**Steps:**
1. **Roots:** Start from root objects (globals, stack variables).
2. **Mark:** Recursively mark all reachable objects.
3. **Sweep:** Free unmarked (unreachable) objects.
This ensures only memory that can no longer be accessed is reclaimed.
---
## Limitations
Even advanced GCs (like in Java) can’t detect *logical* memory retention.
Example: a small object still holds a reference to a large data structure.
Though the data is no longer needed, it remains in memory until the reference is removed.
---
## Summary
- Manual memory management gives control but risks errors.
- Garbage collection automates cleanup but adds overhead.
- Good software design minimizes unnecessary references to help GC work efficiently.
