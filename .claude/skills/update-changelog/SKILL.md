---
name: update-changelog
description: Draft a CHANGELOG.md entry and package.json version bump for this repo's uncommitted or unpushed changes, following Keep a Changelog format and this project's semver pre-alpha versioning conventions. Always propose the entry and bump size for confirmation before writing anything, and never run git commit/push/tag. Use this skill whenever the user asks to update the changelog, log recent changes, bump the version, prepare a release, or asks what's changed in the context of documenting it — even if they don't say "changelog" explicitly.
---

# Update changelog

Draft a changelog entry and version bump for whatever's changed in this repo since the last documented release, then stop and let the user approve it before touching any files. The value here is the judgment call — deciding how big a version bump the changes deserve, and writing bullets that explain *why* a change happened, not just what changed. Don't shortcut that reasoning.

## Workflow

1. **Gather the facts, read-only.** Use `git status` and `git diff` for uncommitted changes, and compare local history against the remote tracking branch (e.g. `git log origin/<branch>..<branch>`) for commits that exist locally but haven't been pushed. Also check `package.json`'s current `"version"` and the most recent entry in `CHANGELOG.md` — sometimes the version has already been bumped by hand without a matching changelog entry (this has happened before in this repo), so don't assume the two are in sync.
   - If nothing has changed and the version already matches the changelog, say so plainly and stop — there's nothing to draft.
   - Only read-only git commands. Never run `git add`, `git commit`, `git push`, or `git tag` — that's the user's call, always, no exceptions.

2. **Summarize the changes in plain language.** If several unrelated things changed, group them by theme rather than listing files. Skim the actual diffs, not just filenames — a changed filename doesn't tell you whether it was a fix, a new feature, or a rewrite.

3. **Decide the version bump, and justify it out loud.** This project uses semver pre-release tags in the form `X.Y.Z-alpha`, with `package.json`'s `"version"` field as the single source of truth (`index.html` reads it at runtime via `fetch('package.json')` rather than hardcoding it, so a bump is always just editing `package.json` + `CHANGELOG.md` — nothing else needs to change).

   | Bump | When | Example |
   |---|---|---|
   | **Patch** (`0.1.1` → `0.1.2`) | Bug fixes, restructuring or simplifying something that already existed, config/dependency tweaks — nothing new that a user would notice as a *feature*. | Removing a fragile iframe embed and replacing it with two buttons: same capability, better implementation. |
   | **Minor** (`0.1.x` → `0.2.0`) | A new section, track, page type, or capability got added — even rough/"alpha" ones. | Adding a new topic section (a new language, a new track) to the sidebar. |
   | **Major** (`0.x.y-alpha` → `1.0.0`, the `-alpha` suffix drops entirely) | The guide stops being "alpha": the placeholder/"under construction" stubs across sections have been substantially filled in, and the site is genuinely usable as a finished reference rather than scaffolding. | Every section listed in `_sidebar.md` has real content instead of a stub. |

   Don't default to patch out of caution — if new user-facing surface area was added, say so and bump minor. State the reasoning in your proposal (one sentence is usually enough), not just the resulting number, so the user can push back if they read it differently.

   The major bump is different in kind from the other two: it's a deliberate milestone call about the project's overall state, not something derivable from a single diff the way patch-vs-minor is. Don't propose it just because a change looks big — if the *current* changes plausibly complete that milestone (e.g. they fill in the last remaining stub section), say so explicitly and ask the user to confirm before treating it as anything other than a patch/minor candidate.

4. **Draft the full entry**, ready to paste in as-is, in the format this project already uses:

   ```markdown
   ## [X.Y.Z-alpha] - YYYY-MM-DD

   ### Added
   - ...

   ### Changed
   - ...

   ### Fixed
   - ...
   ```

   Omit any category with nothing in it. Use today's date.

   For bullets, match the density that's already in `CHANGELOG.md`: a straightforward addition gets one tight line, but a fix or a non-obvious design decision gets a short paragraph explaining the *why* — root cause for bugs, or why an alternative was considered and rejected. Look at the existing entries in `CHANGELOG.md` before writing to match the voice; don't invent a new style.

5. **Present the proposal and stop.** Show the user:
   - The plain-language summary of what changed.
   - The suggested version number *with* your one-line reasoning for the bump size.
   - The exact markdown block to insert at the top of `CHANGELOG.md` (above the current most-recent entry).
   - The exact `package.json` version string change.

   Ask explicitly whether to proceed, adjust the bump size, or reword anything. Do not edit `CHANGELOG.md` or `package.json` until the user says yes.

6. **After approval**, make just those two edits. Then optionally suggest, but never run:
   - A commit message in this repo's existing style — `VERB description` (e.g. `FIX add .nojekyll to load underscore files`, `RELEASE v0.1.1-alpha`).
   - Whether a git tag applies, and if so, a message for it. Not every bump gets tagged — this repo only tags meaningful release checkpoints (`v0.1.0-alpha` marks the commit that made the site actually deployable; `v0.1.1-alpha` marks the commit that fixed the GitHub Pages path bugs), not every small patch. Weigh whether this bump reads as one of those checkpoints or as a minor increment better left untagged for now, and say which you think it is and why. If you do suggest one, format it `vX.Y.Z-alpha` (or `vX.Y.Z` once past 1.0.0) to match the existing tags, with an annotated message in the same style already in use, e.g. `"v0.1.1-alpha: fix GitHub Pages sidebar/iframe path bugs, package.json-driven version footer"`.

   Committing, tagging, and pushing are always left to the user — these are suggestions to hand them, not actions to take.
