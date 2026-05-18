# Folder Guides

Important folders can contain a small `00-readme.md` file.

Use these files to answer:

- what belongs here;
- what does not belong here;
- how to name or group files;
- how to find things later.

Do not put a guide in every small folder. Use them where a folder is a decision
point.

Install managed guides with:

```bash
scripts/install-folder-readmes --apply
```

Update only managed guides with:

```bash
scripts/install-folder-readmes --apply --update-managed
```

The installer will not overwrite an unmanaged or locally edited file unless it
contains its management marker and `--update-managed` is provided.
