# Hyprland Dots (Personal)

Personal Hyprland configuration with a split, layered `conf.d` setup, some theming, and a small Rust TUI to quickly pick a config file to edit. This repo is shared as a reference; feel free to copy bits as I did from others, but there’s no turnkey installer yet and much of the config is quite niche and/or opinionated.

## Folder Structure

- `hyprland.conf` — top-level compositor config; defines `$confDir` and `$scrPath`, and sources `conf.d/*.conf`.
- `conf.d/` — numbered includes by topic to keep things sane and mergeable:
  - `00-env.conf` (env + compositor hints)
  - `05-exec.conf` (autostart services; eww, portal reset, cliphist, hyprpaper)
  - `10-input.conf`, `20-layout.conf`, `30-render.conf`
  - `40-workspaces.conf`, `50-misc.conf`, `55-animations.conf`
  - `60-windowrules.conf`, `70-binds.conf`, `80-monitors.conf`
  - `90-theme.conf` (sources `themes/`), `91-plugins.conf`, `92-userprefs.conf`
  - `99-overrides.conf` (local drop-ins)
- `hyprpaper.conf`, `hypridle.conf`, `hyprlock.conf` — utility daemons and lockscreen config.
- `themes/` — `common.conf`, `colors.conf`, `theme.conf` (sourced via `conf.d/90-theme.conf`).
- `plugins/` — optional Hyprland plugin configs (e.g. hyprscrolling, hyprwinwrap); toggled via `hyprpm`.
- `hyprconf/` — Rust TUI used to search/pick a config file to edit (see below).

## Configuration Strategy

- Single entrypoint: `hyprland.conf` sources numbered `conf.d` files to keep scope small and changes conflict-free.
- The `90-theme.conf` groups theme files (`themes/common.conf`, `themes/colors.conf`, `themes/theme.conf`).
- `91-plugins.conf` contains disabled-by-default sources; enable with `hyprpm` and/or toggle the `source` lines.
- `92-userprefs.conf` is the “personal sandbox” for bindings/layout tweaks that aren’t broadly useful.
- `95-nvidia.conf`/`99-overrides.conf` are for machine-specific or vendor-specific overrides.
- Utilities (`hyprpaper`, `hypridle`, `hyprlock`) are isolated in their own files for clarity.

## Rust TUI: `hyprconf`

`hyprconf` is a small TUI to skim-search config files and open one in your editor.

- Scans: `hyprland.conf`, utilities (`hyprpaper`, `hyprlock`, `hypridle`), `conf.d/*.conf`, `themes/*.conf`, `plugins/*.conf`, and executable `scripts/*`.
- Displays a colored list with category, alias, and an optional description (first comment line).
- Opens the selected file in `$EDITOR` (fallback: `hx`).

Build and run:

```bash
cd hyprconf
cargo build --release
# or
cargo run -- --help
```

Examples:

```bash
# use default root (~/.config/hypr)
hyprconf

# filter to conf.d and open with nvim
hyprconf --category conf-d --editor nvim

# set an explicit root
hyprconf --root ~/.config/hypr
```

Flags worth knowing:

- `--root DIR` to override config root (defaults to `$XDG_CONFIG_HOME/hypr` or `~/.config/hypr`).
- `--category {hyprland,utility,themes,plugins,conf-d,scripts}` to pre-filter.
- `--editor CMD` to choose editor; or set `$EDITOR`.
- `--color SPEC` to set skim theme (e.g. `dark`, `light`, or a custom spec).
- `--no-seg-colors` to disable per-line segment coloring.

## Getting Started

1) Drop this directory at `~/.config/hypr` (or point `hyprconf --root` at it).
2) Review `conf.d/05-exec.conf` for autostarts and make sure the tools exist in your system:
   - `eww`, `cliphist`, `hyprpaper`, `hyprpm`, `udiskie`, `wl-paste`, and `xdg-desktop-portal-*`.
3) Update monitor names and wallpapers in `hyprpaper.conf` and `conf.d/80-monitors.conf` to match your setup.
4) Optionally toggle plugins in `plugins/` via `hyprpm` and `conf.d/91-plugins.conf`.

## Caveats and TODO

- This is a personal repo: no install script, no guarantees. Expect to customize.
- Some pieces are rough or tailored to my hardware (monitor names, fonts, themes).
- Plugins are optional; comment/uncomment `source` lines and run `hyprpm` accordingly.
- I will likely add a Nix module and bootstrap script in the future.
- PRs/issues are welcome, but this repo isn't currently intended for general use.
