# React Native and Expo

Rules specific to React Native, assuming Expo and Expo Router. Read `react.md` first for structure, data access, and state; this file covers only what running on a device changes.

> **Provisional.** These are researched defaults, not yet settled by shipping an app. Confirm or cut each rule against real screens before treating it as house style.

Mechanical React Native rules — raw text outside `<Text>`, inline styles, unused styles, colour literals, platform-specific component placement — belong to `eslint-plugin-react-native` and are not repeated here.

---

## Everything in the bundle is public

There is no server component to hide behind. Every line of JavaScript and every `EXPO_PUBLIC_*` variable is inlined into a bundle any user can unpack and read.

- **Secrets never reach the client.** A private API key, a `service_role` key, or a signing secret lives on a server the app calls, never in app config.
- **Treat the client as untrusted.** Anything the app enforces, a modified app can skip. Authorisation happens on the server.
- **Tokens go in secure storage**, not plain key-value storage. Plain storage is a readable file on the device.

## One implementation, until the platforms genuinely differ

Default to one component for iOS and Android. Reach for `Platform.select`, `Platform.OS`, or a `.ios.tsx` / `.android.tsx` pair only when the behaviour, not just the appearance, differs — and when it does, that divergence is a decision worth a comment.

A change is not verified until it has run on both platforms. Simulator on one is a first check, not the finish line.

## Layout is not CSS

Web layout instincts mislead here.

- **No cascade.** Styles do not inherit from parents, except text styles through nested `<Text>`. A font set on a container `View` does nothing.
- **Flex defaults to `column`**, and every `View` is a flex container.
- **Respect the safe area.** Content under the notch, the home indicator, or a status bar is content the user cannot tap. Use safe-area insets at the screen level rather than hard-coded padding.
- **Every screen with a text input handles the keyboard.** Check that the focused field and its submit button stay visible with the keyboard open.

## Accessibility is opt-in

There is no semantic HTML doing the work by default. A `Pressable` wrapping an icon announces nothing useful until you say what it is.

- **Every custom control gets a role and a label.** `role` (or `accessibilityRole`) and `accessibilityLabel` on anything pressable that is not a plain text button.
- **State goes in `accessibilityState`**: selected, checked, disabled, expanded.
- **Group what reads as one thing.** A card with a title, subtitle, and price wants `accessible` on the container so a screen reader announces it once, not three times.
- **Touch targets are at least 44pt on iOS and 48dp on Android.** Use `hitSlop` when the visual element must stay smaller.
- **Test with VoiceOver and TalkBack**, not only by reading the props.

## Lists

- **Unbounded data goes in a virtualised list.** A `ScrollView` with `.map` renders every row up front; fine for a dozen static rows, a memory and start-up problem for anything that grows.
- **Keep list items light.** Minimal nesting, no effects, thumbnails rather than full images. Detail belongs on its own screen.
- **Stable keys from the data**, never the index, for any list that can reorder, insert, or delete.

Which list component to use is a project choice: record it in the host `CLAUDE.md` under "What we're not using".

## Navigation

- **Route params are untrusted strings.** A deep link can open any route with any params. Parse them at the screen boundary into a real type, as with any other input (`typescript.md`).
- **Every route is an entry point.** A screen reached by a deep link has no previous screen and may have no loaded state. It loads what it needs, and back navigation still goes somewhere sensible.

## The network is unreliable and the app gets backgrounded

- **Every remote call has a loading, error, and offline state.** Model them as a union rather than booleans. A spinner that never resolves on a train is a bug, not an edge case.
- **Backgrounding replaces page unload.** The app can be suspended at any moment and resumed hours later. Clean up subscriptions and timers, and refresh stale data on return to the foreground (`AppState`) rather than assuming it is current.

## Threads

JavaScript runs on one thread; the UI renders on another. When the JS thread is busy, taps and JS-driven animations stall.

- **Animations and gestures run on the UI thread** (Reanimated, native-driver animations), not in JavaScript state updates.
- **Keep heavy work out of render and out of the tap handler.** Parse, sort, or compute large data once, ahead of time or off the interaction path.

## Dependencies

- **Prefer an Expo SDK module** over a third-party package doing the same job. It is versioned with the SDK and tested against it.
- **Check New Architecture support before adding a library** with native code. An unmaintained native module is the most common reason an SDK upgrade stalls.
- **Know what ships over the air.** JavaScript and asset changes can ship as an update; anything touching native code or config needs a new store build and a review cycle. Plan a change's release path before building it.
