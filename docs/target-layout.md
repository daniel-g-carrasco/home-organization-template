# Target Layout

All folder names are lowercase, English, and script-friendly.

```text
~/data/
  inbox/
    00-review/
    10-downloads/
    20-scans/
    30-mobile-imports/
    99-migration-conflicts/
  personal/
    admin/
      identity/
      taxes/
      health/
      residence/
      legal/
      social-security/
      insurance/
      vehicles/
      banking/
      telecom/
      transport/
      travel/
      career/
      education/
      warranties/
      purchases/
      scans/
    finance/
    health-fitness/
    people/
      family/
      friends/
    places/
    records/
  work/
    admin/
    clients/
    projects/
    reference/
    archive/
  community/
  media/
    photos/
      raw/
      albums/
      exports/
      camera-roll/
    audio/
      music-projects/
    video/
      projects/
      exports/
    recordings/
  library/
    books/
      managed-library/
      inbox/
      manuals/
      periodicals/
    fonts/
      families/
      archives/
      license-review/
    software/
      installers/
      packages/
    games/
  technology/
    00-admin/
      licenses/
        proof-of-purchase/
        raw-secrets/
      security/
    10-devices/
    20-network/
    30-servers/
    40-services/
    50-reference/
    90-archive/
  projects/
  shared/
    10-outgoing/
    20-published/
    90-archive/
  archive/
    personal/
    work/
    education/
    family/
  imports/
  templates/

~/dev/
  project-repositories/

~/scratch/
  downloads/
  mobile/
    phone/
    tablet/
  media-cache/
```

## XDG User Directories

Suggested mapping:

```text
XDG_DOWNLOAD_DIR     -> ~/data/inbox/10-downloads
XDG_DOCUMENTS_DIR    -> ~/data/personal
XDG_PICTURES_DIR     -> ~/data/media/photos
XDG_MUSIC_DIR        -> ~/data/media/audio
XDG_VIDEOS_DIR       -> ~/data/media/video
XDG_TEMPLATES_DIR    -> ~/data/templates
XDG_PUBLICSHARE_DIR  -> ~/data/shared
```

Keep the desktop empty or minimal. A desktop folder should not become a storage
system.

## Mobile Devices

Use `scratch/mobile` for transient device exchange. Use `data/inbox` when a
file imported from a device may be worth keeping.

```text
scratch/mobile/phone/
scratch/mobile/tablet/
data/inbox/30-mobile-imports/
```

## Technology

Technology is for ownership, operation, and understanding of technical systems.

Use:

```text
technology/00-admin/      licenses, security exports, recovery material
technology/10-devices/    device notes and diagnostics
technology/20-network/    routers, VPNs, and network configuration
technology/30-servers/    server hosts and service stacks
technology/40-services/   external provider records
technology/50-reference/  reusable technical notes
technology/90-archive/    retired technology contexts
```

Reusable installers belong in `library/software`, not in `technology`.

Raw secrets should not be kept as plain files long term. If they must exist as
files temporarily, isolate them under `technology/00-admin/security` or
`technology/00-admin/licenses/raw-secrets` and exclude those paths from normal
cloud sync until the backup model is intentional.
