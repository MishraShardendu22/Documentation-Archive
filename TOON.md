# TOON (Token-Oriented Object Notation) – A Compact Serialization Format for LLMs

## 1. Definition & Purpose

TOON is a **serialization format** designed for sending structured data into Large Language Models (LLMs) with fewer tokens than JSON. ([GitHub][1])
It’s intended as a **drop-in replacement** for JSON when the consumer is an LLM (rather than a machine API). ([GitHub][1])
It aims for human‐readability + token‐efficiency. ([DEV Community][2])

---

## 2. Why Use TOON

* JSON has a lot of syntactic noise (braces `{}`, brackets `[]`, quotes `"`, repeated keys) that count as tokens when passed to LLMs. ([DEV Community][2])
* When you have many uniform items (e.g., an array of objects all with the same fields), repeating the field names is wasteful. TOON avoids that. ([GitHub][1])
* Reported token savings: typically ~30-60% in favourable cases (uniform arrays) compared to JSON. ([DEV Community][2])

---

## 3. When TOON Is (and Isn’t) Appropriate

**Use TOON when:**

* Data is largely flat or tabular: an array with many items, each with the same fields. ([GitHub][1])
* Token cost or context‐window size is a constraint (LLM input).
* You can afford to impose the format and your pipeline can handle conversion to/from JSON.

**Avoid / Think twice when:**

* Data is deeply nested or highly irregular (many levels of nested objects/arrays) – TOON may lose its advantage. ([GitHub][1])
* Data is highly non‐uniform (arrays where each item has different fields).
* You need broad compatibility with existing tooling that expects JSON; conversion overhead may not justify gain.

---

## 4. Syntax & Structural Rules

Below are the core rules of TOON (based on v2.0 spec). ([GitHub][1])

### 4.1 Objects (key‐value)

* Use `key: value` syntax.
* For nested objects, use indentation (spaces) rather than braces.

  ```
  user:  
    name: Alice  
    age: 30  
  ```
* If the value is a primitive (string, number, boolean, null) you print it directly (with quoting rules below).

### 4.2 Primitive Arrays

* Represent arrays by indicating length in brackets: `[N]:` prefix.
  Example:

  ```
  [3]: apple,banana,cherry  
  ```
* Values separated by delimiter (default comma).

### 4.3 Tabular Arrays (uniform objects)

* When you have an array of objects each with identical primitive‐only fields: you declare the field names once, then list rows.
* Syntax: `[N,]{field1,field2,field3}:`  (Here `,` inside `[]` indicates comma‐delimiter; alternative delimiters allowed)
  Then rows (one per item) indent and give values separated by delimiter.
  Example:

  ```
  users[2,]{id,name,role}:
    1,Alice,admin
    2,Bob,user
  ```

  This means `users` is array length 2, each object has fields `id,name,role`.

### 4.4 Mixed / Non‐uniform Arrays

* If array items are not uniform objects, you use `-` markers (like YAML) to list them:

  ```
  [3]:
    - 42
    - name: Alice
    - ["nested", 5]
  ```

### 4.5 Quoting and Delimiters

* Default delimiter is comma `,`. Alternative delimiters (tab `\t`, pipe `|`) supported. ([GitHub][3])
* Strings are only quoted when necessary. You *do not* quote by default if safe. Quoting needed when:

  * Empty string
  * Leading/trailing whitespace
  * Contains structural characters `: [] {} ,` or the delimiter
  * Identical to keywords `null`, `true`, `false`
  * Looks like a number but is intended as string
    Example: `hello world` → `hello world` (no quotes)
    Example: `,comma` → `" ,comma"` (quotes)

### 4.6 Length Markers & Field Declarations

* Array length markers help the LLM or parser validate structure. Example: `[3]`.
* In tabular arrays you also declare fields `{field1,field2,…}`.
* Optional prefix like `#` can mark lengths: `[ #3, ]` if configured. (Configuration dependent) ([GitHub][3])

---

## 5. Full Example – JSON → TOON

**JSON:**

```json
{
  "users": [
    { "id": 1, "name": "Alice", "role": "admin" },
    { "id": 2, "name": "Bob",   "role": "user"  }
  ]
}
```

**TOON:**

```
users[2,]{id,name,role}:
  1,Alice,admin
  2,Bob,user
```

Another example with nested structure:

```
metadata:
  version: 1.0
  author: John Doe
items[2,]{sku,qty,price}:
  A1,2,9.99
  B2,1,14.50
tags[3]: alpha,beta,gamma
```

---

## 6. Ecosystem & Implementations

* The official spec and implementation (TypeScript) reside in repository toon‑format/toon. ([GitHub][1])
* Community / language ports exist: Python, Go, C++, .NET, R. ([GitHub][3])
* Documentation versioned: e.g., SPEC v2.0 mentioned. ([GitHub][1])

---

## 7. Best Practices & Tips

* In your pipeline: keep your internal representation in e.g., JSON (or your native data structures). Convert to TOON **just before** passing into the LLM. Then convert back if needed.
* Benchmark token counts for your specific model/tokenizer: savings vary. ([DEV Community][4])
* Make sure your data is suitable: arrays with same‐shape objects. Otherwise TOON may not gain.
* Add length markers when you expect the LLM to produce structured output and you want it to check counts.
* Use code‐blocks when embedding TOON in prompts (e.g., `toon ... `).
* Ensure quoting rules are handled correctly: strings with commas, brackets etc must be quoted or escaped.
* When writing encoder/decoder, maintain exact spec (indentation, delimiter, headers) so LLM parsing is reliable.

---

## 8. Limitations & Caveats

* If data is deeply nested (many levels of objects and arrays) the indentation overhead may reduce token savings. Indeed benchmarks show in those cases JSON may even outperform TOON. ([GitHub][1])
* TOON is optimized for LLM‐input context; it’s not necessarily optimal for general storage or APIs where JSON is dominant.
* Tooling ecosystem is newer and less mature than JSON/YAML; you may need custom conversion.
* Delimiter conflicts or quoting mistakes can lead to parse issues.
* Token savings depend on model & tokenizer; not guaranteed.
