# Btrfs Snapshot Strategy

The three-root model (`~/data`, `~/dev`, `~/scratch`) separates content by
human value: irreplaceable, reproducible, disposable. On a Btrfs filesystem,
this separation maps naturally onto subvolume boundaries, which control
snapshot scope.

This document is optional. It applies only when the home directory lives on
Btrfs. On other filesystems the conceptual layout is unchanged; only the
snapshot mechanism described here does not apply.

## Why snapshots matter

External cloud backup typically covers personal data under `~/data`. It does
**not** cover the dotfile region — `~/.config`, `~/.local`, `~/.ssh`,
`~/.gnupg`, browser profiles, application state. Those files are small but
high-value: they encode years of accumulated configuration and are the most
painful thing to lose to a botched upgrade or a configuration mistake.

Local Btrfs snapshots are the right tool for that category:

- cheap (copy-on-write, only changed blocks consume space);
- fast (instant point-in-time copies);
- granular (file-level restore);
- close at hand (no network round trip).

## The snapshot-boundary principle

A snapshot is most useful when it captures a coherent slice of human state
and excludes:

- content that is regenerable from scratch (caches, build artifacts);
- content that has a remote source of truth (git repositories);
- content that is disposable by contract (`~/scratch`);
- content covered by a separate backup policy (`~/data` → cloud).

If those categories are mixed into one snapshot, the snapshot bloats with
churn and noise. Restore becomes harder because the snapshot is full of
content you do not need to recover.

In Btrfs, the unit of snapshot scope is the **subvolume**. Subvolumes nested
inside another subvolume are automatically excluded from snapshots of the
parent. That is the property used to align the snapshot boundary with the
value boundary.

## Subvolume layout

A typical Btrfs layout that matches the three-root model.

Top-level subvolume names vary by distribution: Anaconda on Fedora creates
`root`, `home`, `var` (no prefix); Arch and openSUSE installers commonly use
the `@root`, `@home` convention. The example below uses the Fedora style;
substitute `@root`/`@home` if your installer follows that convention. Nested
subvolumes have no separate name — they are identified by their path inside
the parent subvolume.

```
Btrfs pool
├── root                                → /                          (OS-managed)
├── home                                → /home (or /var/home)       ← snapshot target
│   ├── (nested) home/<user>/.cache     → ~/.cache                   excluded
│   ├── (nested) home/<user>/dev        → ~/dev                      excluded
│   └── (nested) home/<user>/scratch    → ~/scratch                  excluded
└── @data                               → ~/data                     independent
```

In `btrfs subvolume list /sysroot` (or `btrfs subvolume list <btrfs-mount>`)
the nested entries appear with `top level N`, where N is the ID of the
parent `home` subvolume. That `top level` relation is what makes Btrfs
automatically exclude them from snapshots of the parent.

Mapping to the value categories:

| Subvolume | Snapshot intent | Reason |
|---|---|---|
| `home` | primary snapshot target | dotfiles, `.config`, `.local`, credentials, browser profiles |
| `home/<user>/.cache` (nested) | none | regenerable; tens of GiB of constant churn |
| `home/<user>/dev` (nested) | none | git remote is the source of truth |
| `home/<user>/scratch` (nested) | none | disposable by contract |
| `@data` | own policy | covered by external backup; can be snapshotted independently if desired |
| `root` | never | OS rollback is the responsibility of the OS update mechanism, not Btrfs snapshots |

The user-visible paths remain unchanged. Applications see `~/.cache`,
`~/dev`, `~/scratch`, and `~/data` as ordinary directories.

## Creating the layout post-install

The installer of most distributions creates the top-level `root` and `home`
subvolumes (under whatever naming convention the installer uses) but not the
nested subvolumes or `@data`. Create them after first boot, ideally before
populating the affected paths.

### Find the Btrfs device

```bash
lsblk -f
sudo btrfs filesystem show
findmnt /home
```

### Create the @data subvolume (top-level)

Mount the Btrfs root (`subvolid=5`) temporarily:

```bash
sudo mkdir -p /mnt/btrfs-root
sudo mount -o subvolid=5 /dev/mapper/<device> /mnt/btrfs-root
sudo btrfs subvolume create /mnt/btrfs-root/@data
sudo umount /mnt/btrfs-root
```

Add an `/etc/fstab` entry to mount it at `~/data`:

```
UUID=<btrfs-uuid>  /home/<user>/data  btrfs  subvol=@data,compress=zstd:1,relatime  0 0
```

### Create the nested subvolumes inside `home`

Nested subvolumes are created as paths inside the parent's normal mount
point. They use the same Btrfs pool but are tracked independently and are
excluded from parent snapshots.

```bash
sudo rmdir /home/<user>/.cache 2>/dev/null || true
sudo -u <user> btrfs subvolume create /home/<user>/.cache
sudo -u <user> btrfs subvolume create /home/<user>/dev
sudo -u <user> btrfs subvolume create /home/<user>/scratch
```

If `~/.cache` already contains data, move it aside first and restore after:

```bash
sudo mv /home/<user>/.cache /home/<user>/.cache.old
sudo -u <user> btrfs subvolume create /home/<user>/.cache
sudo -u <user> rsync -a /home/<user>/.cache.old/ /home/<user>/.cache/
sudo rm -rf /home/<user>/.cache.old
```

Nested subvolumes do not need fstab entries — Btrfs auto-mounts them as part
of the parent.

### Verify

```bash
sudo btrfs subvolume list /sysroot   # or /home — never / on composefs overlays
findmnt /home/<user>/.cache
findmnt /home/<user>/dev
findmnt /home/<user>/scratch
findmnt /home/<user>/data
```

The `btrfs subvolume list` output should show the nested subvolumes with
`top level` equal to the ID of `home`.

## Taking and restoring snapshots

Take a read-only snapshot of the dotfile region:

```bash
sudo btrfs subvolume snapshot -r /home /home/.snapshots/$(date +%Y%m%d)
```

The snapshot will contain `.config`, `.local`, `.ssh`, `.gnupg`, browser
profiles, etc. It will not contain `.cache`, `dev`, `scratch`, or `data` —
those are separate subvolumes.

Restore a single file or tree:

```bash
cp /home/.snapshots/2026-01-15/<user>/.config/dconf/user \
   ~/.config/dconf/user

rsync -a --delete /home/.snapshots/2026-01-15/<user>/.config/ ~/.config/
```

## Relationship to other persistence layers

Snapshots are one of several mechanisms. They do not replace any of the
others.

| Layer | Tool | Scope | Protects against |
|---|---|---|---|
| OS rollback | distro-specific (rpm-ostree, Snapper, etc.) | `root` | bad system update |
| Dotfile snapshots | Btrfs snapshot of `home` | dotfiles + app state | bad config change, bad app upgrade |
| Personal data backup | external (cloud or NAS) | `@data` | disk failure, theft, ransomware |
| Code | git remote | `home/<user>/dev` content | local disk loss |
| Caches | none | `home/<user>/.cache` | nothing — regenerable |

A bad system update is recovered by the OS update tool. A botched dotfile
change is recovered from a Btrfs snapshot. A lost disk is recovered from
external backup and `git clone`. Each mechanism has a clear job and the
subvolume layout keeps them from interfering with each other.

## Caveats

- **Snapshots are not backups.** A snapshot lives on the same disk as the
  original. A disk failure takes both. Snapshots protect against logical
  mistakes; backups protect against physical loss. Both are needed.
- **SQLite CoW fragmentation.** SQLite databases under `~/.config` (Firefox
  `places.sqlite`, etc.) suffer from Btrfs CoW write amplification over
  time. Mitigation: `chattr +C` on affected files after recreating them
  empty.
- **Subvolume creation order.** Nested subvolumes must be created before
  the path is populated; an existing directory blocks
  `btrfs subvolume create`. For an existing populated path, rename, recreate
  as subvolume, rsync content back.
- **Retention policy is separate.** This document defines the snapshot
  *boundary*, not the *retention policy*. A retention policy (Snapper,
  systemd timers, or manual) can be added later without changing the
  layout.
