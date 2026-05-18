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
