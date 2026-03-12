## Why is size of long differrent in unix based os and windows ? 

### Reason: Different **Data Models**

Operating systems chose different **ABI (Application Binary Interface) data models** when moving to 64-bit systems.

---

### Linux / Unix Model → **LP64**

Used by:

* Linux
* macOS
* BSD

| Type      | Size |
| --------- | ---- |
| `int`     | 32   |
| `long`    | 64   |
| `pointer` | 64   |

Meaning:

```
L → long = 64
P → pointer = 64
```

---

### Windows Model → **LLP64**

Used by:

* Windows 64-bit

| Type        | Size |
| ----------- | ---- |
| `int`       | 32   |
| `long`      | 32   |
| `long long` | 64   |
| `pointer`   | 64   |

Meaning:

```
LL → long long = 64
P → pointer = 64
```

---

### Why Windows Chose LLP64

Microsoft wanted **backward compatibility with 32-bit Windows code**.

Older Windows APIs used `long` heavily.

Example (Win32 API):

```cpp
LONG value;
```

If `long` changed to **64-bit**, it would break:

* binary compatibility
* system libraries
* existing applications

So Windows kept:

```
long = 32-bit
```

and introduced **`long long` for 64-bit integers**.

---

### Why Linux Chose LP64

Unix systems redesigned ABI during the 64-bit transition.

They made:

```
long = 64-bit
pointer = 64-bit
```

because `long` was already commonly used for:

* memory sizes
* file offsets
* pointer arithmetic

---

### Practical Consequence

Code like this is **not portable**:

```cpp
long x = 10000000000;
```

Works on **Linux**, overflows on **Windows**.

Portable solution:

```cpp
#include <cstdint>

int64_t x;
```

---
