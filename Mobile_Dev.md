# Official Documentation

## 1. **Integration with Existing Apps** (Android + iOS)

Step-by-step guide to embed React Native components into native apps:

* **Android**:

  * Set up directory structure (move existing project under `/android`)
  * Install NPM dependencies (`package.json`)
  * Add React Native Gradle plugin and setup in `settings.gradle`, `build.gradle`, and your app’s Gradle config
  * Use a `ReactActivity` to host RN views
    ([React Native][1])

* **iOS**:

  * Mirror directory setup (use `/ios` subfolder)
  * Use CocoaPods to integrate React Native dependencies (`Gemfile`, `Podfile`, `pod install`)
  * Use `RCTRootView` to host RN UI inside native `UIViewController`
    ([React Native][1], [nishabe.medium.com][2])

---

## 2. **Communication Between Native and React Native**

Mechanisms to pass data and callbacks between native and RN layers:

* Pass initial props from native into React Native via `getLaunchOptions` in `ReactActivityDelegate` (Android)
* Establish callbacks and properties from RN back to native
* Patterns for cross-component communication across the bridge
  ([React Native][3])

---

## 3. **Native Modules & TurboModules**

When you need JS to call platform-specific features:

* **Legacy Native Modules**:

  * Write Java/Kotlin or Swift/Objective-C code
  * Expose methods via `@ReactMethod`
  * Call from RN via `NativeModules`
    ([React Native][4])

* **TurboModules**:

  * Modern alternative with codegen support
  * Define typed APIs (using TypeScript/Flow)
  * Generate native bindings for both platforms
    ([React Native][5])

---

## Community Tutorial (Callstack – Mastodon Brownfield Integration)

Real-world guide applying to iOS and Android native apps:

* Fork the native app, set up RN as a submodule
* Create `package.json`, integrate via Gradle (Android) and CocoaPods (iOS)
* On iOS: configure `AppDelegate.swift`, `RCTRootView`, routing logic
* On Android: align Gradle settings, create `ReactActivity`, configure `ReactNativeHost`
* Use Metro bundler to run RN screens seamlessly within the native app
  ([callstack.com][6])

---

## StackOverflow Tips

* Embed RN into native apps using `RCTRootView` for iOS or `ReactActivity` for Android
  ([Stack Overflow][7])
* Confirm communication via native module setup steps in official RN docs
  ([Stack Overflow][8])

---

## Summary Table

| Platform | Official Documentation                                          | Highlights                                               |
| -------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| Android  | Integration guide, native modules, communication                | Embed RN via Gradle, ReactActivity, native module setup  |
| iOS      | Integration guide, `RCTRootView`, native modules, communication | Embed RN via CocoaPods, RCTRootView, native module setup |
| Shared   | TurboModules guide, community brownfield blog                   | Typed native modules, real-world Mastodon case study     |

---

## Next Steps

1. Pick your target platform.
2. Use “Integration with Existing Apps” guide as skeleton.
3. Embed RN view (`ReactActivity` / `RCTRootView`) where needed.
4. Wire up data and callbacks via props and native modules.
5. Refer to TurboModules for typed, modern native APIs.
6. Use the Mastodon brownfield walkthrough for concrete implementation details.

[1]: https://reactnative.dev/docs/integration-with-existing-apps?utm_source=chatgpt.com "Integration with Existing Apps"
[2]: https://nishabe.medium.com/how-to-integrate-react-native-code-with-an-existing-ios-app-655c61a65b8c?utm_source=chatgpt.com "How to integrate react native code with an existing iOS app?"
[3]: https://reactnative.dev/docs/communication-android?utm_source=chatgpt.com "Communication between native and React Native"
[4]: https://reactnative.dev/docs/legacy/native-modules-intro?utm_source=chatgpt.com "Native Modules Intro"
[5]: https://reactnative.dev/docs/turbo-native-modules-introduction?utm_source=chatgpt.com "Native Modules: Introduction"
[6]: https://www.callstack.com/blog/how-to-integrate-react-native-with-an-existing-app?utm_source=chatgpt.com "How to Integrate React Native With an Existing App"
[7]: https://stackoverflow.com/questions/51327263/embed-react-native-app-inside-existing-ios-android-app?utm_source=chatgpt.com "Embed react native app inside existing ios/android app"
[8]: https://stackoverflow.com/questions/73244909/integration-of-react-native-with-existing-android-apps-and-adding-native-modules?utm_source=chatgpt.com "Integration of react native with existing android apps and ..."
