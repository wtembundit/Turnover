# Turnover v1.5.1 Release Notes

A small update: one Data Burn-In fix, and the Timeline Index+ fixes from v1.5.0 for anyone who
installed the first v1.5.0 download.

## Read this if Timeline Index+ stays on "Loading..."

For about nine hours on 22 October the v1.5.0 download was a first build that did not yet contain
the two Timeline Index+ fixes listed in the v1.5.0 notes. On that build the panel stays on
"Loading..." (or says "No active project") on Macs with System Integrity Protection on, which is
every normal Mac. It was replaced under the same version number, so Turnover could not tell the
two apart. **v1.5.1 contains the fixes**: if you installed v1.5.0 that day, update now.

- Turnover > Check for Updates... offers v1.5.1. Or download it from the release page and replace
  Turnover in the **Applications** folder.
- Open Turnover once from the Applications folder, then reopen Final Cut Pro.
- The first time you open Timeline Index+, macOS asks whether Turnover may control Final Cut Pro:
  choose **Allow**.

## Data Burn-In

- **Missing connected layers.** A clip connected above the primary storyline was left out of the
  burn-in (its video track and timecode never appeared) when it was a synced clip that is itself
  retimed and holds its own clips inside. All such layers now show, with the right source timecode
  and retime, and numbered by video track like the others (V2, V3...).
- A connected synced clip that shows only part of a longer clip inside it now follows its own
  window on the timeline. It could previously appear to run for as long as the clip it sits on.

## Notes

Only the Standalone app changed. Timeline Index+ is the same as in v1.5.0 apart from the
installation problem above.
