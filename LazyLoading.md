# LazyExperienceSection Documentation
## Purpose
`LazyExperienceSection` is a React component designed to **lazy-load user experiences** only when the section becomes visible in the viewport. This prevents unnecessary API calls, optimizes performance, and provides a smooth loading fallback (skeleton → data).
---
## Component Flow
1. **Initial Render**
   * Section mounts with a `ref` for intersection observation.
   * `loaded = false`, `loading = false`.
   * Shows `<LoadingState />` placeholder.
2. **Intersection Trigger**
   * When **25% of the section** is visible (`threshold: 0.25`), the `useIntersectionObserver` hook sets `hasBeenVisible = true`.
3. **Fetch Logic**
   * `fetchExperiences()` runs **only once**, guarded by:
     ```ts
     if (loaded || loading) return
     ```
   * `loading = true` → `<ExperienceSkeleton />` displayed.
   * API call → `experiencesAPI.getAllExperiences()`.
   * If success: `experiences` state is updated, `loaded = true`.
   * If failure: shows toast error.
4. **Final State**
   * `<ExperienceSection experiences={experiences} />` is rendered after load.
   * No overlap, no double-fetch.
---
## Key Fix for Overlap
Problem: On **page refresh**, `threshold: 0.05` caused the observer to trigger **too early**, racing the skeleton and the data UI.
Solution: Using `threshold: 0.25` ensures at least 25% of the section is visible before fetch starts, preventing race conditions and overlap.
---
## Component States
- **Idle (before scroll)**
  ```tsx
  <LoadingState />
  ```
- **Loading (after visible)**
  ```tsx
  <ExperienceSkeleton />
  ```
- **Loaded (success)**
  ```tsx
  <ExperienceSection experiences={experiences} />
  ```
- **Error (failure)**
  * Toast: `"Failed to load experiences"`
---
## API Dependency
`experiencesAPI.getAllExperiences()` must return:
```ts
{
  data: Experience[]
}
```
If the response is not an array, fallback is `[]`.
---
## Usage
```tsx
<LazyExperienceSection />
```
- Place inside your main page layout.
- Ensure it has `id="experience-section"` for anchor links.
