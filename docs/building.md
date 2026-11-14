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
