---
title: Novel reader (Advanced)
titleTemplate: Guides
description: Custom CSS/JS snippets, the Tsundoku JS objects, theme variables, and the HTML structure of the WebView novel renderer.
---

# Novel reader snippets

Custom CSS/JS snippets, theme variables, and the `window.Tsundoku` objects run only in the
**WebView** renderer. The Native (TextView) renderer ignores all of them, a limit of Android's
`TextView`, not a setting.

## Enable WebView

1. Open a novel chapter.
2. In reader settings set **Rendering mode** to **WebView**.
3. Open the **Advanced** tab for the controls below.

## Advanced tab controls

- **Enable embedded CSS** (on): keep `<style>` tags and inline `style` attributes shipped inside
  the chapter HTML/EPUB. Off strips them.
- **Enable embedded JS** (off): keep `<script>` tags shipped inside the chapter. Off strips them.
- **Source CSS priority** (off): off makes the reader's base CSS use `!important` so it wins over
  source styles. On drops `!important` so source styles win.
- **CSS Snippets** / **JavaScript Snippets**: named blocks you add, each independently
  enabled/disabled. Enabled ones run on every chapter.
- **Regex Find & Replace**: see [Regex rules](#regex-rules).

## Content pipeline

Chapter text passes through this order before rendering:

1. Strip chapter title (if enabled)
2. Normalize: plain-text for `.txt`/`.text` URLs, otherwise auto-detect HTML / Markdown and
   convert Markdown to HTML
3. Regex find/replace rules
4. Force lowercase (if enabled)
5. Translate (if enabled)
6. Sanitize for the renderer

Sanitize always removes `<noscript>` and HTML comments. It strips `<script>`/`<style>`/inline
styles unless the matching **Enable embedded** toggle is on, and strips media when **Block images
and videos** is on. Your snippets are injected after sanitize, so they always run.

## Injection order

The page is built with the theme variables, base `<style>`, the `window.Tsundoku` and
`window.TsundokuTheme` objects, and your enabled CSS already in `<head>`. After `onPageFinished`
the app re-injects the `Tsundoku` object, then your enabled JS snippets in list order.

For CSS, **later snippets win**. Use `!important` to beat a base rule when **Source CSS priority**
is off.

## `window.Tsundoku`

```js
window.Tsundoku.novelUrl          // string, normalized novel URL
window.Tsundoku.currentChapter    // { id, title, number, path, url }
window.Tsundoku.chapters          // [ { id, title, number, path, url }, ... ] in reading order

window.Tsundoku.runtime.isEditMode             // boolean
window.Tsundoku.runtime.isInfScroll            // boolean
window.Tsundoku.runtime.textSelectionBlocked   // boolean
window.Tsundoku.runtime.forcedLowercase        // boolean
```

Each chapter object has `id` (number, `-1` if unknown), `title` (string), `number` (number, `-1`
if unknown), `path` (relative URL) and `url` (absolute URL).

`runtime.*` is re-pushed on chapter change and on settings change.

The reader also adds these internal fields. They are read-only, do not set them yourself:

```js
// Added by the scroll tracker (present once a chapter has loaded)
window.Tsundoku.runtime.infiniteScrollInstalled  // boolean, listener installed
window.Tsundoku.runtime.loadingNext              // boolean, a next chapter is loading
window.Tsundoku.runtime.setLoadingNext(v)        // function, used by the app
window.Tsundoku.runtime.knownDividerCount        // number, cached divider count
window.Tsundoku.runtime.lastBoundaryUpdate       // number, timestamp (ms)

// Added in edit mode
window.Tsundoku.runtime.editInputBound           // boolean, edit listener installed
window.Tsundoku.runtime.inputListener            // function ref, the edit listener

// Infinite-scroll boundary helpers (on window, not runtime)
window.chapterBoundaries            // [ { chapterId, startOffset, height }, ... ]
window.addChapterBoundary(id, startOffset, height)
window.updateChapterBoundaries()    // recompute from the divider DOM
```

Need another field exposed? Open an issue or PR with the use case.

## `window.TsundokuTheme`

The active reader colors as a JS object. Same values as the CSS variables below, for when you need
them in JS. Every key:

```js
window.TsundokuTheme.mdSysColorPrimary
window.TsundokuTheme.mdSysColorOnPrimary
window.TsundokuTheme.mdSysColorPrimaryContainer
window.TsundokuTheme.mdSysColorOnPrimaryContainer
window.TsundokuTheme.mdSysColorSecondary
window.TsundokuTheme.mdSysColorOnSecondary
window.TsundokuTheme.mdSysColorSecondaryContainer
window.TsundokuTheme.mdSysColorOnSecondaryContainer
window.TsundokuTheme.mdSysColorTertiary
window.TsundokuTheme.mdSysColorOnTertiary
window.TsundokuTheme.mdSysColorTertiaryContainer
window.TsundokuTheme.mdSysColorOnTertiaryContainer
window.TsundokuTheme.mdSysColorError
window.TsundokuTheme.mdSysColorOnError
window.TsundokuTheme.mdSysColorErrorContainer
window.TsundokuTheme.mdSysColorOnErrorContainer
window.TsundokuTheme.mdSysColorBackground
window.TsundokuTheme.mdSysColorOnBackground
window.TsundokuTheme.mdSysColorSurface
window.TsundokuTheme.mdSysColorOnSurface
window.TsundokuTheme.mdSysColorSurfaceVariant
window.TsundokuTheme.mdSysColorOnSurfaceVariant
window.TsundokuTheme.mdSysColorOutline
window.TsundokuTheme.tsundokuReaderBackground
window.TsundokuTheme.tsundokuReaderText
```

Each value is a hex string like `"#006A6A"`.

## CSS theme variables

Defined on `:root`, usable in any CSS/JS snippet:

```css
/* Reader colors (track your selected reader theme) */
--tsundoku-reader-background
--tsundoku-reader-text

/* Material 3 palette */
--md-sys-color-primary        --md-sys-color-on-primary
--md-sys-color-secondary      --md-sys-color-on-secondary
--md-sys-color-tertiary       --md-sys-color-on-tertiary
--md-sys-color-error          --md-sys-color-on-error
--md-sys-color-background     --md-sys-color-on-background
--md-sys-color-surface        --md-sys-color-on-surface
--md-sys-color-surface-variant --md-sys-color-on-surface-variant
--md-sys-color-outline
/* plus the *-container / on-*-container variants */
```

Example, styling blockquotes inside the chapter to follow the reader theme:

```css
tsundoku-chapter blockquote {
  color: var(--md-sys-color-on-surface-variant);
  border-left: 3px solid var(--md-sys-color-primary);
}
```

## HTML structure and selectors

Chapter content is wrapped in a custom element:

```html
<tsundoku-chapter
  data-tsundoku-chapter="1"
  data-chapter-id="123"
  data-chapter-title="Chapter 1"
  data-chapter-number="1"
  data-chapter-path="/novel/ch-1"
  data-chapter-url="https://.../novel/ch-1">
  ...chapter content...
</tsundoku-chapter>
```

Other markers a snippet can target:

| Selector | Meaning |
| --- | --- |
| `tsundoku-chapter` | The chapter content wrapper (target this, not `body`). |
| `#tsundoku-chapters-container` | Wraps all loaded chapters in infinite-scroll mode. |
| `.tsundoku-chapter-divider` | Boundary between chapters in infinite scroll (carries the same `data-chapter-*` attributes). |
| `.tsundoku-plain-text` / `[data-tsundoku-plain-text="1"]` | Chapter detected as plain text. |
| `[data-tsundoku-markdown="1"]` | Content rendered from Markdown. |
| `[data-tsundoku-editable="1"]`, `[contenteditable="true"]` | Edit mode is active. |
| `.td-tts-highlight-bg`, `.td-tts-highlight-underline`, `.td-tts-highlight-outline` | Applied by TTS to the spoken segment. Style these to restyle the read-aloud highlight. |

Reserved ids the app owns. Do not reuse them: `tsundoku-custom-style` (your injected CSS),
`edit-mode-style`, `next-chapter-btn-container`.

## Regex rules

Each rule has a title, a find pattern, a replacement, and toggles: **Use Regex** (literal text when
off), case sensitive, and match whole word. Rules run at pipeline step 3, on the chapter HTML
before rendering, so patterns may need to account for tags.

## Safe snippet practices

- Scope to `tsundoku-chapter` or your own ids/classes; avoid bare `*`.
- Do not edit InnerHtml or Body directly.
- Guard against missing nodes:

```js
const target = document.querySelector('tsundoku-chapter');
if (target) {
  // your logic
}
```

- Snippets re-run per chapter and on settings change. Keep this in mind when adding
  nodes/listeners) so repeated runs don't stack.

## Troubleshooting

- **Snippet ignored:** confirm renderer is **WebView** and the snippet is enabled. Native mode has
  a **Show raw HTML** option to inspect the source markup. This option only shows the source's contents, not HTML/CSS/JS injected by the app or your snippets.
- **Style won't apply:** move the rule to a later snippet, or add `!important` (base styles carry
  `!important` unless **Source CSS priority** is on).
