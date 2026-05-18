# Migration Workflow

## 1. Inventory First

Before moving files, create an inventory with counts and sizes.

Useful commands:

```bash
du -h --max-depth=1 "$HOME" | sort -h
find "$HOME" -maxdepth 1 -mindepth 1 -printf '%f\n' | sort
```

Inspect one overloaded folder at a time. Do not treat the whole home directory
as one migration.

## 2. Create The Target Skeleton

```bash
scripts/bootstrap-layout
scripts/install-folder-readmes --apply
```

## 3. Migrate By Domain

Move files by meaning:

- personal official records;
- work administration;
- work projects;
- photos and media;
- technology records;
- reusable libraries;
- old archives;
- imports and backups.

Prefer copy-first migration when the source is large or unclear:

```bash
rsync -a --dry-run source/ target/
rsync -a source/ target/
```

Use move operations only after you have a snapshot or a verified backup.

## 4. Quarantine Conflicts

If a destination already exists, do not overwrite blindly. Move conflicts to:

```text
data/inbox/99-migration-conflicts/
```

Then inspect them manually.

## 5. Delete Last

Deletion should be a separate pass after:

- the target folder has been reviewed;
- backup/sync policy is correct;
- snapshots or backups exist;
- duplicate files have been verified.
