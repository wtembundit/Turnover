# Timeline Index+ User Manual

Timeline Index+ is a native Final Cut Pro Workflow Extension that reads a live
project directly from Final Cut Pro's own library database. It shows every
clip actually placed on the current timeline, follows the playhead, and
exports a detailed CSV index - without ever touching Accessibility APIs or
requiring an FCPXML export first.

## What It Does

Timeline Index+ opens as a panel inside Final Cut Pro (Window > Extensions >
Timeline Index+) and stays in sync with whichever project is open.

Common uses:

- See every clip on the active timeline in one list - ordinary clips, sync
  clips, multicam clips/angles, compound clips, storylines, gaps, transitions,
  titles, and generators - not just clips with a tracked effect.
- Filter by effect (Retime, Optical Flow, Machine Learning, Stabilization,
  Magnetic Mask, Auto Mask, Tracker, Transform, Spatial Conform, Rate
  Conform, Crop, Distort, Composition), by clip type, by role, or by
  Enabled/Disabled state.
- See each clip's Final Cut Pro **role** (Video, VFX, Dialogue.BoomMic ...) in
  its own column.
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
project. Timeline Index+ instead reads the library's own private SQLite
database live, through Apple's read-only Workflow Extension API - the same
data Final Cut Pro itself is showing, with no export step and no risk of the
snapshot going stale while you keep editing.

This gives it a few real advantages an FCPXML-based tool does not have:

- Multicam angle resolution matches the exact camera asset in use, including
  angles built from a double-system-sound sync group nested several levels
  deep - confirmed against a real FCPXML export as ground truth.
- Source In/Out timecodes use the source media's own real frame rate
  (`FFAsset.timecodeFrameDuration`), not a guess based on the clip's own
  internal time-value denominator, which is frequently not a literal frame
  rate at all.
- Clip identity, media resolution, and analysis-cache checks are all
  confirmed directly against Final Cut Pro's own library structure, not
  inferred from an XML export's own approximations.

The tradeoff is that this only ever works while Final Cut Pro has a project
open - there is no standalone/offline mode the way Data Burn-In has one,
since the whole point is reading the live library.

## Install And Enable

Timeline Index+ is a Final Cut Pro workflow extension inside Turnover.app, and
macOS only registers it once the app has been opened from the Applications
folder:

1. Quit Final Cut Pro.
2. Move `Turnover.app` to the **Applications** folder (not Downloads, not a
   disk image or a zip you opened in place).
3. Open Turnover once from the Applications folder.
4. Start Final Cut Pro. **Window > Extensions > Timeline Index+** is now listed.

Turnover warns in its sidebar ("Timeline Index+ is not set up") while the
extension is not registered, with buttons to show the app and open the
Applications folder. Requirements: a Mac with Apple silicon, macOS 14 or later,
and Final Cut Pro 10.7 or later.

**First use: allow Turnover to control Final Cut Pro.** The first time the panel opens, macOS asks
whether Turnover (Timeline Index+) may control Final Cut Pro. Choose **Allow**: that is how the panel
asks Final Cut Pro which project is open. If you chose Don't Allow, or the panel says it received no
project, open System Settings > Privacy & Security > Automation and switch Final Cut Pro on under
Turnover, then close and reopen the panel.

If it still does not appear: make sure only one copy of Turnover exists, open it
from Applications again, restart Final Cut Pro, and check that the sidebar
warning is gone. Final Cut Pro shows the Extensions menu only when at least one
extension is registered.

## Basic Workflow

1. Open a project in Final Cut Pro with the timeline you want to inspect.
2. Choose **Window > Extensions > Timeline Index+**.
3. The list populates with every clip on the current timeline. Use the
   search field, the effect filter, the type filter, the role filter, or
   the enabled/disabled filter to narrow it down.
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

- **Search**: matches clip names, media names and role names (type "boom" to
  find everything with a BoomMic role). Cmd-F moves to the search field.
- **Effect filter** (All Effects, or one of the effects below): shows only clips carrying a specific tracked effect or change
  (Retime, Optical Flow, Machine Learning, Stabilization, Magnetic Mask,
  Auto Mask, Tracker, Transform, Spatial Conform, Rate Conform, Crop,
  Distort, Composition).
- **Type filter**: All Types, Clip, Audio Only, Video Only, Sync Clip,
  Multicam Clip, Compound Clip, Title, Generator. Title and Generator are
  reported separately - both share the same underlying clip type in Final
  Cut Pro's own data, distinguished by the real Motion effect backing each
  one.
- **Role filter**: laid out like the Roles list in Final Cut Pro's own Index: **All Roles**, then the **video
  roles**, then the **audio roles**, each with Final Cut's colour for it. Only roles the open project uses are
  listed. A role that has sub-roles (Dialogue with BoomMic, MixL ...) opens them as a sub-list, so the main list stays short; in
  the sub-list **All Dialogue** shows the main role with every sub-role, and a sub-role shows only itself.
- **Enabled/Disabled filter**: cycles between All, Enabled Only, and
  Disabled Only, reading Final Cut's own Enable/Disable clip state.

## Roles

The **Role** column shows the roles Final Cut Pro gives each clip: the video
role and the audio role of the clip as a whole, written as Final Cut writes
them ("Video", "Titles", "VFX", "Adjustment Clips", "Dialogue", "Dialogue.BoomMic"),
each with a small square in **Final Cut's own colour** for that role. A role's
default sub-role is shown as just the main role. A sync clip often has a
different role on each audio channel (Camera, BoomMic, Mix ...); the column
leaves those out to stay short, hover a cell to see every role of the clip, and
the detail panel under the list lists them too. The role filter and the search
field match channel roles as well.

Roles are read from the project's own role list, so custom roles show with the
names you gave them, and the colours are the ones set in Final Cut's Role Editor.
Like Final Cut's own Roles list, a clip's own audio channels do not count while they
are muted or switched off (the camera's audio under a synced recorder track, for
example), but the channels synced in from a recorder do; a clip whose only audio is
switched off still shows its main audio role (Dialogue). Where a channel is assigned a
role, that role is the clip's audio role: a clip set to Music whose channel is assigned
Effects is an Effects clip, as in Final Cut's timeline.
Storylines, gaps and transitions have no role and stay blank.

## Keyboard

- **Cmd-F**: go to the search field.
- **Down arrow** in the search field: go to the list.
- **Up / Down** in the list: move through clips (each move also moves Final Cut's
  playhead there).
- **Return** in the list: move the playhead to the selected clip again.

## Analysis Cache Check

Click the cache-status icon (a warning triangle or a checkmark, next to the
refresh button) to see a breakdown:

- **Stabilization** is checked per clip and is reliable - Final Cut's own
  cache file names the clip directly, so Timeline Index+ can confirm
  whether a specific clip's Stabilization analysis is actually cached, and
  link straight to that clip if it's missing.
- **Optical Flow, Machine Learning retime, Magnetic Mask, and Auto Mask**
  can only be reported in aggregate (how much cached data exists in the
  library overall) - Final Cut's own cache files for these types do not
  record which clip they belong to, so there is no reliable way to confirm
  a *specific* clip is covered. Re-running Analysis in Final Cut Pro before
  export remains the only sure way to check these.

The cache location itself is resolved the same way Final Cut Pro resolves
it internally - including libraries whose cache lives on an external/
relocated volume rather than next to the library.

## CSV Export

Exports the clips currently visible under the active search/filter, in
timeline order. Columns:

`Timeline In, Timeline Out, Duration, Source In, Source Out, Clip, Type,
Original Filename, Camera, Angle, Reel, Scene, Take, Notes, Custom
Metadata, Enabled, Features, Details, Title Text, Role, Audio Channel Roles`

- **Original Filename/Camera/Reel/Scene/Take/Custom Metadata** are resolved
  from every event database in the library at export time (not during
  ordinary browsing, to keep the list itself fast) - for a multicam/sync
  clip, this reflects the actual media in use, not the wrapper's own
  bookkeeping.
- **Source In/Out** use the source media's own real frame rate when it can
  be resolved this way (see "Why It Reads The Library Directly" above).
- **Title Text** is the actual on-screen text of a Title clip, read from its
  own Motion scene data - blank for anything that isn't a Title.
- **Role** is the column of the same name in the panel; **Audio Channel
  Roles** lists the roles of single audio channels, separated by semicolons.
- **Details** summarizes what changed for each tracked feature (e.g. a
  Blend Mode change, a Retime speed/direction, a Crop value) in plain,
  human-readable form.

## Known Limits

- Requires Final Cut Pro to have an open project - there is no
  offline/standalone mode.
- Multicam angle camera selection, when a clip's real media sits behind a
  double-system-sound sync group with no other disambiguating signal, uses
  a best-effort default (the alphabetically-first camera in that sync
  group) rather than a fully general per-placement selector. This matched
  the one real, ground-truth-verified case checked, but is not guaranteed
  for every possible sync-group shape.
- Positions were cross-checked row by row against Final Cut Pro's own Timeline
  Index export and the FCPXML of a complex project (246 of 246 rows match in
  name and position). Transitions are listed at their start, and a dissolve
  does not shift the clips after it. Rows Final Cut's own index does not list
  (for example the camera clip inside a sync clip, shown because it carries a
  tracked effect) can still appear in addition.
- Optical Flow, Machine Learning retime, Magnetic Mask, and Auto Mask
  analysis-cache checks are aggregate-only (see "Analysis Cache Check"
  above) - this is a confirmed limit of what Final Cut Pro's own cache
  files expose, not a gap in Timeline Index+ itself.
