# Turnover v1.5.2 Release Notes

A bug-fix and polish update: keyboard control for the whole app, roles in Timeline Index+, a steady preview
playback, and a simpler, shape-safe Resolution for Data Burn-In exports.

## Keyboard

- **Cmd-1 to Cmd-9 open the tools**, in sidebar order (1 Conform Prep ... 9 Title <-> Caption); **Cmd-[ and Cmd-]**
  step to the previous or next tool.
- **Cmd-R** runs the tool, **Shift-Cmd-R** sends the result to Final Cut Pro, **Cmd-K** opens the Burn-In
  Customizer, **Cmd-E** exports the video, **Cmd-.** stops the current export. A new **Tools** menu lists them all;
  commands that cannot be used yet are grayed out.
- **Customizer:** a new **Element** menu with Cmd-N (new element), Cmd-D (duplicate), Option-Cmd-Delete,
  Shift-Cmd-H (hide/show), Option-Cmd-Up/Down (reorder) and Option-Cmd-1 to 4 for the Element, Display, Data and
  Export settings. Up/Down select the previous/next element, and **Option plus an arrow nudges the selected
  element by a pixel** (Shift as well for ten). Cmd-key combinations now always reach the menus; before, a few of
  them (such as Cmd-O) were taken by the preview's single-key controls while the Customizer was open.
- **Timeline Index+:** Cmd-F goes to the search field, Down arrow from there goes to the list, Return moves the
  playhead to the selected clip again.
- Tab and Shift-Tab move between controls; macOS needs **Keyboard navigation** switched on (System Settings >
  Keyboard) for Tab to reach every button and menu.

See [Keyboard Shortcuts](https://github.com/wtembundit/Turnover/blob/main/docs/keyboard-shortcuts.md) for the full list.

## Timeline Index+

- **A Role column**, with each clip's Final Cut Pro role as Final Cut writes it (Video, Titles, VFX, Adjustment
  Clips, Dialogue, Dialogue.BoomMic ...) and a small square in **Final Cut's own colour** for it. Custom roles show
  under the names you gave them. A sync clip's single audio channels (Camera, BoomMic, Mix ...) are in the tooltip and
  the detail panel.
- **A Role filter** laid out like the Roles list in Final Cut's own Index: video roles, then audio roles, each with its
  colour. A role with sub-roles opens them as a sub-list instead of making the list long: pick **All Dialogue** for the
  main role with everything under it, or a single sub-role. Only the roles of the open project are listed.
- Sub-roles are read from every audio channel of a clip, including the channels synced in from a separate recorder
  (BoomMic, MixL, MixR-1 ... Namphet, Yak), and follow Final Cut's own rules: a muted or switched-off channel of the
  clip itself does not count, and a channel's role overrides the clip-level audio role. On the test project the video
  roles, the audio roles and all the Dialogue sub-roles match Final Cut's Index Roles list one for one.
- The first filter is now called **All Effects** (it was a bare "All"), and its column **Effects / Changes**, so each of the
  three filters says what it chooses: effects, types, roles. The CSV export keeps its "Features" column name.
- The search field also matches role names.
- The CSV export has two new columns, **Role** and **Audio Channel Roles**.

## Data Burn-In

- **Playback in the Customizer is steady.** Without a reference video, Play moved one frame on every tick of a
  60-per-second timer, so a 24 fps project ran at about two and a half times speed and stuttered: the burned-in
  text and timecode changed so fast that the bottom of the preview seemed to shake. It now follows the real clock
  at the speed you pick (1x, or J/L for faster), and stops at the end it is travelling towards.
- **Full screen preview.** A new button at the right of the transport bar (or **F**, or Shift-Cmd-F) puts the preview alone on
  the whole screen, with the transport bar under it, to check the burn-in at full size. F, Esc or the button brings the
  panels back.
- **Resolution is one choice, not two.** The export menu used to mix percentages with named sizes (1080p, 720p ...),
  which went wrong for pictures that are not 16:9. Now it is **Original**, a percentage (200%, 150%, 75%, 50%, 25%)
  or **Custom**, and every choice scales both edges together, so the picture keeps its shape: portrait stays
  portrait, 4:3 stays 4:3, 2.39:1 stays 2.39:1. Each menu entry shows the size it gives for your picture, and the
  line under it states the result and its shape ("1280 x 720, 16:9").
- **Custom** takes a width or a height, whichever you type; the other edge follows. The estimated file size
  follows too.
- Presets saved with the old named sizes (such as 1080p) still work and mean the same as before. The command-line
  option `--resolution` accepts `original`, a percentage, `w1280`, `h720` or `1080p`, and `--burn-in-transparent`
  accepts it too.

## Notes

Only the Standalone app and its Timeline Index+ panel changed. The Final Cut Pro side of Timeline Index+ (the
permission prompt on first use, opening Turnover once from the Applications folder) is as in v1.5.1.
