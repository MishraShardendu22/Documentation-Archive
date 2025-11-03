# Cross-Origin-Opener-Policy (COOP) and Cross-Origin-Embedder-Policy (COEP) Issues

Your page’s COOP and **Cross-Origin-Embedder-Policy (COEP)** headers don’t match what the browser expects, so it blocks cross-origin access or SharedArrayBuffer usage.

Typical causes:

* Missing or conflicting headers:

  ```md
  Cross-Origin-Opener-Policy: same-origin
  Cross-Origin-Embedder-Policy: require-corp
  ```

* You’re loading cross-origin iframes or scripts that don’t send proper CORS headers.
* Running site on `localhost` without matching COEP/COOP setup.

Fix:

1. Add correct headers from backend:

   ```js
   res.setHeader("Cross-Origin-Opener-Policy", "same-origin");
   res.setHeader("Cross-Origin-Embedder-Policy", "require-corp");
   ```

2. Ensure all third-party resources (scripts, images, fonts) have `crossorigin` set and send `Access-Control-Allow-Origin`.
3. For local dev, disable isolation or match both headers across origins.
