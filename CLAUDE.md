# CLAUDE.md

The Xbox 360 Blades dashboard: QML shell + Python helpers. **This repo is only the
program.** It is packaged and configured by `landonreekstin/nixos-config`
(`pkgs/xbox360-dashboard/default.nix`, pinned by `rev`/`hash`, and the Hyprland `xbox360`
desktop module that generates its config). Everything host-specific lives there; design
and history notes are in that repo's `docs/xbox360.md`.

## The contract with nixos-config

Nix provides, this repo consumes:

- `shell/Config.js` — generated, gitignored. Every value the QML reads as `Cfg.<name>`
  (`import "Config.js" as Cfg`). **Adding a `Cfg.foo` here needs a matching `var foo` in
  the Nix module**, or the shell fails to load; land the nixos-config side in the same
  change (bump the pin there).
- `shell/blade.frag.qsb` — compiled from `shaders/blade.frag.in` after the Nix side
  substitutes `@N@ @KY@ @KD@ @CI@ @CR@` (curve knots, card inset/radius). Keep those
  placeholders.
- `pad/xbox360-pad.py` and `pad/xbox360-audio.py` take one JSON config path as argv[1].
  New keys need the Nix side to write them.
- The shell is run as `qs -c xbox360` and driven over `qs -c xbox360 ipc call ui
  <guide|home|lock|power>` — keybinds and swayidle in nixos-config call those names.

## Working on it

- Commit style: `type(scope): description`, as in nixos-config
  (`feat|fix|tweak|docs`, scope usually `shell`, `pad`, `guide`, a page name).
- Hot reload: copy the generated config next to the QML and run Quickshell on the
  checkout, inside the Xbox 360 session:
  `cp ~/.config/quickshell/xbox360/Config.js ~/.config/quickshell/xbox360/blade.frag.qsb shell/ && qs -p shell`
  (stop the session's own `qs -c xbox360` first). Quickshell reloads on save.
- Releasing a change: push here, then in nixos-config bump `rev`/`hash` in
  `pkgs/xbox360-dashboard/default.nix` (see its `docs/companion-repos.md`), rebuild, and
  verify by logging into the session.
