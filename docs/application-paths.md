# Application Paths

After changing the home folder layout, XDG user directories are only the first
layer. Some applications keep their own download, import, export, recent, media,
or library paths.

## Recommended Order

1. Create the folder skeleton.
2. Configure XDG user directories and GTK bookmarks.
3. Restart the file manager.
4. Audit application configs.
5. Update applications that keep their own paths.

Commands:

```bash
scripts/bootstrap-layout
scripts/configure-xdg-user-dirs --apply
nautilus -q
scripts/audit-application-paths
```

## XDG And GTK

The expected mapping is:

```text
Downloads -> ~/scratch/downloads
Documents -> ~/data/personal
Pictures  -> ~/data/media/photos
Music     -> ~/data/media/audio
Videos    -> ~/data/media/video
Templates -> ~/data/templates
Public    -> ~/data/shared
Projects  -> ~/data/projects
```

GTK bookmarks are updated for file chooser sidebars and Nautilus.

## Browsers

Firefox, Floorp, LibreWolf, Waterfox, and similar Gecko browsers may keep
download paths inside profile `prefs.js`. Prefer creating a `user.js` file in
the profile instead of editing `prefs.js` directly.

Example:

```js
user_pref("browser.download.folderList", 2);
user_pref("browser.download.dir", "/home/USER/scratch/downloads");
user_pref("browser.download.lastDir", "/home/USER/scratch/downloads");
```

Chromium-based browsers can usually be changed from:

```text
Settings -> Downloads -> Location
```

Use `~/scratch/downloads` as the default. Enable "Ask where to save each
file" only if downloads are often domain-specific.

## Mail Clients

Mail clients often have a separate "save attachment" directory.

For Thunderbird-style clients:

```js
user_pref("messenger.save.dir", "/home/USER/scratch/downloads");
user_pref("browser.download.dir", "/home/USER/scratch/downloads");
```

## Photo And Media Applications

Photo tools can keep paths in both preferences and databases.

For darktable:

- `darktablerc` stores recent import/export locations;
- `library.db` stores imported film roll folders.

Only edit the database while darktable is closed, and always make a backup:

```bash
cp ~/.config/darktable/library.db ~/.config/darktable/library.db.bak-$(date +%Y%m%d-%H%M%S)
```

Then use darktable's own UI or a careful SQLite migration to update old folder
roots.

GIMP and similar GTK apps often follow GTK bookmarks and the file chooser, but
recent files can still point to old locations until cleared.

## Download Clients

Download clients should distinguish complete and incomplete files:

```text
complete downloads   -> ~/scratch/downloads
incomplete downloads -> ~/scratch/downloads
```

This keeps half-written files out of durable data and backup scope.

## Office Suites And Media Players

LibreOffice, VLC, and similar applications often keep recent-document history.
Those stale paths are usually harmless but confusing. Clear recent files from
the UI when needed.

## Flatpak

Flatpak apps with `xdg-download`, `xdg-music`, `xdg-pictures`, or similar
filesystem permissions follow the XDG mapping. Apps with broad `home` access
may still remember their own recent paths and should be audited separately.
