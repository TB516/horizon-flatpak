# Building the Flatpak

The manifest repackages the official, checksum-pinned Horizon launcher
tarball for x86_64, with the upstream desktop entry and icon.
`HORIZON_LAUNCHER_NO_SELF_UPDATE` turns off the launcher's updater, since
`/app` is read-only and Flatpak owns the launcher. The game versions the
launcher installs still download into the app's data folder, the same way
Prism Launcher downloads Minecraft versions.

The host needs Flatpak, Flatpak Builder, and the per-user
`org.freedesktop.Platform//25.08` and `org.freedesktop.Sdk//25.08`. The build
script does not install or update those dependencies.

```sh
./scripts/build-flatpak build
```

This writes `build-flatpak/dev.playhorizon.Launcher.flatpak`. To build and
install for your user, then launch:

```sh
./scripts/build-flatpak install
./scripts/build-flatpak run
```

The `run` command forwards additional arguments to the launcher. Build output
and cache stay in `build-flatpak/`.

## Updating Horizon

The launcher source carries `x-checker-data` for Flatpak External Data
Checker, which updates the URL and SHA-256 in the manifest and adds the
release to the AppStream metadata:

```sh
podman run --rm -v "$PWD:/repo:z" -w /repo ghcr.io/flathub/flatpak-external-data-checker:latest \
    --edit-only flatpak/dev.playhorizon.Launcher.yml
```

For packaging fixes without a new upstream release, add an AppStream release
that appends a hyphen and package revision to the upstream version, such as
`0.2.64-1`.

## Releasing

Run **Release Flatpak bundle** from the Actions tab after committing an
update. It builds the bundle and attaches it to a GitHub release named after
the newest version in the AppStream metadata, replacing the bundle if that
release already exists.
