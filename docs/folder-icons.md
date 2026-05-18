# Folder Icons

GNOME/Nautilus can show custom icons for normal folders through GIO metadata.
This is useful when a structured home layout has important roots such as
`data`, `projects`, `dev`, and `scratch`.

## Model

`scripts/configure-folder-icons` generates local SVG folder icons under:

```text
~/.local/share/icons/home-organization/
```

Then it assigns them with:

```bash
gio set -t string <folder> metadata::custom-icon file:///path/to/icon.svg
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

## Commands

Preview:

```bash
scripts/configure-folder-icons
```

Apply:

```bash
scripts/configure-folder-icons --apply
```

Clear custom icons:

```bash
scripts/configure-folder-icons --clear --apply
```

## Notes

- Icons are local SVG files, so they do not depend on the active icon theme.
- Nautilus may need to be reopened after metadata changes.
- GIO metadata is per-user state; this is why the script exists instead of
  relying only on files committed to the repository.
