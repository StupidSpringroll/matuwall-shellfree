# matuwall-shellfree

Install: `git clone https://github.com/StupidSpringroll/matuwall-shellfree.git && cd matuwall-shellfree && makepkg -si`

Fork of [Matuwall](https://github.com/naurissteins/Matuwall), frozen at the last
Python (GTK4/libadwaita) release **0.1.6** and diverged. Upstream rewrote the
app as a native C tool in 0.2.x (apply backends only `awww`/`sweetbg`), which
made these behaviors impossible, so this package pins the old architecture and
carries the fork as a patch.

## Divergence vs upstream 0.1.6 (3 files)

| File | Change |
|---|---|
| `matuwall/app.py` | `_backdrop_opacity = 0.02` instead of `1.0` — the click-catcher scrim is rendered ~invisible at the compositor (`niri` layer rule multiplies it to 0), while still swallowing presses |
| `matuwall/ui/content.py` | wallpaper applies **instantly**: spawn `matuwall-swaybg <path>` first, then run matugen in the background. Upstream ran the full palette pass before the wallpaper swapped, leaving seconds of lag |
| `matuwall/ui/panel.py` | scrim sized to the **largest monitor** (not GTK "primary"), so it covers the 4K output instead of a 1536px slab |

Upstream behaviors kept: backdrop click-away close, panel mode.

## Runtime requirements (not packaged — they are desktop config)

The fork is useless without these three companions:

1. **`matuwall-swaybg` wrapper in `$PATH`** — kills the previous swaybg 0.5s
   after the new one is up (bounded swap, no grey gap), and downscales >24MP
   images with `vips` first. Example at
   `~/Projects/shellfree.tar.gz` (shellfree dotfiles backup).
2. **Compositor scrim rule** (`niri`): match namespace `^matuwall-backdrop$`,
   opacity 0.0.
3. **matugen `[config.wallpaper] set = false`** so matugen never races the
   wrapper for the wallpaper.

## Why not upstream 0.2.x

The 0.2.x C rewrite cannot express this fork: its `on_apply` hooks run *after*
the backend applies the wallpaper (no "instant swap then palette"), and its
backends are hardcoded to `awww`/`sweetbg` (no custom swaybg backend, no scrim,
no click-away backdrop).

## Maintainer notes

- Bump `pkgrel` for fork-only fixes. Never bump `pkgver` past 0.1.6 unless you
  are porting the fork to a new upstream release (see `prepare()` — the patch
  is applied on top of the untouched upstream source).
- Keep `shellfree.patch` minimal: regenerate from
  `diff -u <upstream>/... <fork>/...` per file.