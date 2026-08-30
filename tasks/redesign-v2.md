# Cockpit v2 — grouped nav, native-quiet, zero customization

Decided with Stephane 2026-08-26. Supersedes the open v1.3 UX list.

## Why
1. "Selecting in Customize doesn't work" — root cause below.
2. "Design too complex to understand" — 25 tabs flat + a Customize panel with
   per-tab widget editing, layout presets and filter chips.
3. "Don't like the sidebar design" — partly `theme: high-contrast` was set.

## Root cause of the selection bug
Tab visibility is driven by `userPrefs.enabledTabs`. TWO surfaces write it
(the rail's `+` picker and the Customize checkboxes) and a THIRD thing can
override it (`applyLayoutOverlay` re-hides via v2 `hiddenTabs`, which the
Customize toggle never clears). Stephane's stored value was only
`["welcome","custom","routines","timeline"]`.
Separately, prefs are split across two globalState buckets after an
extension-id rename: `dashable.claude-cockpit` (old, holds his widget picks)
vs `dashable.claude-code-cockpit` (what actually gets read).

Fix by construction: show every tab, always. `enabledTabs` stops mattering.

## Decisions (locked)
- Keep all 25 tabs, grouped into 5 with sub-navigation.
- Native VSCode-quiet: theme tokens only, no custom palette, tight type scale.
- Remove customization ENTIRELY — no widget picker, no tab show/hide, no
  layout presets, no theme selector. Theme follows VSCode.

## Groups
- **Now**     overview(ex-custom), now, watchtower, office, approval, replay
- **Work**    skills, agents, gallery, routines, library, talk
- **Explore** browse, history, obsidian, discover, timeline
- **System**  mac, security, settings, self, recs
- **Learn**   welcome, tutorial, help

## Tasks
- [x] A1 `TAB_GROUPS` constant + `groupOf(id)` + active-group state
- [x] A2 `getEnabledTabIds` → full catalogue (keep surface-disabled hiding only)
- [x] A3 `renderTabBar` → group row + sub-tab row; drop rail/+/×/collapse
- [x] A4 Delete customize: panel, hint, ⚙ button, all its handlers
- [x] A5 Theme forced to auto; drop theme pref UI
- [x] A6 Drop now-dead commands/keybindings from package.json; ⌘1–⌘5 = groups
- [x] B1 CSS: native-quiet restyle of header + nav
- [x] C1 compile + 139 tests + package + reinstall + verify
