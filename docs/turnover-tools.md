# Turnover Tools Guide

This guide explains the main Turnover workflows for Final Cut Pro VFX turnover in Turnover Standalone. See the [Standalone Guide](../standalone/TurnoverApp/README.md) for installation and input methods.

> Current status: Turnover is usable for the workflows we have tested, but it is still a work in progress. Some FCPXML edge cases still need refinement, especially complex retime/speed-ramp combinations, nested sync clips, titles connected across multiple clips, markers, transforms, and metadata in unusual timelines.

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

Creates review burn-ins from an FCPXML or Final Cut Pro project loaded into Turnover.

Exports:

- **Transparent overlay:** a ProRes 4444 `.mov` with alpha, using a saved preset.
- **Burned-in reference:** H.264/HEVC export over a reference video.
- **Customizer:** preview, style changes, metadata selection, range export, and preset editing.

Best for:

- Quick transparent burn-in overlays that can be placed above a timeline in Final Cut Pro.
- Review exports where the burn-in layout should match a saved team preset.
- Customizing fields, labels, metadata, style, or export range.

Important notes:

- Transparent export is ProRes 4444 `.mov` with alpha and no audio.

## 📝 VFX Naming

Automatically numbers `VFX NAMING` titles or captions, or resets them back to the `XXXX` placeholder.

Best for:

- Timelines with placeholders such as `ABC_SC99_XXXX`.
- Creating running shot numbers such as `0010`, `0020`, `0030`.
- Restarting numbering when the scene prefix changes.

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
