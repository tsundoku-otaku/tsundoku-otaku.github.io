---
title: Custom sources
titleTemplate: Guides
description: Build your own novel source by pointing to a website and selecting page elements, basing it on an installed source, or importing JSON.
---

# Custom sources

A **custom source** is a novel source you build yourself, with minimal to no coding, by pointing to a website and telling it which parts of each page to read. Useful for a small or personal site that has no existing extension, or to add a mirror/renamed copy of a site an extension already covers.

Open **Browse - Custom sources** (pen icon) and tap **add**. There are three ways to start:

- **Guided selector wizard** - navigate the site in a WebView and tap the parts you want. Best for a site that does not use javascript/ajax for fetching content.
- **Start from an installed source** - pick an installed source to delegate fetching/parsing to it by swapping in a different base URL. Best if the site has a similar template/api as the source.
- **Import JSON** - paste a source definition or load it from a file. **Fill template** drops a ready-to-edit skeleton. Good if you find pasting in html from your PC easier or you want to import your backed source json.

**Note**:
A proper source is almost always better than custom sources.


::: tip How a source is organised
Every source describes three kinds of page:

- **Listing** - popular / latest / search results (a grid or list of novels).
- **Details** - one novel's page (title, cover, description, author, status, chapter list).
- **Content** - one chapter's text.

You provide a **URL** for each listing and a **CSS selector** for each piece of data. The wizard writes the selectors for you, but understanding them helps you fix one by hand.
:::

## Basing on an installed source

Pick **Start from an installed source** and choose any installed extension, JS plugin, or other source. The app then **delegates** all fetching and parsing to that source: popular, latest, search, details, and the chapter list all come from the original.

You need to set different base URL on your custom source so it will be rewritten every outgoing request from the original source to the site you set. You may want to use this if you:

- Follow a site that moved to a new domain before the extension is updated.
- Use a faster or unblocked mirror of a supported site.
- The site uses the same template/internal structure(look, url paths) as the source

Notes:

- Some sources, if not built properly or in a unconventional way (example: having an api url but not using the baseUrl to build it) may fail to work.

## The guided selector setup

This is a set list of steps that helps you build a source without potentially using a PC.

First you enter the **source name** and **base URL**. Then, for each step, you use a toggleable Element Selector button to select the elements the setup requests.


### Steps

The steps may vary depending on the options you choose that describe the source.

1. **Popular novels** - on the popular/trending listing, tap a novel's **cover** (optional) and **title** inside one card.
2. **Popular pagination** (optional) - navigate to page 2 when prompted. The page-1 and page-2 URLs are diffed automatically to build a `{page}` pattern (handles `?page=2`, `/page/2`, trailing-number forms).
3. **Latest novels** (optional) - same as popular, for the latest-updated list. If you skip it, the popular card selectors are reused.
4. **Search** - type a probe word (for example `a` or `world`) and run the site's search. (see [search probe](#search)).
5. **Search pagination** (optional) - navigate to page 2 of the results to derive the search `{page}` pattern.
6. **Novel details** - on a novel's page, tap **title**, **description**, **cover**, and **genres**. The current URL is saved as the **sample novel URL** so the reading test has a known novel to test.
7. **Chapter list** - tap one or more **chapter rows/links**. For numbered sites you can instead capture a [chapter URL pattern](#generated-chapter-lists) by tapping the first and last chapter.
8. **Chapter list pagination** (optional) - navigate to page 2 of a long chapter list to derive its `{page}` pattern.
9. **Chapter content** - tap the block holding the chapter text. There is also an **Auto-detect** button that scores elements by text density and picks the paragraph-heavy block.
10. **Review** - shows a summary and runs the full [test](#test-before-you-save).

### Tapping and confirming a selector

- Toggle **selection mode**, then tap an element. A confirmation dialog shows the match and lets you walk **up** to the parent or **down** to a child before confirming, so you can widen or narrow the pick.
- For list steps (popular, latest, chapters), tapping two cards is enough.
- A **Reverse chapters** toggle on the chapter step handles sites that list newest first.

## Editor fields

You can open the editor and set or correct any field by hand. The wizard fills most of these for you.

Most input fields have a webview icon to open a single step setup.

### Identity

- **Name**
- **Base URL** - the site root, `http(s)://...`. Used to resolve relative links and as `{baseUrl}` in URL templates.
- **Language** - BCP-47 code (`en`, `zh`, ...). Controls which language filter the source appears under.

### Listing URLs

- **Popular URL** - page-1 URL of the popular/trending list.
- **Latest URL** (optional) - latest-updated list, falls back to popular if blank.
- **Search URL** - search endpoint with a `{query}` placeholder, for example `https://example.com/?s={query}`.
- **Paged URL templates** (optional, one per list) - page-2+ template with `{page}` (and `{query}` for search). Only needed if the wizard's pagination diff did not capture it.
- **Use POST for search** - enable if the site searches via a form POST instead of a URL query.

### List-item selectors (per list)

These are relative to one card in the list.

- **List item** - selector matching each novel card/row (required for any list you enable).
- **Link** - the title/cover anchor that holds the novel URL.
- **Title**
- **Cover** - the cover image. The probe reads lazy-load attributes (`data-src`, `srcset`, CSS `background-image`, ...) automatically, so usually just point at the `img`.
- **Next page** (optional) - a "next" link, as an alternative to a `{page}` template.

### Details selectors

- **Title** (required)
- **Author**
- **Description**
- **Genres / tags** - one or more elements. their text is joined.
- **Cover**
- **Status** - the element whose text says ongoing/completed/etc. See **Status mapping** below.

### Chapters

A source needs **either** a scraped chapter list **or** a generated URL pattern.

Scraped list:

- **Chapter list** - selector for each chapter item.
- **Chapter link / name / date** - relative to the item.
- **Chapter date format** (optional) - a Java `SimpleDateFormat` pattern such as `MMM d, yyyy`. Needed when dates are worded rather than numeric.
- **Index link selector** (optional) - if the chapter list lives on a separate page linked from the details page, point at that link.
- **Chapter list paged URL** (optional) - page-2+ template with `{page}` and `{novelUrl}`, preferred over a next-page link.

### Content

- **Content selector** (required) - the block holding the chapter text.
- **Content fallback selectors** (optional, comma-separated) - tried in order if the primary returns nothing.
- **Elements to remove** (optional, comma-separated) - selectors stripped from the chapter before display (ads, prev/next links).

### Advanced

- **Status mapping** - map this site's status words to `ongoing` / `completed` / `hiatus` / `cancelled`, for example `连载=ongoing, 完结=completed`. Matching is case-insensitive substring.
- **Custom headers** - one `Key: Value` per line, for example `Referer: https://example.com`. Use for sites that gate content on a referer or user agent.
- **Test search query** - the probe word used when testing search (defaults to `a`).
- **Sample novel URL** - a novel **details-page** URL the reading test opens directly. Set this if popular/latest/search are not configured yet, so you can still test reading.

## Generated chapter lists (URL pattern)

Some sites are built in such a way where chapter URLs are numbered (`/novel/abc/chapter-1`, `chapter-2`, ...). Instead of a list selector, give a **Chapter URL pattern**:

- `{n}` - the chapter number.
- `{novelUrl}` - the current novel's path, so one pattern works for **every** novel on the site, not only the one you captured it from.

Example: `{novelUrl}/chapter-{n}`. Then either:

- set **first** and **last** chapter numbers, or
- point a **chapter count selector** at the element on the details page that shows the total (its digits are read as the last number).

A **name template** (default `Chapter {n}`) controls the displayed chapter names.


## Search

The search URL is a template containing `{query}` (and optionally `{page}`, `{baseUrl}`). The query is URL-encoded at request time. With **Use POST for search** on, the part after `?` is sent as a form body instead.

### Search probe (how the URL is captured)

The probe looks for the set word in the resulting URL (trying URL-encoded, `%20`-space, and raw forms) and replaces the first match with `{query}`. It checks the result host matches your base URL. If your probe word does not appear in the URL (for example the site searches via POST or an opaque id),
the probe cannot derive a template, and you have to set the **Search URL** by hand instead.

## Test before you save

Use **Run test** in the editor and at the end of the wizard before saving. It exercises each configured section:

- **Popular / Latest / Search** - fetches page 1 and reports how many novels parsed.
- **Reading** - opens one novel, then pulls details, the chapter list, and the first chapter's content, showing a short preview of the start and end of the text.

The reading test needs a novel to open. It uses the **Sample novel URL** if set, otherwise it tries popular, latest, then search. Read the per-step results to see exactly which selector failed.

## Saving (validation)

On save, a custom source must have:

- a **name** and an `http(s)` **base URL**.
- at least one listing URL (**popular**, **latest**, or **search**), with a matching **list-item** selector.
- a details **title** selector.
- a **content** selector.
- **either** a chapter-list selector **or** a chapter URL pattern.

Names must be unique, and base URLs must be unique.

## Working with JSON

Every custom source is stored as a JSON file and can be exported, edited, and re-imported.

- **Export** - from a source's menu, export to JSON.
- **Import** - paste the JSON or load a file.
- **Fill template** - shows a json template.


```json
{
  "name": "My Source",
  "baseUrl": "https://example.com",
  "language": "en",
  "popularUrl": "https://example.com/popular?page={page}",
  "searchUrl": "https://example.com/?s={query}",
  "postSearch": false,
  "reverseChapters": false,
  "selectors": {
    "popular": { "list": ".novel-item", "link": "a", "title": ".title", "cover": "img" },
    "details": { "title": "h1", "author": ".author", "description": ".summary", "genre": ".tags a", "status": ".status", "cover": ".cover img" },
    "chapters": { "list": ".chapter-list li", "link": "a", "name": "a", "date": ".date" },
    "content": { "primary": ".chapter-content", "fallbacks": [], "removeSelectors": [], "removeBoilerplate": true }
  }
}
```

For a generated chapter list, replace the `chapters` block's `list` with a pattern instead:

```json
"chapters": { "urlPattern": "{novelUrl}/chapter-{n}", "firstNumber": 1, "lastNumber": 500, "nameTemplate": "Chapter {n}" }
```

`null` and omitted optional fields behave the same. Latest and search fall back to the `popular` selectors when their blocks are `null`.

## Troubleshooting
- **Make sure the data is included in the HTNL**: if the site uses Javascript/Ajax to load data, then it can't be scraped.
- **Listing is empty** - the **list-item** selector matched nothing, or the listing URL is wrong. Re-open the wizard on that step, or test the selector in your browser's DevTools.
- **Chapter shows an error instead of text** - the content selector returned nothing. Check the **content selector**, add **content fallback selectors**.
- **Missing chapters / wrong order** - toggle **Reverse chapters**. For generated lists, check **first/last** numbers or the **count selector**.
- **Covers don't load** - point the **cover** selector at the `<img>` itself. The probe handles lazy-load attributes, but a wrapper `div` may not carry the URL.

## Tips

- **Prefer stable selectors.** Class names like `.chapter-content` survive layout changes better than positional ones like `div:nth-child(3)`.
- **If you want to skip latest/search/popular** if the source is difficult to parse or you just want to parse novels, pass a dummy selector for one of lists, then use mass import to directly add novels.
