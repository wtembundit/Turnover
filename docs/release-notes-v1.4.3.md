# Turnover v1.4.3 Release Notes

Turnover v1.4.3 introduces **Timeline Index+**, a native Final Cut Pro
Workflow Extension bundled inside Turnover Standalone - no separate install.
It's a new way to work with Turnover: instead of exporting FCPXML first, it
reads your project directly from Final Cut Pro's own library, live.

## Introducing Timeline Index+

Open it from **Window > Extensions > Timeline Index+** in Final Cut Pro.

- **Every clip on the timeline, live** - ordinary clips, sync clips,
  multicam clips/angles, compound clips, storylines, gaps, titles, and
  generators, updated as you edit, with no export step.
- **Filter by feature** - Retime, Optical Flow, Machine Learning,
  Stabilization, Magnetic Mask, Auto Mask, Tracker, Transform, Spatial
  Conform, Rate Conform, Crop, Distort, Composition - by clip type, or by
  Enabled/Disabled state. Search by name.
- **Playhead sync** - select a row and Final Cut's playhead jumps there;
  move Final Cut's own playhead and the matching row selects itself.
- **Plain-language change details** - see exactly what changed on a clip
  (Blend Mode, Transform, Retime speed, Crop, and more) without digging
  through the Inspector.
- **CSV export** - the current filtered list, in timeline order, with real
  Timeline and Source In/Out timecodes, camera/reel/scene/take/custom
  metadata resolved from your whole library, on-screen Title text, and a
  plain-language summary of every tracked change.
- **Analysis cache check** - before an export that depends on cached
  analysis (Optical Flow, Machine Learning retime, Stabilization, Magnetic
  Mask, Auto Mask), see a breakdown of what's actually cached, including
  libraries whose cache lives on a relocated external volume. Clips still
  needing analysis are clickable, so you can jump straight to each one.
- **Open Turnover** - a header button opens the full Turnover Standalone
  app directly, for Data Burn-In and the rest of the tools, without
  leaving Final Cut Pro.

## Distribution

- Signed with a Developer ID Application certificate and notarized by
  Apple - previously an ad-hoc/Apple Development signature only.

This release only updates the Standalone edition (bundling Timeline
Index+). The SpliceKit edition is unchanged from v1.4.2.
