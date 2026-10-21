# Turnover v1.5.0 Release Notes

Turnover v1.5.0 is a big update to the Standalone app: a redesigned window, automatic
updates, a much more flexible Data Burn-In, a round of Conform Prep fixes, and a long list of
Timeline Index+ accuracy fixes. The SpliceKit plugin is no longer part of Turnover.

## Updating

- **This is the first version that updates itself.** Turnover now checks for new versions
  in the background and installs them from the app menu (Turnover > Check for Updates...).
  Copies older than v1.5.0 cannot do this, so install this one by hand: download it from the
  release page, move Turnover to the **Applications** folder, and open it from there.
- Requires a Mac with Apple silicon and macOS 14 or later.
- The SpliceKit edition and its Lua tools have been retired. Everything they did is in the
  Standalone app.

## The new window

- A sidebar with two tabs, **Turnover** and **VFX**, each listing its tools. The FCPXML you
  are working on sits at the top of the sidebar, because every tool uses the same file; drop
  a file anywhere in the window to replace it.
- All settings live in the main window, with the Liquid Glass look on macOS 26.
- Run, Send to Final Cut Pro and the other actions are laid out the same way in every tool.

## Data Burn-In

**A cache-first flow.** Build the Burn-In cache first; Customize, Reference Video and Export
unlock once it is ready. Building no longer pops the Customizer open.

**A redesigned Customizer.**
- A burn-in is a list of independent **elements**. Add, duplicate, delete, show or hide and
  reorder them, and drag them anywhere in the preview (they snap to corners and the centre).
  Offsets are fractions of the frame, so a preset looks the same at any resolution.
- Global style and per-element style are separated: an element can follow the global look or
  have its own size, colour and padding.
- Undo and redo for every change, reset buttons for Move across / Move down, labels that
  are never cut off.

**Export.**
- **Resolution:** scale an export up or down (200%, 150%, 75%, 50%, 25%, or 2160p to 360p)
  while keeping the picture's aspect ratio. Text and padding scale with the picture.
- Burned-in **ProRes 422** (HQ, standard, LT, Proxy) is available again, with PCM audio.
- Fixed an export that could freeze part-way through when the picture was ready before its
  audio.

**Connected layers, without extra data items.** Each element can repeat its own text for every
connected layer, stacked away from the frame edge: file, timecode, metadata **and analysis**
(retime, transform, crop...) come from that layer's own clip. A blank line separates the
groups, and an optional label names each by its video track: V1 for the primary storyline,
V2 for a clip connected on lane 1, V3 for lane 2. The older Source Layers list items still
exist and are now labelled as a separate feature in the Display tab.

**Captions as data.** Caption text, role, name and VFX number/note are available to any
element. Choose which caption roles an element reads (SRT, iTT, CEA-608, any role name) to
burn in subtitles or to show VFX shots, with the element's own font, size, colour and
position. Captions switched off in Final Cut Pro are skipped unless you turn on
"Include disabled captions" for that element.

**More accurate source data.**
- Clips with a Conform Rate (for example 23.98 or 47.95 material in a 24p project) now
  advance one source frame per timeline frame, as Final Cut Pro does, so source timecode no
  longer drifts on long clips.
- Fixed shots that had no source data at all: a retimed Synchronize Clips item whose picture
  sat inside it.
- Clip starts are matched with a small tolerance so a clip's first frame is never taken for
  the previous clip.
- Fixed a clip missing from Data Burn-In when it had an unrelated connected item nested inside
  it, a retimed clip inside a sync clip not showing its Speed, connected layers collapsing
  when two stacked layers shared a source position, and a connected sync insert showing the
  take's full length instead of its short placed window.

## Conform Prep

Conform Prep flattens a project for conform: sync clips, and the common kinds of multicam
clip, become plain video-only clips that reference the original media, with retimes, titles,
markers and effects kept.

- A hold at the head of a retimed clip, and the speed ramps after it, are kept.
- Captions inside a sync clip stay with the flattened clip, and sync clips whose extras are
  captions or connected picture layers now flatten.
- Multicam clips with stacked speeds (a retime on the multicam clip plus one inside the
  angle) multiply correctly, and the result imports into Final Cut Pro: very large time
  fractions that made Final Cut drop clips are trimmed and rounded.
- Fixed a crash on projects graded with certain colour tools, and a connected clip on its own
  lane being dropped.
- The guide now lists exactly which multicam shapes are flattened and which are skipped and
  reported (multicam inside compound clips, nested multicam, angles that are sync clips).

## Timeline Index+

Checked row by row against Final Cut Pro's own Timeline Index export of a complex project:
every row now matches in name and position.
- A transition no longer pushes the clips after it later, and transitions are listed.
- Sync clips on the primary storyline use their own position instead of an audio clip
  connected inside them.
- Connected storylines, and the clips inside them, no longer start late by the length of
  their connect point.
- Connected clips report their real position after trimming or slipping; clips whose audio
  was detached and replaced by a gap are listed; internal placeholder gaps are not; animated
  Transform values read as one line per property.
- **Setup help:** Final Cut Pro only lists the extension once Turnover has been opened from
  the Applications folder. Turnover now warns in its sidebar when that is not the case, and
  registers the extension when it can.
- The panel has a **Copy Diagnostics** button that records what it did and what Final Cut Pro
  answered, to make problems reportable, and says so when Final Cut has not sent a project.
- Read access for the library is now requested explicitly, and the Final Cut Pro Trial and
  Creator Studio variants are covered.

## Known limits

- Multicam flattening still skips the shapes listed above.
- On some Macs the Timeline Index+ panel has been reported to stay on loading; this release
  adds the diagnostics and the access changes above but the cause was not reproduced.
- Retimed clips that also carry a Conform Rate use their time map as is in Data Burn-In.
