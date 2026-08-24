# Timeline Index+ Guide

Timeline Index+ is a native Final Cut Pro Workflow Extension, bundled inside
Turnover Standalone, that reads a live project directly from Final Cut Pro's
own library. It shows every clip actually placed on the current timeline,
follows the playhead, and exports a detailed CSV index - without an FCPXML
export step.

## What It Does

Timeline Index+ opens as a panel inside Final Cut Pro (Window > Extensions >
Timeline Index+) and stays in sync with whichever project is open.

Common uses:

- See every clip on the active timeline in one list - ordinary clips, sync
  clips, multicam clips/angles, compound clips, storylines, gaps, titles, and
  generators - not just clips with a tracked effect.
- Filter by feature (Retime, Optical Flow, Machine Learning, Stabilization,
  Magnetic Mask, Auto Mask, Tracker, Transform, Spatial Conform, Rate
  Conform, Crop, Distort, Composition), by clip type, or by Enabled/Disabled
  state.
- Follow the Final Cut Pro playhead automatically, or click a row to move the
  playhead there.
- See exactly what changed on a clip - Blend Mode, Transform, Retime speed,
  Crop, Distort, and more - with real values, not raw internal keys.
- Export a CSV index of the current filtered list, including camera/reel/
  scene/take/custom metadata, real Timeline and Source In/Out timecodes, and
  Title text.
- Check whether Optical Flow, Machine Learning retime, Stabilization,
  Magnetic Mask, or Auto Mask analysis is actually cached before an export
  that needs it.
- Open the full Turnover app for Data Burn-In and the other Turnover tools,
  without leaving Final Cut Pro.

## Why It Reads The Library Directly

Most Final Cut Pro automation works from an exported FCPXML snapshot of a
project. Timeline Index+ instead reads the library live, through Apple's
read-only Workflow Extension API - the same data Final Cut Pro itself is
showing, with no export step and no risk of the snapshot going stale while
you keep editing.

This gives it a few real advantages an FCPXML-based tool does not have:

- Multicam angle resolution matches the exact camera asset in use, including
  angles built from a double-system-sound sync group nested several levels
  deep.
- Source In/Out timecodes use the source media's own real frame rate rather
  than a guess based on the clip's own internal time-value denominator,
  which is frequently not a literal frame rate at all.
- Clip identity, media resolution, and analysis-cache checks are all
  confirmed directly against Final Cut Pro's own library structure, not
  inferred from an XML export's own approximations.

The tradeoff is that this only ever works while Final Cut Pro has a project
open - there is no standalone/offline mode the way Data Burn-In has one,
since the whole point is reading the live library.

## Basic Workflow

1. Open a project in Final Cut Pro with the timeline you want to inspect.
2. Choose **Window > Extensions > Timeline Index+**.
3. The list populates with every clip on the current timeline. Use the
   search field, the feature filter, the type filter, or the enabled/
   disabled filter to narrow it down.
4. Click a clip to select it (this also moves Final Cut's playhead there);
   moving Final Cut's own playhead selects the matching row automatically.
5. Select a clip to see its non-default values (effects, transform,
   retime, crop, etc.) in the detail panel below the list.
6. Click **Export CSV** to save the current filtered list, including
   metadata resolved from every event in the library.
7. Watch the analysis-cache indicator (top right) for a warning before an
   export that depends on cached analysis data.
8. Click **Open Turnover** to launch the full Turnover app for Data
   Burn-In and the other Turnover tools.

## Filters

- **Search**: matches clip names.
- **Feature filter**: shows only clips carrying a specific tracked change
  (Retime, Optical Flow, Machine Learning, Stabilization, Magnetic Mask,
  Auto Mask, Tracker, Transform, Spatial Conform, Rate Conform, Crop,
  Distort, Composition).
- **Type filter**: All Types, Clip, Audio Only, Video Only, Sync Clip,
  Multicam Clip, Compound Clip, Title, Generator. Title and Generator are
  reported separately, even though they share the same underlying clip type
  in Final Cut Pro's own data.
- **Enabled/Disabled filter**: cycles between All, Enabled Only, and
  Disabled Only, reading Final Cut's own Enable/Disable clip state.

## Analysis Cache Check

Click the cache-status icon (a warning triangle or a checkmark, next to the
refresh button) to see a breakdown:

- **Stabilization** is checked per clip and is reliable - Timeline Index+
  can confirm whether a specific clip's Stabilization analysis is actually
  cached, and link straight to that clip if it's missing.
- **Optical Flow, Machine Learning retime, Magnetic Mask, and Auto Mask**
  can only be reported in aggregate (how much cached data exists in the
  library overall) - there is no reliable way to confirm a *specific* clip
  is covered, the same limitation Final Cut Pro's own export-time warning
  has. Every clip requesting one of these is still listed and clickable, so
  you can quickly jump through and check/re-analyze each one by hand.
  Re-running Analysis in Final Cut Pro before export remains the only sure
  way to check these.

The cache location itself is resolved the same way Final Cut Pro resolves
it internally - including libraries whose cache lives on an external/
relocated volume rather than next to the library.

## CSV Export

Exports the clips currently visible under the active search/filter, in
timeline order. Columns:

`Timeline In, Timeline Out, Duration, Source In, Source Out, Clip, Type,
Original Filename, Camera, Angle, Reel, Scene, Take, Notes, Custom
Metadata, Enabled, Features, Details, Title Text`

- **Original Filename/Camera/Reel/Scene/Take/Custom Metadata** are resolved
  from every event in the library at export time - for a multicam/sync
  clip, this reflects the actual media in use, not the wrapper's own
  bookkeeping.
- **Title Text** is the actual on-screen text of a Title clip - blank for
  anything that isn't a Title.
- **Details** summarizes what changed for each tracked feature (e.g. a
  Blend Mode change, a Retime speed/direction, a Crop value) in plain,
  human-readable form.

## Known Limits

- Requires Final Cut Pro to have an open project - there is no
  offline/standalone mode.
- Multicam angle camera selection, when a clip's real media sits behind a
  double-system-sound sync group with no other disambiguating signal, uses
  a best-effort default (the alphabetically-first camera in that sync
  group) rather than a fully general per-placement selector.
- Storyline position/duration in the "All" list is best-effort and not yet
  cross-validated against Final Cut Pro's own Timeline Index panel the way
  most other position values are.
- Optical Flow, Machine Learning retime, Magnetic Mask, and Auto Mask
  analysis-cache checks are aggregate-only (see "Analysis Cache Check"
  above) - this is a confirmed limit of what Final Cut Pro's own cache
  files expose, not a gap in Timeline Index+ itself.
