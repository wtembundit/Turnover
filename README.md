# Turnover

Final Cut Pro turnover workflows, available three ways: a standalone macOS
app, a native Final Cut Pro Workflow Extension, and a SpliceKit plugin. Pick
whichever fits how you work, or use more than one together.

## Download

Grab the latest build from the [Releases page](https://github.com/wtembundit/Turnover-Releases/releases/latest).

- `Turnover-Standalone-v*.zip` - the standalone app. Also bundles **Timeline
  Index+**, a Final Cut Pro Workflow Extension panel, automatically.
- `Turnover-SpliceKit-v*.zip` - the SpliceKit plugin edition, for running
  everything from inside Final Cut Pro's own `Turnover` menu. Requires
  [SpliceKit](https://github.com/elliotttate/SpliceKit).

Turnover is signed with a Developer ID certificate and notarized by Apple -
macOS opens it normally, no Gatekeeper warning.

## Turnover Standalone

1. Extract the downloaded archive.
2. Move `Turnover.app` to your Applications folder.
3. Open it.

Drag a `.fcpxml` file, an `.fcpxmld` bundle, or a project straight from
Final Cut Pro into the window to get started.

### Tools

- **Conform Prep** - flatten supported sync clips and prepare timelines for
  online conform.
- **Data Burn-In** - build customizable burn-ins, preview against a
  reference movie, and export transparent ProRes 4444 overlays or
  burned-in H.264/HEVC review movies.
- **VFX Auto Naming / Reset Naming** - number or reset `VFX NAMING` titles.
- **VFX Auto Marker** - create markers from VFX titles.
- **VFX Shot List** - extract marker-anchored thumbnails from a reference
  movie and build an Excel shot list.
- **VFX Pull EDL** - build a source pull EDL with per-side handle frames.
- **VFX Timeline** - place returned VFX renders back into the timeline.
- **Marker Export** - export markers as EDL, CSV, or TXT.

## Timeline Index+

Bundled inside Turnover Standalone - installing the app registers it with
Final Cut Pro automatically. Open it from Final Cut Pro's own
**Window > Extensions > Timeline Index+**.

Unlike the tools above, Timeline Index+ doesn't need an FCPXML export - it
reads your currently open project directly from Final Cut Pro's own library
while you edit:

- See every clip on the active timeline in one list - clips, sync clips,
  multicam angles, compound clips, storylines, gaps, titles, and
  generators.
- Filter by feature (Retime, Optical Flow, Stabilization, and more), clip
  type, or Enabled/Disabled state.
- Follow Final Cut's playhead automatically, or click a row to move the
  playhead there.
- See exactly what changed on a clip, in plain language.
- Export a CSV index with camera/reel/scene/take metadata and real
  Timeline/Source In-Out timecodes.
- Check whether analysis data (Optical Flow, Stabilization, etc.) is
  actually cached before an export that needs it.
- Jump straight to Turnover Standalone for Data Burn-In and the rest of the
  tools, without leaving Final Cut Pro.

## Turnover for SpliceKit

1. Install [SpliceKit](https://github.com/elliotttate/SpliceKit/releases/latest)
   and confirm its menu appears in Final Cut Pro.
2. Extract the downloaded Turnover archive.
3. Double-click `Install Turnover.command` and follow the prompts.
4. Restart Final Cut Pro and open the `Turnover` menu.

This edition includes the same tool set as Turnover Standalone, run from
inside Final Cut Pro, plus **VFX Shot List** can drive and capture the
Final Cut Pro viewer directly instead of needing a separately exported
reference movie.

## Requirements

- Apple Silicon Mac, macOS 14 or later.
- Final Cut Pro, for importing generated FCPXML results.
- Node.js and required spreadsheet packages are bundled - no separate
  runtime install needed.
- SpliceKit edition only: [SpliceKit](https://github.com/elliotttate/SpliceKit)
  installed and working with Final Cut Pro.

## Known Limitations

- FCPXML can represent visually identical timelines through different
  nesting and timing structures - keep your original timeline and verify
  generated results before turnover.
- Conform Prep works best on a duplicate timeline with audio detached.
- Complex or previously unseen nested retime structures may still need a
  reduced reproducible case reported.
- Timeline Index+ requires an active Final Cut Pro sequence - there's no
  offline mode, since it reads the live library.

## Updates

Turnover checks this page for new releases automatically when it opens.
Use **Check for Updates** in the app's header to check manually at any
time.
