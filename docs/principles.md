# Principles

## Separate Domains From Storage Mechanics

Folders should describe why a file exists, not merely its extension or the app
that created it.

Good durable domains:

- `personal`
- `work`
- `media`
- `library`
- `technology`
- `community`
- `projects`
- `archive`

Weak top-level domains:

- `pdf`
- `misc`
- `new`
- `old stuff`
- `desktop backup`

## Use Three Home Roots

Use a small number of stable roots:

```text
~/data/      durable files
~/dev/       source repositories
~/scratch/   temporary and rebuildable work
```

This keeps backup policy, search behavior, and device migration easier to
reason about.

## Number Only Workflow Folders

Do not number everything. Numbering is useful when it expresses lifecycle or
priority.

Recommended prefixes:

```text
00  admin, setup, inbox, review
10  primary active work
20  secondary active work or shares
30  reference
40  production assets
50  deliverables or releases
90  archive
99  quarantine or conflicts
```

Domain folders such as `personal`, `work`, `media`, and `technology` should
stay unnumbered.

## Archive In Context First

Prefer contextual archives:

```text
data/work/clients/example-client/90-archive/
data/technology/90-archive/retired-device/
```

Use the global `data/archive` for whole historical domains or old life contexts
that no longer fit an active domain.

## Keep Inboxes Small

An inbox is temporary. It should make uncertainty visible, not hide unfinished
organization forever.

Review inbox folders periodically and move files into their final domain.

## Snapshot Boundary Follows Value

The three roots already separate content by human value: irreplaceable,
reproducible, disposable. On filesystems that support cheap point-in-time
snapshots (Btrfs, ZFS), this separation should map onto the snapshot
boundary itself.

A snapshot of `~` is most useful when it captures only what is small,
slowly-changing, and not protected by another mechanism — typically the
dotfile region (`~/.config`, `~/.local`, `~/.ssh`, browser profiles).
Bulk content with its own protection should be excluded from that scope:

- `~/.cache` — regenerable, no value;
- `~/dev` — git remote is the source of truth;
- `~/scratch` — disposable by contract;
- `~/data` — covered by external backup or its own snapshot policy.

On Btrfs this is realized by making each of those a separate (possibly
nested) subvolume. See [btrfs-snapshot-strategy.md](btrfs-snapshot-strategy.md)
for the concrete layout. On filesystems without subvolumes the same
principle applies through other tools (per-directory backup, exclude lists)
and the layout itself does not change.
