# Prompt: settings menu with Share and English/Portuguese language

Use this prompt with a coding assistant to add a settings menu to the Bastão prototype (`index.html`).

## Context

Bastão is a single-file prototype (`index.html`: HTML, CSS and plain JavaScript, no frameworks, no backend). All interface text already lives in the `TEXT.en` dictionary and is read with `t(key, params, lang)` and `tPlural(key, n, params, lang)`. Static elements use the `data-i18n` attribute and are filled by `applyStaticText()`. Business rules are pure functions and the acceptance tests run on load with `runAcceptanceTests()`.

## Task

Add a settings button fixed in the top right corner of the page. It opens a small menu with two options:

1. **Language:** switch the interface between English and Brazilian Portuguese.
2. **Share:** share the link of the page.

## Requirements

### Settings button and menu
- Round icon button (gear) in the top right corner of the header, with `aria-label`, `aria-haspopup="true"` and `aria-expanded`.
- The menu is a white card with rounded corners and a soft shadow, same visual language as the rest of the page (Montserrat, primary green `#004A42`).
- Closes with Esc, with a click outside, after the language changes and after the link is shared or copied. It stays open when the manual copy field is shown. Focus returns to the button.
- Works at 375 px width without horizontal scrolling.

### Language
- Add a `TEXT.pt` dictionary with every key that exists in `TEXT.en`. Missing keys fall back to English (already handled by `t()`).
- Language switch as two options (English, Português) that show which one is active (`aria-pressed`).
- Switching language re-renders static text, indicators, table, status labels, warnings, dialogs and history. The history is stored as structured entries, so old entries are translated too.
- Update `<html lang>` (`en` or `pt-BR`) and the document title.
- Keep the language in the URL (`?lang=pt`) with `history.replaceState`, so a shared link opens in the same language. Do not use cookies or `localStorage`: the prototype keeps data in memory only.
- Translate interface text only. Discipline names, inputs and owners are user data and stay as typed.
- Dates stay `DD/MM/YYYY` in both languages.
- Use only the built-in dictionary. Do not call any translation service: the page must not send data to servers.
- No em dash or en dash in any text, in both languages. Use a comma or a colon.

### Share
Use a fallback chain, because browser support differs:

1. If `navigator.share` exists, call it from the click handler (it requires a user gesture and a secure context) with title, text and URL. Ignore `AbortError` (user closed the share sheet). On any other error, go to step 2.
2. Else, if `navigator.clipboard.writeText` exists, copy the URL and show a "Link copied" message. On error, go to step 3.
3. Else, show the URL in a read-only field, already selected, so the user can copy it by hand.

When the page is opened from a local file (`location.protocol === "file:"`), show a short note in the menu: the link only works on this computer, publish the page (for example with GitHub Pages) to share it with other people.

Sharing sends only the link (and the language). The handovers typed on the page are not shared, because they live only in the page memory.

### Tests
- Keep every existing acceptance test passing. Tests keep comparing English labels.
- Add tests for Portuguese labels, for example `Blocked, 2 days` becomes `Bloqueado, 2 dias`.
- Add a test that `TEXT.pt` has every key of `TEXT.en`.
- Add a test that no text in either dictionary contains an em dash or an en dash.

## Feasibility notes (checked before writing this prompt)

| Item | Finding | Impact |
|---|---|---|
| Web Share API (`navigator.share`) | Needs a secure context and a user gesture. Not available in every browser (MDN marks it as limited availability; for example it is missing in desktop Chromium on Linux). | Must be feature-detected, with a fallback. |
| Clipboard API (`writeText`) | Needs a secure context. Chromium treats `file://` pages as a secure context (checked: `window.isSecureContext` is `true`). | Works in most cases; a manual copy field covers the rest. |
| Link of a local file | A `file://` link points to a file on one computer. | Real sharing needs the page to be published (GitHub Pages or similar). |
| Translation | A fixed dictionary is enough for a prototype with about 90 strings. Automatic translation services would send page text to a server. | Built-in dictionary only. |

References: [MDN, Navigator.share()](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share), [MDN, Web Share API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Share_API), [MDN, Clipboard.writeText()](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/writeText).

## Done when
- Every acceptance test passes in both languages.
- The menu works with mouse, keyboard and touch, at 1280 px and 375 px.
- Share works or falls back without errors in the console.
- No em dash or en dash in the page.
