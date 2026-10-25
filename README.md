# Turnover

Final Cut Pro turnover workflows, available two ways: a standalone macOS app
and a native Final Cut Pro Workflow Extension bundled inside it. Use either on
its own, or both together.

## Download

Grab the latest build from the [Releases page](https://github.com/wtembundit/Turnover/releases/latest).

- `Turnover-Standalone-v*.zip` - the standalone app. Also bundles **Timeline
  Index+**, a Final Cut Pro Workflow Extension panel, automatically.
- `SHA256SUMS-v*.txt` - checksums for verifying a download.

Turnover is signed with a Developer ID certificate and notarized by Apple -
macOS opens it normally, no Gatekeeper warning. If you still see a warning
the first time, Control-click the app, choose **Open**, then confirm once.

## Requirements

- Apple Silicon Mac, macOS 14 or later.
- Final Cut Pro, for importing generated FCPXML results.
- Node.js and required spreadsheet packages are bundled with the app - no
  separate runtime install needed.

---

## Turnover Standalone

### Install

1. Extract the downloaded archive.
2. Move `Turnover.app` to your **Applications** folder.
3. Open it **from the Applications folder**. This also registers Timeline
   Index+ with Final Cut Pro, which only lists it once Turnover has been opened
   from there.

From v1.5.0 on, Turnover updates itself: it checks for new versions in the
background and installs them from **Turnover > Check for Updates...**.

### Basic Use

Turnover Standalone works from an **FCPXML export**, not a live connection
to Final Cut Pro:

1. In Final Cut Pro, export the project you want to work on as FCPXML
   (File > Export XML), or export a `.fcpxmld` bundle.
2. Drag the `.fcpxml`/`.fcpxmld` into the Turnover window, or use **Choose
   File**. You can also drag a project directly from Final Cut Pro's own
   browser into the window - Turnover reads it the same way.
3. Pick a tool from the sidebar and run it.
4. Turnover writes its own output XML/files alongside your input - your
   original export is never modified.
5. Import the result back into Final Cut Pro where the tool's own
   instructions say to (some tools produce a file to import; others export
   a movie or spreadsheet directly).

### Tools

- **Conform Prep** - flattens sync clips, the common kinds of multicam clip,
  and retimed/speed-ramped structures into a video-only version safe to hand
  to an online/finishing artist working in a different NLE. It removes audio
  structures it understands automatically and keeps retimes, titles, markers
  and effects. Multicam inside compound clips, nested multicam and similar
  shapes are skipped and listed in the report. Work on a duplicate timeline,
  since the goal is a flattened *copy*, not an edit of your original.
- **Data Burn-In** - builds a customizable on-screen data overlay (source
  filename, timecode, camera/reel/scene/take metadata, retime/transform
  values, and more) from your FCPXML, previewed live against a reference
  movie you supply. Exports either a transparent ProRes 4444 overlay (to
  composite yourself) or a burned-in H.264/HEVC/ProRes 422 review movie with
  audio, at the size you choose. See **Data Burn-In** below for more on how
  it decides what to show.
- **VFX Auto Naming / Reset Naming** - numbers `VFX NAMING` title clips in
  sequence, or resets them back to placeholders for re-numbering later.
- **VFX Auto Marker** - creates Standard, To Do, or Chapter markers from
  VFX naming titles already on the timeline.
- **VFX Shot List** - extracts a thumbnail for each VFX shot (anchored by
  marker) from a reference movie you export, and builds an Excel workbook
  listing every shot.
- **VFX Pull EDL** - builds a source pull EDL/TSV with handle frames added
  to both sides of each range - enter `8` to add 8 frames head and tail.
- **VFX Timeline** - places returned VFX renders back into the timeline at
  the correct position and tags them with a `VFX Deliveries` keyword.
- **Marker Export** - exports Final Cut Pro markers as EDL, CSV, or TXT.

### Data Burn-In, In More Detail

Data Burn-In reads your FCPXML, builds a frame-by-frame index of what's
visible on the timeline at every moment (which clip, its metadata, any
retime/transform on it), and lets you place any of that data anywhere on
screen as text.

- Choose a preset, or build a custom layout from independent **elements**:
  add as many as you like, drag them anywhere in the preview, type plain
  text or insert data from the **Insert Data** menu (source filename,
  timecode, camera, reel/scene/take, custom metadata, retime/transform
  values, captions, and more). Every change can be undone.
- An element can repeat its own text for each connected layer (stacked above
  or below the source, labelled V1, V2... like the timeline's video tracks),
  and can show caption text or VFX shots from the roles you choose, so
  captions can be burned in as subtitles.
- Preview plays back against a reference movie you choose, with standard
  J/K/L shuttle controls, so what you see matches what exports.
- Export a **transparent overlay** (ProRes 4444 with alpha, no reference
  movie required) to composite yourself, or a **burned-in reference**
  (H.264, HEVC or ProRes 422, with audio from your reference movie) as a
  self-contained review file, scaled up or down while keeping the aspect
  ratio.
- Save layouts as presets, locally or as a shareable JSON file.

Multicam support is still experimental - if your timeline has multicam
clips, flattening them first (in Conform Prep, or with a third-party
multicam flattener) before running Data Burn-In gives more reliable
results.

---

## Timeline Index+

Bundled inside Turnover Standalone - no separate install. Move Turnover to
the Applications folder and open it from there once; then open the panel
from Final Cut Pro's own **Window > Extensions > Timeline Index+**. Turnover
warns in its sidebar if the extension is not registered yet.

This is a different kind of tool from everything else in Turnover:
instead of working from an FCPXML export, it reads your **currently open
project directly from Final Cut Pro's own library, live** - the same data
Final Cut Pro itself is showing you, updated as you edit, with no export
step and nothing that can go stale.

### What it shows

- Every clip actually placed on the active timeline in one list - ordinary
  clips, sync clips, multicam clips/angles, compound clips, storylines,
  gaps, transitions, titles, and generators - not just clips with an effect
  applied.
- Filter by feature (Retime, Optical Flow, Machine Learning, Stabilization,
  Magnetic Mask, Auto Mask, Tracker, Transform, Spatial Conform, Rate
  Conform, Crop, Distort, Composition), by clip type, or by Enabled/
  Disabled state. Search by name.
- What actually changed on a clip - Blend Mode, Transform, Retime speed,
  Crop, and more - described in plain language, not raw values.

### Playhead sync

Select a clip in the list and Final Cut's playhead jumps to it. Move Final
Cut's own playhead and the matching row selects itself automatically -
the list always shows where you are.

### CSV Export

Exports whatever's currently visible under your search/filter, in
timeline order, with real Timeline and Source In/Out timecodes, camera/
reel/scene/take/custom metadata (resolved from your whole library, not
just the current project), on-screen Title text, and a plain-language
summary of every tracked change - ready to hand off as a shot log or
turnover reference.

### Analysis Cache Check

Before an export that depends on cached analysis (Optical Flow, Machine
Learning retime, Stabilization, Magnetic Mask, Auto Mask), click the
status icon next to the refresh button for a breakdown of what's actually
cached in your library right now - including libraries whose cache lives
on a relocated external volume rather than next to the library itself.

**Stabilization** can be checked per clip reliably, and a missing one
links straight to that row. **Optical Flow, Machine Learning retime,
Magnetic Mask, and Auto Mask** can only be confirmed in aggregate (Final
Cut Pro's own export warning for these doesn't name the clip either) -
every clip requesting one of these is still listed and clickable, so you
can quickly jump through and check/re-analyze each one by hand.

### Open Turnover

A button in the header opens the full Turnover Standalone app directly,
for Data Burn-In and the rest of the tools, without leaving Final Cut Pro.

### Limits

- Requires an open Final Cut Pro project - there's no offline mode,
  since the whole point is reading the live library.
- Multicam angle camera selection, in the rare case a clip's real media
  sits behind a synced double-system-sound recording with no other
  disambiguating signal, uses a reasonable best-effort default rather than
  a fully general per-placement selector.

---

## SpliceKit plugin

The SpliceKit plugin edition has been retired. Every tool it offered is in
Turnover Standalone.

---

## Known Limitations

- FCPXML can represent visually identical timelines through different
  nesting and timing structures - keep your original timeline and verify
  generated results before turnover.
- Conform Prep works best on a duplicate timeline with audio detached;
  titles and markers should be cleared when validating flattening
  behavior specifically. Multicam flattening covers the common shapes only.
- Complex or previously unseen nested retime structures may still need a
  reduced reproducible case reported.
- Data Burn-In's multicam support is experimental - flatten multicam
  clips first for reliable results.
- Timeline Index+ requires an open Final Cut Pro project.

## Documentation

- [Turnover Tools Guide](docs/turnover-tools.md): detailed workflow
  descriptions and usage notes for every tool - Conform Prep, Data Burn-In,
  VFX Auto Naming/Reset Naming, VFX Auto Marker, VFX Shot List, VFX Pull
  EDL, and VFX Timeline.
- [Timeline Index+ Guide](docs/timeline-index-extension.md): the native
  Final Cut Pro Workflow Extension - live clip index, filters (including roles), playhead
  following, CSV export, and analysis-cache checking.
- [Keyboard Shortcuts](docs/keyboard-shortcuts.md): every shortcut in the main
  window, the Burn-In Customizer and Timeline Index+.
- Release notes: [v1.5.2](docs/release-notes-v1.5.2.md) ·
  [v1.5.1](docs/release-notes-v1.5.1.md) ·
  [v1.5.0](docs/release-notes-v1.5.0.md) ·
  [v1.4.3](docs/release-notes-v1.4.3.md) ·
  [v1.4.2](docs/release-notes-v1.4.2.md) ·
  [v1.4.1](docs/release-notes-v1.4.1.md) ·
  [v1.4.0](docs/release-notes-v1.4.0.md) ·
  [v1.3.2](docs/release-notes-v1.3.2.md) ·
  [v1.3.1](docs/release-notes-v1.3.1.md) ·
  [v1.3.0](docs/release-notes-v1.3.0.md) ·
  [v1.2.2](docs/release-notes-v1.2.2.md) ·
  [v1.2.1](docs/release-notes-v1.2.1.md) ·
  [v1.2.0](docs/release-notes-v1.2.0.md) ·
  [v1.1.0](docs/release-notes-v1.1.0.md)

This repository ships builds and user-facing documentation only. Turnover's
implementation lives in a separate, private source repository.

## Updates

From v1.5.0, Turnover checks for new versions in the background (about once a
day) and installs them after you confirm. Use **Check for Updates...** in the
app menu or the sidebar to check manually. Earlier versions do not update
themselves: install v1.5.0 by hand once.

Download page: https://wtembundit.github.io/Turnover/
