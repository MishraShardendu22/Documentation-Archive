# Browser Architecture

---

### **1. Data**

* **Role:** Persistent and session-level storage system.
* **Works:** Maintains cookies, cache, history, localStorage, sessionStorage, IndexedDB.
* **Example:** Login tokens, saved preferences, cached CSS/JS for faster reloads.

---

### **2. UI Interface**

* **Role:** Visible user layer.
* **Works:** Displays address bar, bookmarks, tabs, navigation controls.
* **Example:** You click a bookmark or back button; triggers engine actions.

---

### **3. Browser Engine**

* **Role:** Coordinator between UI and rendering/JS/network subsystems.
* **Works:** Controls rendering pipeline, updates pages, handles reflows and repaint requests.
* **Example:** Detects that a new DOM node was added, tells rendering engine to repaint.

---

### **4. Rendering Engine**

* **Role:** Converts web code (HTML, CSS) into pixels.
* **Works (sequence):**

  1. Parse HTML → DOM
  2. Parse CSS → CSSOM
  3. Combine → Render Tree
  4. Layout → Calculate geometry
  5. Paint → Rasterize and draw to screen
* **Example:** Displays YouTube home feed layout.

---

### **5. JavaScript Engine**

* **Role:** Executes JavaScript and interacts with DOM APIs.
* **Works:** Parses → Compiles → Executes → Updates DOM and event loop queue.
* **Example:** Handles “Like” click, dynamically updates element color or count.

---

### **6. Network**

* **Role:** Handles all HTTP/HTTPS, DNS, SSL, WebSocket connections.
* **Works:**

  1. Performs DNS lookup
  2. Establishes TCP/SSL
  3. Fetches resources
  4. Streams data to rendering and JS engines
* **Example:** Downloads HTML, JS, images for a site.

---

### **7. Timers (Browser Component)**

* **Role:** Event scheduling subsystem tied to the **Event Loop**.
* **Works:**

  * Keeps track of delayed or periodic tasks.
  * Integrates with event loop queue to execute callbacks when time expires.
  * Internal to browser core (not only `setTimeout`).
* **Example:**

  * Rendering engine requests a reflow in 16ms for next frame (via timer queue).
  * JS engine schedules microtasks (Promises) and macrotasks (timeouts).

---

### **Real Sequence Example**

When you open `https://twitter.com`:

1. **UI Interface** → User enters URL.
2. **Network** → Sends HTTP request.
3. **Data** → Reads cookies, cache.
4. **Browser Engine** → Starts page load orchestration.
5. **Rendering Engine** → Parses HTML/CSS → draws page.
6. **JS Engine** → Executes scripts → updates UI.
7. **Timers** → Schedules frame updates, async JS, and network polling.

---

# HTML 

Nodelist 
  |-> Display
  |-> Interact

realtion nodes nad doc 
Load File
Raw Bytes
Charecter Token h1 p html 

what is a nodelist 


css om
some time even css can stop js from exexuting active algos being made so the css om is ready before js starts exexuting

html css work parallely 
js in hte end 
but css cna actually block js from exexuting

Reference

![Image 1 - browser diagram](./Images/image1.jpeg)
![Image 2 - browser diagram](./Images/image2.jpeg)
![Image 3 - browser diagram](./Images/image3.jpeg)
![Image 4 - browser diagram](./Images/image4.jpeg)