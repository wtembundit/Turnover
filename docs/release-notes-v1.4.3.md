# Turnover v1.4.3 Release Notes

Turnover v1.4.3 is a reliability release for Timeline Index+, the native
Final Cut Pro Workflow Extension bundled in Turnover Standalone, plus
proper Developer ID signing and notarization for distribution.

## Timeline Index+

- Fixed a real misattribution bug: a multicam angle built from a
  double-system-sound sync group (its own `media` reference resolves to an
  `FFClipRef`, not a direct asset) could show an entirely unrelated clip's
  media instead of its own, because the broader same-database fallback
  search used to resolve it isn't scoped by edge name and could wander
  into a completely different clip connected nearby. Root-caused via a
  real FCPXML export used as ground truth, then fixed by resolving the
  `FFClipRef`'s own lookup key against the matching `FFAnchoredSequence` -
  which can live in a different sibling event database - before ever
  reaching that broader search. Verified against the same FCPXML ground
  truth: exact match.
- Fixed Source In/Out timecodes for clips whose source-range denominator
  isn't a literal frame rate (confirmed live: values like `2400`, `12288`,
  `720000` occur on ordinary clips) - now reads the source asset's own
  real `timecodeFrameDuration`/drop-frame convention at CSV-export time
  instead of guessing from the raw denominator.
- Fixed a bug where `containingCollection(for:)`'s own containment climb
  could not pass through a title/generator parent at all - Adjustment
  Layers and titles/generators anchored onto another title (e.g. multiple
  stacked title elements at the same point) vanished from the "All" clip
  list entirely rather than just being uncategorized.
- Titles and Generators are now reported as separate clip types (both
  previously shared one combined "Generator / Title" category) - the
  underlying Motion effect's own `effectType` reliably distinguishes them.
- Title clips can now show their actual on-screen text in the CSV export,
  read from the title's own Motion scene data.
- Added a Duration column to the CSV export, positioned after Timeline
  Out.
- The clip-type/feature/search filter controls now correctly reflect
  their last selection after the panel is closed and reopened - the
  underlying state was always retained correctly; only the controls
  themselves failed to sync on reopen.
- Analysis-cache warnings for Optical Flow, Machine Learning retime,
  Magnetic Mask, and Auto Mask are now clickable, jumping straight to
  each requesting clip - these types can only be confirmed in aggregate,
  never per clip (Final Cut Pro's own export warning for them doesn't
  name the clip either), so quick manual navigation is the practical
  alternative to an automatic check.
- The analysis cache location is now resolved the same way Final Cut Pro
  resolves it internally (via the library's own `Settings.plist`), rather
  than assuming the cache always sits next to the library - covers
  libraries whose cache lives on a relocated external volume.
- Added an "Open Turnover" button to open the full Turnover Standalone app
  directly from the extension panel, rather than duplicating its tools
  inside the extension.

## Distribution

- Signed with a Developer ID Application certificate and notarized by
  Apple - previously an ad-hoc/Apple Development signature only.
- Fixed the in-app update checker, which had been pointing at a URL that
  no longer serves public releases.

This release only updates the Standalone edition (bundling Timeline
Index+). The SpliceKit edition is unchanged from v1.4.2.
