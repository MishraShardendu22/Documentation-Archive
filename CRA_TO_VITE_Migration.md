# Migration Notes: CRA → Vite

## 1. Stricter ESLint Rules

**Issue:** Unused imports, unused variables, and undefined identifier errors surfaced.
**Cause:** CRA’s linting defaults were lax; Vite setups together with updated ESLint configs are stricter.
**Resolution:**

* Deleted unused code.
* Adjusted `.eslintrc` only where intentional violations were needed.

---

## 2. React JSX Handling

**Issue:** Vite couldn’t compile `.js` files with JSX; errors claiming JSX wasn’t enabled.
**Cause:** Vite enforces proper file extensions and requires a specific plugin.
**Resolution:**

* Installed and configured `@vitejs/plugin-react`.
* Renamed `.js` files containing JSX to `.jsx`.

---

## 3. TailwindCSS v4 (CSS-First Configuration)

**Issue:** `tailwind.config.js` went missing after upgrading to TailwindCSS v4.
**Cause:** v4 dropped automatic config file generation. It adopts a CSS-first approach: no `tailwind.config.js` by default([Tailwind CSS][1], [Stack Overflow][2], [JavaScript in Plain English][3]).
**Resolution:**

* Accept v4’s CSS-first model: configure everything in CSS using `@theme`, `@plugin`, `@source`, for example([Stack Overflow][4], [Tailwind CSS][1], [Medium][5]).
* If maintaining a legacy JS config is necessary, create `tailwind.config.js` manually and reference it in CSS using the `@config` directive:

  ```css
  @import "tailwindcss";
  @config "./tailwind.config.js";
  ```

  ([Stack Overflow][4]).

---

## 4. Environment Variables

**Issue:** `process.env.REACT_APP_*` references broke after migration.
**Cause:** CRA exposes env vars via `process.env.REACT_APP_*`, while Vite uses `import.meta.env.VITE_*`.
**Resolution:**

* Renamed all `.env` keys from `REACT_APP_*` → `VITE_*`.
* Updated code to use `import.meta.env.VITE_*`.

---

## Summary

Main blockers addressed:

1. Stricter linting.
2. JSX file handling (`@vitejs/plugin-react`, `.jsx` extension).
3. Tailwind v4’s shift to CSS-first configuration (drop or manually import `tailwind.config.js`).
4. Environment variable syntax changes.

Once resolved, build and runtime under Vite were stable.

[1]: https://tailwindcss.com/blog/tailwindcss-v4?utm_source=chatgpt.com "Tailwind CSS v4.0"
[2]: https://stackoverflow.com/questions/73474335/tailwind-config-js-file-not-being-generated?utm_source=chatgpt.com "tailwind.config.js file not being generated"
[3]: https://javascript.plainenglish.io/no-tailwind-config-js-in-tailwind-css-v4-heres-what-changed-97bb277eeacd?utm_source=chatgpt.com "No tailwind.config.js in Tailwind CSS v4? Here's What ..."
[4]: https://stackoverflow.com/questions/79450336/how-can-i-setup-tailwind-config-js-with-angular-tailwindcss-v4-application?utm_source=chatgpt.com "How can I setup tailwind.config.js with Angular & ..."
[5]: https://medium.com/%40kidaneberihuntse/tailwind-css-v4-1-has-no-tailwind-config-js-heres-how-to-customize-everything-with-theme-11a19b108963?utm_source=chatgpt.com "🚀 Tailwind CSS v4.1 Has No tailwind.config.js — Here's ..."
