# Turnover v1.4.1 Release Notes

Turnover v1.4.1 is a reliability release for the v1.4 Data Burn-In and Conform Prep workflows. It keeps the v1.4 feature set, tightens real-world FCPXML handling, and updates the SpliceKit plugin and standalone app to the same version.

## Conform Prep

- Improved video-only reconstruction so understood timeline audio structures are removed without deleting valid picture, titles, transitions, connected clips, or metadata.
- Improved simple `sync-clip` flattening after audio cleanup, including connected visual siblings, connected `ref-clip`/compound-style items, title anchors, and transition policy.
- Added experimental `mc-clip` flattening for observed active-video-angle multicam shapes. The current implementation resolves the selected video angle, preserves transforms, markers, metadata, speed/timeMap data, and emits video-only output that imports back into Final Cut Pro on the current tested samples.
- Added safer retimed multicam output for parent and angle-source timeMap cases by using a generic clip/video representation where native `asset-clip` semantics would make Final Cut Pro show black media, import a gap, or rehydrate audio.
- Snaps nested title offsets using the sequence frame duration to avoid preventable Final Cut Pro import warnings about edits not being on frame boundaries.

### Experimental Note

Conform Prep `mc-clip` support is still experimental. It is enabled for the observed real-world active-angle cases in this release, but it needs more production timelines before it should be treated as general multicam support. Continue testing on duplicate timelines and verify the imported result in Final Cut Pro.

## Data Burn-In

- Rechecked connected-layer handling against the automated fixture suite.
- Data Burn-In now documents and tests multiple connected video layers above the Primary Storyline, including connected clips represented as child lane items inside a primary clip. Source Layer 1, Source Layer 2, and higher layers are resolved from real positive-lane connected video clips.
- Fixed real-world connected-layer timing cases where a connected video could appear in Burn-In before its visible timeline offset when its parent clip had a `timeMap`.
- Fixed disabled connected clips leaking into Burn-In by treating `enabled="0"` as a generic visual-disabled state and propagating that state to child media.
- Fixed connected visual clips whose FCPXML video role is not named `video`, such as trailer media stored with a `titles.*` role, as long as the referenced asset is real video.
- Fixed connected `sync-clip` and selected-video `mc-clip` containers nested inside a primary clip so their child media resolves as connected source layers instead of disappearing or being classified as Primary Storyline media.
- Fixed Analysis Details ownership so normal `{analysis_*}` fields report the Primary Storyline source only, while Source Layer Details can still show retime information for each connected layer.
- Transparent ProRes 4444 alpha export now defaults its frame rate from the FCPXML timeline, with a Custom frame-rate override available when the timeline metadata is not the intended delivery rate.
- Preserves the v1.4 transparent ProRes 4444 overlay workflow and standalone customizer behavior.

## Regression Coverage

- Added Conform Prep fixtures for video-only cleanup, connected visual preservation, connected ref clips, transition policy, active-angle multicam flattening, multicam resource-definition guards, parent timeMap multicam, and source-angle timeMap multicam.
- Added Data Burn-In fixtures for one connected layer, connected sync-child layers, connected `mc-clip` layers, title-role video layers, multiple connected layers, connected layer bounds under retimed parents, disabled connected layers, selected/missing metadata, retime variants, speed ramps, transforms, sync-clip retime, and audio-role intervals.

## Release Scope Notes

- Data Burn-In selected-angle `mc-clip` source resolution remains experimental; flatten multicam before Burn-In when source/timecode accuracy is critical.
- Conform Prep `mc-clip` flattening is experimental but improved enough for broader duplicate-timeline testing.
- Report new failures with the smallest reproducible FCPXML/FCPXMLD sample and the Final Cut Pro imported/re-exported result when possible.
