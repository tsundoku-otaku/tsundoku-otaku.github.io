---
title: Novel reader snippets
titleTemplate: Guides
description: How custom CSS/JS snippets, regex rules, and the Tsundoku JS object work in the novel reader.
---

# Novel reader snippets

Custom CSS/JS snippets and the `window.Tsundoku` object run only in the **WebView** novel
renderer. Regex find/replace and embedded CSS/JS toggles also apply, with the rules below.

## Enable WebView

1. Open a novel chapter.
2. Open reader settings, set **Rendering mode** to **WebView**.
3. Open the **Advanced** tab for regex, embedded toggles, and snippets.

The Native (TextView) renderer ignores all WebView snippets and embedded CSS/JS. This is a limit
of Android's `TextView`, not a setting.

## Content pipeline

Chapter text passes through this order before it reaches either renderer:

1. Strip chapter title (if enabled)
2. Normalize markup
3. Regex and find/replace rules
4. Force lowercase (if enabled)
5. Translate (if enabled)
6. Sanitize for the target renderer

Sanitize, step 6, removes `<noscript>` and HTML comments always, and media when **Block media**
is on. It strips chapter `<script>`/`<style>` unless the matching **Embedded CSS / JS** toggle is
on (TextView always strips both). Your snippets are injected after this, so they always run.

## Injection order (WebView)

The chapter loads with a base `<style>`, the `window.Tsundoku` object, and theme variables already
in `<head>`. After `onPageFinished` the app re-injects:

1. Combined style block: base style, then **Embedded CSS**, then enabled **CSS snippets** in
   list order.
2. `window.Tsundoku` refresh, then **Embedded JS**, then enabled **JS snippets** in list order.

For CSS **later rules win**. Use `!important` to
beat a base rule when **Source CSS priority** forces base styles.

## The `window.Tsundoku` object

Available to JS after page load:

```js
window.Tsundoku.novelUrl            // string
window.Tsundoku.currentChapter      // { id, title, number, path, url }
window.Tsundoku.chapters            // [ { id, title, number, path, url }, ... ] in order
window.Tsundoku.runtime.isEditMode
window.Tsundoku.runtime.isInfScroll
window.Tsundoku.runtime.textSelectionBlocked
window.Tsundoku.runtime.forcedLowercase
```

`runtime.*` values are re-pushed on chapter change and on settings change. Need another field
exposed? Open an issue or PR with the use case.

## Embedded CSS / JS toggles

In **Advanced**:

- **Embedded CSS** (on by default): keep `<style>` tags shipped inside the chapter HTML/EPUB.
- **Embedded JS** (off by default): keep `<script>` tags shipped inside the chapter.

Off means those tags are stripped during sanitize. Both are WebView only.

## Safe snippet practices

- Scope to chapter content with id/class selectors; avoid `*`.
- Guard against missing nodes and against infinite loops:

```js
const target = document.querySelector('.chapter-content');
if (target) {
  // your logic
}
```

## Troubleshooting

- **Snippet ignored:** confirm renderer is **WebView** and the snippet is enabled. Native mode
  has a raw-HTML option to inspect the source markup.
- **Style conflict:** move the rule to a later snippet, or add `!important`. Base styles may carry
  `!important` when **Source CSS priority** is set, so match it.
