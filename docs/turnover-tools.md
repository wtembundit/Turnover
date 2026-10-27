# Turnover Tools Guide

This guide explains the main Turnover workflows for Final Cut Pro VFX turnover in Turnover Standalone.

> Current status: Turnover is usable for the workflows we have tested, but it is still a work in progress. Some FCPXML edge cases still need refinement, especially complex retime/speed-ramp combinations, nested sync clips, titles connected across multiple clips, markers, transforms, and metadata in unusual timelines.

Every tool can be opened and run from the keyboard (Cmd-1 to Cmd-9, Cmd-R ...); see [Keyboard Shortcuts](keyboard-shortcuts.md).

## Using the Window

Drag an FCPXML, an FCPXMLD or a project from Final Cut Pro into the window (or use **Choose File**), choose a tool in the sidebar, check the settings shown for it, then **Run**. Tools that return a project to Final Cut Pro also offer **Send to Final Cut Pro**; the others save a file.

The sidebar has two tabs:

- **Turnover:** Conform Prep, Data Burn-In, Marker.
- **VFX:** VFX Naming, VFX Auto Marker, VFX Pull EDL, VFX Shot List, VFX Timeline, Title ↔ Caption.

**VFX Source** (Auto / Titles / Captions) is a shared setting of every VFX tool that reads shot numbers and notes from the timeline: VFX Naming, VFX Auto Marker, VFX Pull EDL, VFX Shot List and VFX Timeline. *Auto* uses whichever one the project has. If a project has VFX naming under both titles and captions, choose one explicitly.

## 🎞 Conform Prep

Prepares a timeline for VFX conform by flattening `sync-clip` items, and the common kinds of multicam clip, into easier-to-read original source clips where possible, as a video-only result. Multicam inside compound clips, nested multicam and similar shapes are skipped and listed in the report.

Best for:

- Timelines with many `sync-clip` items that need to become source-backed clips.
- VFX turnovers where source filename, visible source timecode, titles, markers, transforms, and metadata should survive as much as possible.
- Cases where an editor applied retime to the sync clip, the original inner clip, or both.

What it does:

- Flattens simple sync clips into `clip` or `asset-clip` elements that reference the original source.
- Combines outer sync-clip speed with inner original-clip speed when the structure is supported.
- Attempts to preserve speed segments, speed transitions/ramps, reverse segments, blade speed, titles, markers, transforms, and metadata.
- Normalizes offsets and durations so Final Cut Pro can import the generated FCPXML with fewer edit-boundary warnings.
- Writes a report showing which clips were flattened and which clips were skipped.

Important notes:

- **Preflight:** duplicate the timeline and keep the master timeline untouched. Conform Prep now removes understood audio structures automatically, similar in result to running on a duplicate after detaching/deleting audio.
- For critical conform checks, manually detach/delete audio on the duplicate first when practical. This gives the cleanest comparison, especially while validating source timing after relink.
- Audio, title, and marker preservation can introduce import behavior that obscures the source-flattening result. Validate those elements separately after clip timing and media references are confirmed.
- Not every FCPXML shape is guaranteed yet. Multicam is flattened for the common active-angle shapes only; anything else is skipped and reported.
- If a clip disappears or titles/markers drift after import, keep both the original XML and generated XML as a reproducible test case.

## 🔥 Data Burn-In

Creates review burn-ins from an FCPXML or Final Cut Pro project loaded into Turnover: text and timecode drawn from the timeline's own data (project, timeline timecode, source file and timecode, metadata, connected layers, VFX notes, captions, analysis details), either as a transparent overlay or burned into a reference video.

For the full user manual, see [Data Burn-In](./data-burn-in.md).

Build the Burn-In cache first (it reads the timeline); **Customize...** and **Export Video...** unlock once it is ready.

Settings on the Data Burn-In page:

- **Preset:** the saved layout to use.
- **Reference Video:** the picture to burn in and to preview on. Required for *Burned-In Reference*; optional for *Transparent Overlay*.
- **Export As:** *Transparent Overlay* or *Burned-In Reference*.
- **Export Video...:** renders the burn-in with the current settings.
- **Customize...:** opens the Customizer with a live preview.

Exports:

- **Transparent Overlay:** ProRes 4444 `.mov` with alpha and no audio. Place it above the timeline in Final Cut Pro. The frame rate defaults to *Auto*, read from the timeline.
- **Burned-In Reference:** your reference video with the burn-in on top. H.264, HEVC or ProRes 422 (HQ, standard, LT, Proxy); MP4 or MOV for H.264 and HEVC (ProRes is always MOV). The reference video's audio is kept.

### Customizer settings

The Customizer's inspector has four tabs (Option-Cmd-1 to 4):

- **Element:** a burn-in is a list of independent *elements* that you add, duplicate, hide and reorder. For the selected element:
  - **Placement:** drag it in the preview, or pick its corner and set exact *Move across* / *Move down* offsets.
  - **Content:** plain text plus live data from **Insert Data**, with optional **Conditions** that append text (for example `TEMP AUDIO` when the audio role contains Dialogue).
  - **Connected layers:** *Stack connected layers* (0-6) repeats the element's text for each connected clip above the primary storyline, with *Space between layers* and *Label tracks (V1, V2...)*.
  - **Captions:** which caption roles the element reads, and *Include disabled captions*.
  - **Style:** font, size, color, text and background opacity, padding; per element, or following the global style.
- **Display:** *Show labels by default*, *Show source file extensions*, the *Source Layers List* options (layers shown, Compact or Detailed, one or two lines) and per-item *Data Labels*.
- **Data:** which **Metadata** items feed Custom Metadata, and the **Analysis Details** to show: Transform, Position, Scale, Rotation, Crop, Distort, Spatial Conform, Stabilize, Optical Flow, Retime.
- **Export:** **Preset** (Save, Save As, Rename, Delete, Import/Export as JSON to share a look between machines) and **Export Video**:
  - *Mode*, *Codec* and *Container*.
  - *Frame Rate:* Auto, or a custom fps.
  - *Resolution:* Original, 200%, 150%, 75%, 50%, 25% or Custom. The picture always keeps its shape; text and padding scale with it.
  - *Bitrate* for H.264 and HEVC.
  - *Reveal when finished*, then **Export Video...**, which can be queued.

Preview and range:

- Mark an **In** and **Out** (I / O) to export only that range; X clears it.
- Click an empty part of the picture, or press Shift-Cmd-A, to deselect the element.
- F (or Esc to leave) shows the preview full screen.
- Exports run in a queue with *Stop Current* and *Stop All*.

## 🏷 Marker

Exports the markers of the current timeline to a list you can load elsewhere, for example into DaVinci Resolve.

Settings:

- **Marker Type:** *All Markers*, *Standard*, *To Do*, *Chapter*, or *Turnover Recheck*.
- **Format:** *EDL* (optimized for DaVinci Resolve), *CSV* or *TXT*.

Best for:

- Bringing the editor's markers into a grade, conform or VFX tool.
- Collecting everything that Turnover flagged for a second look (*Turnover Recheck*).

What it does:

- Reads the markers on the timeline and keeps the ones that match the chosen type.
- In an EDL the marker colors are Green for Standard, Red for To Do, Blue for Chapter and Purple for Turnover Recheck.
- *Turnover Recheck* lists the markers whose name starts with `TURNOVER RECHECK:`. Turnover's other tools add them where something needs a manual check after import.
- You choose the output folder when you run it.

Output:

```text
Markers - <Project Name> - <type>.edl
Markers - <Project Name> - <type>.csv
Markers - <Project Name> - <type>.txt
```

## 📝 VFX Naming

Automatically numbers `VFX NAMING` titles or captions, or resets them back to the `XXXX` placeholder.

Best for:

- Timelines with placeholders such as `ABC_SC99_XXXX`.
- Creating running shot numbers such as `0010`, `0020`, `0030`.
- Restarting numbering when the scene prefix changes.

Settings:

- **Mode:** *Auto Number* or *Reset to XXXX*.
- **Start** and **Step** (Auto Number): the first shot number and the increment. Both default to `10`, giving `0010`, `0020`, `0030`.
- **VFX Source:** titles, captions, or Auto.

What it does:

- Reads titles that use the bundled `VFX NAMING` Motion template.
- Finds names that end with the `XXXX` placeholder.
- Replaces only the placeholder with a running shot number.
- Imports an updated project back into Final Cut Pro.

Example:

```text
ABC_SC99_XXXX  ->  ABC_SC99_0010
ABC_SC99_XXXX  ->  ABC_SC99_0020
ABC_SC100_XXXX ->  ABC_SC100_0010
```

## 🔁 Resetting Names

Resets numbered `VFX NAMING` titles back to the `XXXX` placeholder.

Best for:

- Renumbering a timeline from scratch.
- Undoing an earlier numbering pass.
- Resetting after editorial order changes.

What it does:

- Reads numbered titles such as `ABC_SC99_0010`.
- Changes only the shot-number portion back to `XXXX`.
- Leaves the prefix and scene name unchanged.

Example:

```text
ABC_SC99_0010 -> ABC_SC99_XXXX
ABC_SC99_0020 -> ABC_SC99_XXXX
```

## 🛠 VFX Auto Marker

Creates Final Cut Pro markers from `VFX NAMING` titles or captions so downstream workflows can read shot positions.

Best for:

- Timelines where VFX naming is already approved.
- Creating marker references for VFX Shot List and VFX Pull EDL.
- Choosing the marker type that best matches the team's workflow.

Marker types:

- `Standard`: regular Final Cut Pro marker.
- `To Do`: follow-up marker.
- `Chapter`: chapter marker.

Settings:

- **Marker:** the type to create.
- **Rename and add notes:** on by default; see below.
- **VFX Source:** titles, captions, or Auto.

What it does:

- Lets the user choose marker type.
- Reads the VFX number from each naming title.
- Places markers at the title positions so other Turnover workflows can use them.
- `Rename and add notes` (on by default) writes VFX numbers and notes back into marker names/notes through FCPXML import. Turn it off to create marker anchors only.

`VFX Shot List` does not require renamed markers. It treats VFX titles as the source of truth for shot number and description, then uses markers as capture anchors.

`VFX Shot List` and `VFX Pull EDL` intentionally resolve VFX shots the same way, so their row counts and shot numbers should always match on the same timeline.

## 📋 VFX Shot List

Builds an Excel shot list with thumbnails from the current timeline.

Best for:

- Sending a VFX shot list to a vendor, online editor, or post supervisor.
- Including thumbnails for each VFX shot.
- Collecting timeline timecode, source timecode, filename, metadata, and remarks in one workbook.

Output:

```text
Desktop/
  VFX Shot List - <Project Name>/
    VFX Shot List - <Project Name>.xlsx
    Thumbnails/
```

Main columns:

- Thumbnail
- VFX Number
- Note
- Timeline TC In
- Duration (Frames)
- Source Filename
- Source TC In
- Source TC Out
- Metadata
- Remark

Settings: **Reference Movie** (the movie the thumbnails are taken from) and **VFX Source**.

Thumbnail capture behavior:

- Thumbnails are extracted from a reference movie whose timecode matches the timeline.
- Each thumbnail is taken at a user marker placed anywhere within the VFX title or caption's own visible range; the marker's name does not matter.

Important notes:

- The reference movie must start at the same timecode as the timeline, or thumbnails will land on the wrong frames.
- Timelines with heavy overlap should be checked after export to confirm the source ranges are correct.
- `VFX Shot List` and `VFX Pull EDL` should resolve the same VFX shot set. Both tools use markers as anchors and `VFX NAMING` titles or captions as the source of VFX number, note, and visible range.

## 🧾 VFX Pull EDL

Creates a source-pull EDL from VFX markers.

Best for:

- Sending source ranges to online/VFX.
- Adding handles to every pulled range.
- Pulling more than one overlapping layer under the same VFX shot.

Settings: **Handles** (0-240 frames per side) and **VFX Source**.

Handle frames:

- The entered value is applied to both sides of each source range.
- Entering `8` adds 8 frames to the head and 8 frames to the tail.
- The value does not need to be even.

Layer naming:

- The primary source uses `PL01`.
- Additional overlapping sources use `EL01`, `EL02`, and so on.

Output:

```text
VFX Pull EDL - <Project Name>.edl
```

Important notes:

- Renamed markers are not required. `VFX Pull EDL` uses marker positions as anchors and reads VFX number/note from `VFX NAMING` titles, matching the `VFX Shot List` row model.
- If `VFX Shot List` row detection changes, recheck `VFX Pull EDL` counts, source filenames, handles, and layer rows against the same timeline.

## 📦 VFX Timeline

Places returned VFX renders back into the Final Cut Pro timeline.

Best for:

- Conforming VFX delivery files back into an editorial timeline.
- Placing renders as connected clips, replacements, or auditions.
- Tracking delivery versions such as v1, v2, and v3.

Settings:

- **Deliveries Folder:** the folder with the returned renders.
- **Handles:** frames of handle included in each render, trimmed from both the head and the tail when it is placed (0-240).
- **Slate:** frames of slate at the start of each render, trimmed from the head (0-240).
- **Placement:** *Connected*, *Replace* or *Audition* (below).
- **VFX Source:** titles, captions, or Auto.

The result is imported into the Final Cut Pro event `📦 Turnover`.

Modes:

- `Connected`: places the render as a connected clip above the timeline.
- `Replace`: replaces an existing VFX item; if no item exists, it falls back to connected mode.
- `Audition`: adds the render as an audition version; if no item exists, it falls back to connected mode.

Output examples:

```text
📦 VFX Deliveries v1 - <Project Name>
📦 VFX Deliveries v2 - <Project Name>
```

Important notes:

- Render filenames should clearly match the VFX shot code.
- Timelines with complex title, marker, or clip overlap should be checked after import.

## 🔄 Title ↔ Caption

Converts VFX titles into captions, or captions into titles, so a timeline can move between the two ways of marking VFX shots.

Settings:

- **Direction:** *Title → Caption* or *Caption → Title*.
- **Original:** what happens to the items that were converted.
  - *Keep* leaves the original in place and adds the new one alongside.
  - *Disable* does the same, but switches the original off in Final Cut Pro.
  - *Delete* removes the original.

Best for:

- Projects that name VFX shots with titles but need captions (or the other way round) for the other tools, the shot list or the burn-in.
- Moving a naming pass from one form to the other without retyping it.

Output:

```text
<Project Name> - Title to Caption.fcpxml
<Project Name> - Caption to Title.fcpxml
```

Important notes:

- Check the result after import, especially when you choose *Delete*; *Keep* or *Disable* are the safer first passes.
