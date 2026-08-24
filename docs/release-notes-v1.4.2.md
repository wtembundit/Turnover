# Turnover v1.4.2 Release Notes

Turnover v1.4.2 is a focused reliability release after v1.4.1. It keeps the
same workflow surface, reduces the standalone app bundle size, and hardens the
Conform Prep video-only path after Final Cut Pro import/export testing.

## Conform Prep

- Fixed sync-clip conform prep cases where Final Cut Pro could rehydrate audio
  after import/export even though Turnover's direct output was audio-free.
- Replaced the final cloned `_video` asset strategy with source-coordinate
  `clip` + child `video` wrappers for remaining source-backed A/V clips that
  need stronger video-only behavior.
- Fixed mixed `sync-clip` + `mc-clip` timelines where a retimed multicam angle
  with a leading gap could import as missing media. Parent multicam timeMaps are
  now remapped into selected source media coordinates when needed.
- Added a final nested-title frame-boundary cleanup after structural rewrites.
  Remaining Final Cut Pro title warnings should be treated as low-risk when the
  title count, owner clip, and placement match after import/export.
- Documented the v1.4.0 -> v1.4.1 -> v1.4.2 video-only lessons directly in the
  Conform Prep guide so future sync, multicam, title, and connected-layer fixes
  do not regress each other.

`mc-clip` flattening remains experimental. It is now safer on the tested
active-angle cases, but new production shapes should still be verified on
duplicate timelines after Final Cut Pro import.

## Data Burn-In

- Fixed connected source layers nested inside primary clips so multiple visible
  connected video layers can appear in the burn-in at the same frame.
- Fixed nested connected layer timing so layers do not appear before their real
  timeline range.
- Disabled connected clips are now suppressed generically through child media
  propagation.
- `{analysis_*}` fields now use primary-owner analysis, while connected layer
  detail fields keep using each visible connected layer's own analysis.

## VFX Shot List

- Fixed multi-sequence FCPXML handling where marker thumbnail anchors could be
  calculated against the wrong sequence start time.
- Added an Excel visual contract regression so standalone runtime changes do not
  accidentally change the shot list workbook layout.

## Standalone App

- Added a Bun-compiled `turnover-engine` dispatcher for the standalone app while
  keeping the existing `.mjs` scripts as the source of truth.
- The app still falls back to Node when needed, but the bundled app no longer
  needs the large Node runtime and script `node_modules` folders in the normal
  build.
