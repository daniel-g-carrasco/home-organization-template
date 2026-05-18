# Backup Policy

A clean folder layout should make backup policy obvious.

## Back Up By Default

Back up:

- `data/personal`;
- `data/work`;
- `data/community`;
- `data/library`;
- `data/projects`;
- `data/shared`;
- `data/archive`;
- `data/templates`.

## Treat Carefully

Review before normal cloud sync:

- raw photo archives;
- large media project source files;
- historical imports;
- plain-text secrets;
- call recordings or other sensitive recordings;
- private keys and recovery codes.

## Usually Exclude

Usually exclude:

- `scratch`;
- caches;
- generated build artifacts;
- temporary downloads;
- application render caches;
- package-manager caches;
- virtual environments and dependency folders.

See `policies/backup-exclude.example`.
