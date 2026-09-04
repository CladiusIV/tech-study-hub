# Changelog

All notable changes to this project are documented here.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project uses [Semantic Versioning](https://semver.org/) pre-release tags.

## [0.1.2-alpha] - 2026-09-04

### Added
- A project-scoped Claude Code skill (`update-changelog`) that drafts changelog entries and version bumps from the repo's uncommitted/unpushed changes, following this project's Keep a Changelog and semver-alpha conventions, and always asks for confirmation before writing anything.

### Changed
- Removed the `<iframe>` embed from the three interview-question pages (React, Angular, Python). It carried real ongoing cost — a fixed 500px scroll box, `core-dark.css` fighting the embedded page's own dark mode, and every GitHub Pages path bug fixed in 0.1.1-alpha was a direct consequence of the iframe boundary. Replaced with two buttons: "Open [X] Interview Bank" (opens the standalone page in a new tab) and "Download HTML" (same-origin download, triggered via a dynamically-created `<a download>` so it isn't rewritten by docsify's markdown-link compiler the way a plain `<a>` would be).

## [0.1.1-alpha] - 2026-09-03

### Fixed
- **Sidebar navigation breaking after the first click, GitHub Pages only.**
  `_sidebar.md` links are written relative to the site root (e.g.
  `frontend/react/README.md`). The config had `relativePath: true`, which
  makes docsify resolve those same links relative to whatever page you're
  *currently* on instead of the site root. That's why it only "worked for
  the landing page": from the root, current-directory and site-root happen
  to coincide, but the moment you're one level deep and click a sibling
  topic (e.g. viewing a Frontend page, then clicking a Backend link),
  docsify tries to load `frontend/react/backend/...`, which doesn't exist.
  Fix: leave `relativePath` off (docsify's default). Considered instead
  prefixing every sidebar link with a leading `/` (which docsify resolves
  from the site root even with `relativePath: true`, verified working
  under a simulated GitHub Pages subpath) — rejected because it adds an
  ongoing authoring tax (every future link needs the leading slash or the
  bug silently comes back for just that link) for no benefit, since we
  don't rely on relocatable doc subtrees the way `relativePath` is meant
  to support.
- **Embedded interview-question `iframe`s 404 on GitHub Pages.** The three
  `qa.md` pages (React, Angular, Python) embedded their standalone HTML
  banks with a root-absolute path (`/frontend/react/interview-questions/
  346-....html`). That's plain HTML, not something docsify rewrites, so
  the browser resolves it from the literal domain root. Harmless on a
  local dev server (root == domain root there) but broken on a GitHub
  Pages *project* site, which is served under `/<repo-name>/`, not the
  domain root — the leading slash was stripping that prefix. Fix: dropped
  the leading slash so the path resolves relative to wherever `index.html`
  itself is hosted, same pattern the "Download HTML" button already used
  correctly.
- **Noisy, misleading `_sidebar.md` 404s in devtools.** Separate from the
  bug above: docsify's `loadSidebar` probes for a directory-specific
  sidebar override at every level above the current page (e.g.
  `frontend/react/interview-questions/_sidebar.md`, then
  `frontend/react/_sidebar.md`, then `frontend/_sidebar.md`) before
  falling back to the root one. Since we only maintain one `_sidebar.md`,
  every intermediate lookup 404s by design — harmless, but easy to mistake
  for a real bug (and was masking the two real ones above). Added an
  `alias` redirecting every such lookup straight to the root file, so
  there's exactly one `_sidebar.md` request, always 200.
- The sidebar version footer now reads its number from `package.json` at
  runtime instead of a hardcoded string in `index.html`, so bumping the
  version never means touching more than one file again.

## [0.1.0-alpha] - 2026-09-03

### Added
- Docsify-powered site skeleton with sidebar navigation.
- Frontend section: React and Angular (overview, core concepts, interview questions, code snippets).
- Backend section: .NET, Java, Node.js, and Python (overview, core concepts, interview questions, code snippets).
- Mobile section: Flutter, React Native, Android (Kotlin/KMP), and iOS (Swift) (overview, core concepts, interview questions, code snippets).
- Standalone interview-question banks (React, Angular, Python) embedded via iframe with a working "Open in Full Window" and "Download HTML" action.
- Versioning system (this changelog, `package.json` version field, sidebar version footer).

### Fixed
- Added `.nojekyll` so GitHub Pages' Jekyll pipeline doesn't strip
  underscore-prefixed files (`_sidebar.md`) before serving — without this
  the sidebar never loaded at all once deployed.

### Notes
- Most section content is still placeholder ("Under construction").
