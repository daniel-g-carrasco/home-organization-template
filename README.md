# Home Organization Template

A portable, script-friendly home folder model for Linux workstations and
personal devices.

The goal is to separate valuable data, source code, and temporary work so the
same structure can be reproduced across machines without relying on memory or
one-off cleanup decisions.

## Core Model

```text
~/data/      valuable personal, professional, media, and reference files
~/dev/       source repositories and development workspaces
~/scratch/   temporary, generated, rebuildable, or disposable work
```

Keep normal Linux application state in the standard dot directories unless you
have an intentional reason to move it. This template is about human-managed files,
not replacing `.config`, `.local`, or `.cache`.

## Quick Start

Preview the folder skeleton:

```bash
scripts/bootstrap-layout --dry-run
```

Create the folder skeleton:

```bash
scripts/bootstrap-layout
```

Configure XDG user directories and file-manager bookmarks:

```bash
scripts/configure-xdg-user-dirs --apply
nautilus -q
```

Preview folder guide files:

```bash
scripts/install-folder-readmes
```

Install folder guide files:

```bash
scripts/install-folder-readmes --apply
```

Audit application configs for stale paths:

```bash
scripts/audit-application-paths
```

## Repository Map

- `docs/principles.md`: rules and tradeoffs behind the layout.
- `docs/target-layout.md`: full folder tree and XDG mapping guidance.
- `docs/migration-workflow.md`: conservative migration process.
- `docs/folder-guides.md`: local `00-readme.md` policy.
- `docs/backup-policy.md`: backup and sync boundaries.
- `docs/application-paths.md`: XDG, file-manager, browser, mail, media, and
  app-specific path integration.
- `docs/folder-icons.md`: Nautilus/GIO folder icons resolved from the active
  icon theme for the organized home layout.
- `docs/btrfs-snapshot-strategy.md`: optional Btrfs subvolume layout that
  aligns the three-root model with snapshot scope (Btrfs-only).
- `policies/backup-exclude.example`: example exclusion file for backup tools.
- `policies/naming.md`: naming conventions.
- `templates/folder-readme.md`: template for folder-local guide files.
- `scripts/bootstrap-layout`: creates the target skeleton.
- `scripts/install-folder-readmes`: creates concise `00-readme.md` guides.
- `scripts/configure-xdg-user-dirs`: points XDG user directories and GTK
  bookmarks to the organized layout.
- `scripts/audit-application-paths`: reports application config files that
  still reference old home paths.
- `scripts/configure-folder-icons`: assigns theme-provided folder icons to the
  primary organized folders.

## Safety Rule

Do not begin by deleting or moving everything. Create the target skeleton,
migrate one domain at a time, verify backups, and only then remove old sources.
