# Data Burn-In User Manual

Data Burn-In lets editors preview and export customizable burn-in overlays from
an FCPXML timeline. It is designed for review, turnover, source checking, VFX
communication, and editorial QC without requiring users to inspect internal
cache or manifest files.

## What It Does

Data Burn-In reads an FCPXML or FCPXMLD timeline, builds a frame-resolved cache,
and lets the user place live timeline/source data on top of a reference video or
a transparent overlay.

Common uses:

- Show project, timeline timecode, source filename, and source timecode.
- Show selected metadata such as scene, take, reel, camera name, angle, or
  custom metadata.
- Show connected source layers above the Primary Storyline.
- Show analysis details such as retime, transform, position, scale, crop,
  stabilize, optical flow, and spatial conform.
- Add conditional messages, such as temporary audio warnings, when timeline data
  matches a condition.
- Export a burned-in reference video or transparent ProRes 4444 overlay.

## Basic Workflow

### Standalone Customizer

1. Open Turnover standalone app.
2. Drop or choose an FCPXML/FCPXMLD file.
3. Switch to **Data Burn-In**.
4. Build the Data Burn-In cache.
5. Open **Customize**.
6. Choose a preset or edit the Custom preset.
7. Select a field position, type plain text, or use **Insert Data**.
8. Adjust display, metadata, analysis details, source layers, and style settings.
9. Optionally mark an export range with In/Out.
10. Export a burned-in reference or transparent overlay.

Choose a reference video when you need preview playback with picture/audio or a
burned-in reference export. Transparent overlay export can run without a
reference video.

## Elements: Placing Text Freely

A burn-in is a list of independent **elements**. In the Customizer's Elements panel you can
add, duplicate, delete, show or hide each one, and reorder them (right-click a row; later rows
are drawn on top). Any number of elements can share a corner.

To place an element, drag it in the preview. It snaps to its corner and to the centre lines of the
frame. The Element tab also has the 3x3 corner picker (the corner or edge the text is measured
from, which also decides its alignment) and exact "Move across" / "Move down" values in pixels of
the frame. Offsets are stored as fractions of the frame, so a preset keeps its look at any
resolution. Presets saved before this keep working: each of their fields becomes one element.

### Connected layers inside an element

Each element has a **Stack connected layers** setting (0-6). When it is above 0 and the text
contains source data (file, timecode, metadata...), the same text is repeated for each connected layer
visible on that frame, stacked away from the frame edge: above the source line
for bottom positions, below it for the others. The layers are separated by a blank line (**Space between layers**), and **Label tracks (V1, V2...)** can
prefix every block with its video track like in the timeline: V1 is the primary storyline, V2 a clip
connected on lane 1, V3 lane 2. No extra data items are needed.

### Captions as subtitles or VFX shots

Captions in the timeline (any role, e.g. `SRT.th`, `ITT.en`) are available as data: Caption Text, Caption
Role, Caption Name, Caption VFX Number and Caption VFX Note. In the Element tab, **Captions** lists the
roles found in the timeline; choose which roles that element reads (none selected = all). Put Caption Text
in an element near the bottom for subtitles, or Caption VFX Number to show VFX shots. Font, size, color and
position are the element's own. Captions switched off in Final Cut Pro are skipped unless that
element turns on **Include disabled captions**. A VFX-named caption also feeds the normal VFX Number / VFX Note data.

## Field Builder

Each burn-in field has:

- **Position:** Top, Middle, or Bottom across Left, Center, and Right.
- **Content:** plain text plus inserted dynamic data.
- **Show this field:** enables or disables that field.
- **Conditions:** optional append text when timeline data matches.

Plain text stays exactly as typed. Dynamic values use tokens inserted from
**Insert Data**, so users do not need to memorize token names.

Useful examples:

```text
{project}
{source_file}
{source_tc} {metadata_custom}
TEMP AUDIO
{source_layers_details}
```

## Dynamic Data

Supported user-facing data groups:

- Project and timeline values.
- Timeline timecode and timeline frame rate.
- Source filename, source timecode, source frame, and source FPS.
- Source layers and source layer timecode.
- Selected metadata.
- VFX title text or notes.
- Audio-role data for condition workflows.
- Analysis details such as retime, transform, position, scale, crop,
  stabilization, optical flow, and spatial conform.

Data that is missing for a frame renders as empty text, not as raw internal keys
or unrelated fallback values.

## Metadata

Metadata display is user-selected.

- Metadata names are shown as human-readable labels.
- Internal keys such as `com.apple...` or `kMDItem...` are hidden or converted
  to readable names.
- If specific metadata keys are selected, only those keys render.
- If a selected key is missing on the current clip, it renders nothing.
- If no metadata keys are selected, Turnover may use a concise default metadata
  summary.

## Source Layers

Source-derived fields follow Final Cut Pro's visible timeline model:

- **Primary source:** the Primary Storyline source at the timeline frame.
- **Layer 1, Layer 2, ...:** connected visible video clips above the Primary
  Storyline.
- **Connected layers:** positive-lane clips are treated as source layers,
  including connected `clip`, `sync-clip`, `asset-clip`, `ref-clip`, and
  selected video `mc-clip` containers nested inside a primary clip.
- **Source layers setting:** limits how many connected video layers are shown.
  Use `0` to hide connected layers.
- **Layer display:** Compact shows one short connected-clip line; Detailed uses
  selected source, metadata, and retime values.
- **Detail layout:** One Line keeps each layer compact; Two Lines separates the
  layer name from the selected values when a corner needs more readable spacing.
- **No fallback:** if no connected layer exists, source-layer fields render
  empty.

When more than one connected clip is visible on the same frame, Data Burn-In
orders them by lane/layer so Source Layer 1 and Source Layer 2 can be displayed
separately according to the preset's Source Layers limit.

Detailed layer labels follow the global **Show labels** rule and the Source
Layers/Source Layer Details label override. Turning labels off leaves readable
values separated by spacing rather than repeated prefixes.

## Analysis Details

Analysis Details are controlled by checkboxes so users can choose what appears:

- Transform
- Position
- Scale
- Rotation
- Crop
- Distort
- Spatial Conform
- Stabilize
- Optical Flow
- Retime

Position values are converted from FCPXML transform units to editor-facing
pixels when the timeline format height is known. FCPXML stores transform
position as normalized values relative to the project frame height, so Turnover
displays `x/y ... px` instead of ambiguous raw decimals.

## Preview Player

The Customizer preview supports:

- Space: play/pause
- J/K/L: shuttle-style playback controls
- Left/Right (or , and .): step backward/forward one frame
- Up/Down: select the previous/next element
- Option plus an arrow: nudge the selected element by one pixel (Shift as well: ten)
- I: mark In
- O: mark Out
- X: clear marked range
- Click the picture (anywhere that is not an element) to **deselect** the element, so the preview has no orange frame; click
  an element, or its row in the list, to select it again. Shift-Cmd-A does the same from the keyboard.
- F, or the arrows button at the right of the transport bar: **full screen preview**. The preview fills the screen with the
  transport bar below it; the other panels and all element frames are hidden. Press F, Esc or the button again to come back.

Keyboard shortcuts work when the player or preview area is active, but do not
steal typing while a text field is being edited. The Element menu adds Cmd-N (new
element), Cmd-D (duplicate), Option-Cmd-Delete, Shift-Cmd-H (hide/show), Option-Cmd-Up/Down
(reorder) and Option-Cmd-1...4 for the four settings tabs; see
[Keyboard Shortcuts](keyboard-shortcuts.md). If the selected reference
video has audio, preview playback plays audio.

## Mark In/Out Export Range

Users can mark a shorter export range instead of exporting the full video.

- Marked range appears inside the scrub bar.
- The playhead/current-frame indicator remains visible above the range.
- Export uses the marked range when both In and Out are set.
- Clearing the range returns export to full duration.

## Presets

Presets store reusable burn-in layouts.

Preset data includes:

- Field text and positions.
- Show/hide state per field.
- Labels and label overrides.
- File extension preference.
- Metadata selections.
- Conditions.
- Global and per-field style.
- Analysis detail options.
- Source layer options.
- Export settings.

Preset actions:

- **Save:** update the selected local preset.
- **Save As:** create a new named preset.
- **Import:** load a JSON preset from another machine.
- **Export:** save a JSON preset for sharing.
- **Delete:** remove a local preset when it is not protected.

## Style Settings

Users can adjust:

- Font size.
- Text opacity.
- Text color with the macOS color picker.
- Background opacity.
- X/Y padding.
- Global style or per-field overrides.

Preview and export should match for font, size, opacity, color, padding, and
anchor position.

## Export Modes

### Transparent Overlay

- Codec: ProRes 4444.
- Container: `.mov`.
- Includes alpha.
- Does not include audio.
- Frame rate defaults to **Auto**, read from the FCPXML timeline. This is
  important for transparent overlay because there is no reference video file to
  inherit timing from.
- If Auto looks wrong, switch Frame Rate to Custom before export.

### Burned-In Reference

- Codec: H.264, HEVC, or ProRes 422 (HQ, standard, LT, Proxy).
- Container: MP4 or MOV for H.264 and HEVC; ProRes is always MOV.
- Includes audio when the selected reference video has audio. H.264 and HEVC
  carry it as AAC; ProRes carries it as uncompressed PCM.
- Frame rate defaults to **Auto** from the FCPXML timeline.
- Custom frame rate is available as a correction option before export.
- **Resolution** scales the output up or down and always keeps the picture's shape: a portrait picture
  stays portrait, 4:3 stays 4:3, 2.39:1 stays 2.39:1. Text and padding scale with the picture. Choose
  **Original**, one of the percentages (200%, 150%, 75%, 50%, 25%) or **Custom**. Each menu entry shows
  the size it gives for your picture, and the line under the row states the result and its shape
  ("1280 x 720, 16:9"). In Custom, type a width or a height; the other edge follows, so the shape cannot
  drift. Sizes are made even, which video codecs require, and capped at 8192 pixels.

Transparent Overlay remains locked to ProRes 4444; ProRes 4444 is not offered
for a burned-in reference, where there is no alpha to keep.

For testing from a terminal, the app can render a burned-in reference without the
interface:

```zsh
Turnover.app/Contents/MacOS/Turnover --burn-in-video \
  --source-xml Project.fcpxmld --reference-video Reference.mov \
  --output Out.mov --codec prores422 [--seconds 30] [--resolution 50%] [--preset-name "Source Burn-In"]
```

The `--resolution` option takes `original`, a percentage (`50%`), a width (`w1280`), a height (`h720`) or the
shorter edge in pixels (`1080p`, as before). `--burn-in-transparent` accepts it too.

## Export Queue

Exports can be queued.

- **Now Exporting:** shows the current job.
- **Queued:** shows pending jobs.
- **Stop Current:** cancels the current export and continues with the next job.
- **Stop All:** cancels the current export and clears the queue.
- Completed jobs may reveal in Finder if enabled.

## Good to Know

Clips with a Conform Rate (for example 23.98 or 47.95 material in a 24p timeline) advance one source
frame per timeline frame, as Final Cut Pro does, so their source timecode stays frame-accurate over long
clips. Retimed clips keep their own time map.

Multicam source resolution is experimental. When source/timecode accuracy is
critical, flatten multicam clips before running Data Burn-In, then export or send
the flattened timeline to Turnover.
