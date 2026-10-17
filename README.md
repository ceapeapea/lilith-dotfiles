# lilith dotfiles

My rice for [lilith](https://github.com/ohto121/lilith) — a wlroots-based
Wayland compositor with free-floating windows on an endless zoomable canvas
and a built-in status bar.

![shot](https://github.com/ohto121/lilith/raw/rice/screenshots/red.png)

Everything is coloured by **pywal16** from the current wallpaper: compositor
borders, the built-in bar, foot, GTK3/4, Qt6 (qt6ct), dunst.

## What's inside

```
bin/            session + helper scripts (lilith-session, wal-*, wmenu-apps, ...)
lilith/         config.h + config/ (the rice branch) and rice.patch
config/foot/    foot terminal
config/wmenu/   launcher app filter (wmenu-apps)
config/wal-templates/  pywal16 templates — the source of all colours
config/gtk-3.0, gtk-4.0/  settings.ini (Adwaita + prefer-dark)
config/qt6ct/   Qt6 theming (Fusion + pywal scheme)
```

The interesting part of the compositor config is `lilith/config/`:
appearance (borders from the pywal palette, no focus highlight), keybinds
(helium, wmenu-apps, yazi, lock, screenshots, lilith-volume with a dunst OSD),
and the built-in bar: bottom, transparent, Gallant font, `lilith HH:MM` left,
tags in the middle, battery + volume on the right.

## Install (Arch/Artix)

Packages:

```
base-devel wlroots0.19 libinput libxkbcommon libdrm fcft pixman \
xorg-server-xwayland xcb-util-wm  # build
pywal16 foot wmenu dunst swaybg swayidle swaylock cliphist wl-clipboard \
grim slurp brightnessctl polkit-gnome qt6ct ttf-nerd-fonts-symbols  # runtime
```

The font used everywhere is **Gallant Raster Term** (put the .ttf in
`~/.local/share/fonts`), with **Symbols Nerd Font** as the icon fallback.

1. Compositor:

```
git clone https://github.com/ohto121/lilith && cd lilith
git checkout rice          # rice branch = this rice compiled in
make
```

2. Dotfiles:

```
cp -r bin   ~/             # scripts to ~/.local/bin (mkdir -p if needed)
cp -r config/foot config/wmenu config/gtk-3.0 config/gtk-4.0 config/qt6ct ~/.config/
cp -r config/wal-templates ~/.config/wal/templates
```

3. Pick a wallpaper and generate everything:

```
wal-lilith ~/Pictures/wallpapers/your-image.png
```

4. Start the session from a TTY: `~/.local/bin/lilith` (symlink to
`lilith-session`, which rebuilds lilith if the palette changed, then applies
GTK/Qt theming and autostarts bar, dunst, swayidle, clipboard watcher, etc.).

## How the palette plumbing works

- `wal-lilith <image>` runs pywal16 on the image → `~/.cache/wal/lilith-colors.h`
  (from `config/wal-templates/lilith-colors.h`), sets the wallpaper via swaybg,
  copies the header into the lilith source tree and rebuilds.
- `wal-gtk` / `wal-qt` / `wal-yambar` recolour GTK, Qt (qt6ct) and yambar
  from the same palette at every login and wallpaper change.
- Restart lilith after a palette change and everything switches colour.
