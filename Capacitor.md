# CapacitorJS + Next.js Full Documentation
---
## 0. Next.js + Capacitor Known Problems
Capacitor expects **static export** builds. Next.js dynamic/SSR features break.
1. **Dynamic Routes (`[id].js`, `[slug].js`)**
   * Must use `getStaticPaths` + `getStaticProps`.
   * `getServerSideProps` **not supported** (no backend in Capacitor app).
   * Workaround: pre-generate pages or fetch data from an API.
2. **API Routes**
   * Next.js API routes **do not work** in static export.
   * Use an external backend or serverless functions.
3. **Image Optimization (`next/image`)**
   * Requires server, so it fails.
   * Fix: in `next.config.js` →
   ```js
   images: { unoptimized: true }
   ```
4. **Middleware / Edge Functions**
   * Not supported. Disable them.
5. **Internationalization (i18n)**
   * Static export only supports default locale.
   * Workaround: export separate builds per locale.
6. **Dynamic Imports**
   * Works, but must be properly bundled before export.
   * Avoid `next/dynamic` with SSR only.
7. **Large Bundle Size**
   * Next.js bundles are heavy (300KB–500KB+).
   * Optimize with `analyze` and `next.config.js` tweaks.
8. **Hot Reload**
   * Can load dev server in Capacitor, but it’s slower than React Native hot reload.
---
## 1. Introduction to Capacitor
CapacitorJS is a cross-platform runtime for building **native iOS/Android apps using web apps** (Next.js, React, Vue, etc.).
It wraps your web code in a WebView and exposes **native APIs** (camera, storage, push notifications).
- Website: [https://capacitorjs.com](https://capacitorjs.com)
- Core idea: *"Web code + native bridge + app stores"*
---
## 2. Installation & Setup
### Install Capacitor
```bash
npm install @capacitor/core @capacitor/cli
```
### Initialize
```bash
npx cap init
```
- `appId`: unique reverse-domain (e.g., `com.user.portfolio`)
- `appName`: your app’s name
- `webDir`: Next.js output folder (`out`)
Example `capacitor.config.ts`:
```ts
import { CapacitorConfig } from '@capacitor/cli';
const config: CapacitorConfig = {
  appId: 'com.mishra.portfolio',
  appName: 'Portfolio',
  webDir: 'out',
  server: { androidScheme: 'https' }
};
export default config;
```
### Add Platforms
```bash
npx cap add android
npx cap add ios
```
---
## 3. Workflow
1. Build Next.js for static export:
   ```bash
   next build && next export
   ```
   Output goes to `/out`.
2. Sync with native platforms:
   ```bash
   npx cap sync
   ```
3. Open in IDEs:
   ```bash
   npx cap open android
   npx cap open ios
   ```
4. Run via Android Studio/Xcode.
---
## 4. Running Apps
### Android
```bash
npx cap open android
```
- Runs in Android Studio.
- Use an **emulator** or physical device.
### iOS
```bash
npx cap open ios
```
- Runs in Xcode.
- Requires macOS.
---
## 5. Plugins & Native APIs
Install official Capacitor plugins:
```bash
npm install @capacitor/camera @capacitor/geolocation @capacitor/haptics
```
Example: Camera
```ts
import { Camera, CameraResultType } from '@capacitor/camera';
const photo = await Camera.getPhoto({
  resultType: CameraResultType.Uri,
  quality: 90
});
console.log(photo.webPath);
```
---
## 6. Live Reload
Instead of rebuilding every time, point Capacitor to dev server:
```ts
server: {
  url: "http://192.168.1.5:3000", // your Next.js dev server
  cleartext: true
}
```
Run:
```bash
npm run dev
npx cap run android --livereload
```
---
## 7. Building for Production
1. Build Next.js static export:
   ```bash
   npm run build
   next export
   ```
2. Copy to native:
   ```bash
   npx cap copy
   ```
3. Build in Android Studio/Xcode for release.
---
## 8. Updating Workflow
When making web changes:
```bash
next build && next export
npx cap copy
```
Optional full sync (if plugins/config changed):
```bash
npx cap sync
```
---
## 9. Common Issues
- **Android Studio not found** → set `CAPACITOR_ANDROID_STUDIO_PATH` env.
- **White screen on launch** → check `webDir` path in config.
- **Dynamic routes fail** → pre-render with `getStaticPaths`.
- **API calls to `/api/...` fail** → move to external backend.
---
## 10. Learning Path
1. Learn Capacitor basics (config, build, sync).
2. Learn how Next.js static export works.
3. Try core plugins (Camera, Geolocation).
4. Connect your app with a real backend (REST/GraphQL).
5. Optimize for performance (bundle analysis).
6. Setup CI/CD for Android/iOS builds.
7. Publish to **Google Play** / **App Store**.
---
