# Folder Icons

GNOME/Nautilus can show custom icons for normal folders through GIO metadata.
This is useful when a structured home layout has important roots such as
`data`, `projects`, `dev`, and `scratch`.

## Model

`scripts/configure-folder-icons` does not generate artwork. It resolves existing
`folder-*` icons from the active icon theme, then from inherited themes and
fallback themes such as `Adwaita-yellow`, `MoreWaita`, and `Adwaita`.

It assigns the resolved icon files with:

```bash
gio set -t string <folder> metadata::custom-icon file:///path/to/theme-icon.svg
```

The script covers:

```text
~/data
~/data/inbox
~/data/personal
~/data/work
~/data/community
~/data/media
~/data/library
~/data/technology
~/data/projects
~/data/shared
~/data/archive
~/data/imports
~/data/templates
~/dev
~/scratch
```

It also covers stable second-level folders where the theme has a clear matching
symbol: downloads, scans, finance, places, media types, books, games, software,
technology subdomains, shared workflow folders, and archives. It intentionally
does not assign icons to date/event folders or individual project folders.

The root `~/data` intentionally uses the earth folder icon from the active
theme. This gives the durable data root a visible symbol while keeping it in the
same folder style as the rest of the theme.

`~/data/community` uses the globe folder icon when available. This keeps it
distinct from `~/data/shared`, which uses the public-share folder icon.

Technology subfolders avoid `folder-cloud` and `folder-docker` as first choices
because those names can resolve to blue MoreWaita folders in some theme stacks.
Yellow theme-consistent alternatives are preferred instead.

Inbox review folders avoid `folder-recent` for the same reason. `folder-drag-accept`
keeps the review/incoming meaning while staying in the yellow theme family.

## Commands

Preview:

```bash
scripts/configure-folder-icons
```

Apply:

```bash
scripts/configure-folder-icons --apply
```

Apply with an explicit theme:

```bash
scripts/configure-folder-icons --theme MoreWaita --apply
```

Inspect available folder icons from the active local theme:

```bash
find -L ~/.local/share/icons/Margine-Adwaita/scalable/places \
  -maxdepth 1 -type f -name 'folder*.svg' -printf '%f\n' | sort
```

Check where a themed icon really comes from:

```bash
readlink -f ~/.local/share/icons/Margine-Adwaita/scalable/places/folder-earth.svg
```

Clear custom icons:

```bash
scripts/configure-folder-icons --clear --apply
```

## Notes

- Icons come from the active theme when possible, so the folder shape and symbol
  style remain coherent with the desktop.
- `metadata::custom-icon-name` is not accepted by `gio` on this system, so the
  script stores a file URI to the resolved theme icon.
- Nautilus may need to be reopened after metadata changes.
- GIO metadata is per-user state; this is why the script exists instead of
  relying only on files committed to the repository.
