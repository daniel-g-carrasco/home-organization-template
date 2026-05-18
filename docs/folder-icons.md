# Folder Icons

GNOME/Nautilus can show custom icons for normal folders through GIO metadata.
This is useful when a structured home layout has important roots such as
`data`, `projects`, `dev`, and `scratch`.

## Model

`scripts/configure-folder-icons` does not generate artwork. It resolves existing
`folder-*` icons from the active icon theme, then from inherited themes and
fallback themes such as `MoreWaita` and `Adwaita`.

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

The root `~/data` intentionally uses the documents folder icon from the active
theme. This gives it a visible symbol while keeping the folder yellow and
neutral enough for a root container.

`~/data/community` uses the globe folder icon when available. This keeps it
distinct from `~/data/shared`, which uses the public-share folder icon.

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
