# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Alexander Dentler's personal academic website, served at https://adentler1.github.io/ via GitHub Pages. Plain HTML + CSS, no build step, no package manager, no dependencies. Deployed automatically from the `main` branch (GitHub Pages rebuilds within about a minute of a push).

## Commands

There is no build, lint, or test tooling — this is static HTML/CSS served as-is. To preview locally, just open the HTML files in a browser, or serve the directory (e.g. `python3 -m http.server`) so relative asset paths resolve. Check changes by opening the page(s) you touched in a browser; there are no automated checks to run.

## Structure

- `index.html` — homepage (hero, featured work, recent lectures, selected research, projects, contact)
- `research.html` — publications and working papers, rendered client-side from a JS data array (see below)
- `projects.html` — applied projects (Monitoring Mexico + subprojects @LiquidMexico and Monitoring Minutes, Soccer Contributions)
- `teaching.html` — teaching approach, courses, pedagogy
- `cv.html` — CV with PDF download options
- `css/style.css` — single shared stylesheet for all pages
- `assets/` — PDFs, images, project logos, organized in subfolders per project/talk series (`DVP/`, `LiquidMexico/`, `Talks/`, `LectureNotes/`, `ProjectAssets/`)

Each page is a fully self-contained `.html` file with its own `<head>` (title, meta description, Open Graph/Twitter tags, favicon link, `css/style.css`, Google Analytics snippet). There is no templating or includes system — shared markup (header/nav, footer) is duplicated across every page and must be edited in each file individually when it changes.

## Research page architecture

`research.html` is the one page with real logic. Publications live in a `papers` array embedded directly in an inline `<script>` block at the bottom of the file (not a separate data file). Each paper object has: `id` (used as the heading anchor, e.g. `research.html#crime-paper`), `title`, `authors`, `venue`, `year`, `status` (`accepted` / `published` / `policy` / `submitted` / `wip`), `topics` (array, keyed against `topicMeta`), `abstract`, `bullets`, `links`, and an optional `wp` object describing an earlier working-paper version (title, ref, delta, bullets).

The page renders client-side with vanilla JS (no framework): toggle buttons switch `currentSort` (chronological vs. by-topic) and `currentDisplay` (abstract vs. key-point bullets vs. titles-only), and `render()` rebuilds `#research-output` from the `papers` array via a small DOM-builder helper (`el()`). To add a paper, add an entry to the `papers` array — the `id` becomes the anchor other pages link to (e.g. `index.html` "Selected Research" links to `research.html#do-private-banks`), so keep ids stable once referenced elsewhere. `statusOrder` controls section ordering under chronological sort; `topicMeta[...].order` controls section ordering under by-topic sort.

## Styling conventions

- All color values are CSS custom properties on `:root` (`--background`, `--text`, `--accent`, `--muted`, `--border`, etc.), overridden inside `@media (prefers-color-scheme: dark)`. There is no manual light/dark toggle — it follows the OS/browser preference. Any new color must be added as a variable with a dark-mode override, not hardcoded.
- Recurring layout patterns are class-based and shared across pages: `.section` / `.section-header` for page sections, `.entry` / `.card` for list items (each gets a small top-left accent bar via `::before`, suppressed on `:first-child`), `.link-row` for groups of outbound links, `.stack` for vertical item lists, `.tag` (+ `.tag--published` / `.tag--policy` / `.tag--submitted` / `.tag--wip`) for status pills, `.project-entry` / `.subproject` for the nested project structure on `projects.html`, `.carousel` / `.carousel-track` for horizontally scrolling image strips, `.wp-note` for the working-paper delta callout on the research page.
- External links (anything leaving the site) use `target="_blank" rel="noopener noreferrer"`.
- Content width is capped via `--max-width` (720px, prose) and `--nav-width` (1040px, header), both centered.

## Content conventions

- Every page's `<head>` carries matching `<title>`, meta description, and Open Graph/Twitter tags — keep these in sync with the page's actual content when editing.
- Nav links use `aria-current="page"` on the active page's link; update this when copying the header block to a new page.
- New/notable items get a `<span class="tag">New</span>` appended to their `<h3 class="entry-title">`.
- Google Analytics (gtag.js, measurement ID `G-CP04B68RK2`) is loaded identically on every page.
