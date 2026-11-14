# Horizon Flatpak

An unofficial Flatpak package for the [Horizon](https://playhorizon.dev)
launcher on x86_64 Linux. Horizon is a rewrite of Minecraft: Java Edition
26.3 in Rust. You need a Microsoft account that owns Java Edition.

Neither Horizon nor this package is approved by or associated with Mojang or
Microsoft.

## Install

Download `dev.playhorizon.Launcher.flatpak` from the
[latest release](https://github.com/TB516/horizon-flatpak/releases/latest)
and open it with your software store, or install it from a terminal:

```sh
flatpak install --user dev.playhorizon.Launcher.flatpak
```

Launch Horizon from your app menu or with `flatpak run dev.playhorizon.Launcher`.
To update, install a newer release the same way.

## What it can access

Horizon runs sandboxed. It can use the network, your display, graphics card
and audio, but not your files. The launcher's own updater is turned off, so
it only changes when you install a new release. The game versions it
installs, Minecraft's files, worlds and settings all live under
`~/.var/app/dev.playhorizon.Launcher/`.

If it can't find an existing Minecraft install, Horizon downloads Minecraft
26.3 with your account. To reuse an install from another launcher instead,
give Horizon read access to it, for example for the Prism Launcher Flatpak:

```sh
flatpak override --user --filesystem=~/.var/app/org.prismlauncher.PrismLauncher:ro dev.playhorizon.Launcher
```

Your Microsoft sign-in is saved in your desktop's keyring.

## Uninstall

```sh
flatpak uninstall --delete-data dev.playhorizon.Launcher
```

Without `--delete-data`, your worlds and downloads stay in
`~/.var/app/dev.playhorizon.Launcher/`. The saved sign-in stays in your
keyring until you remove it there.

For local builds and updating the package, see [Building](docs/building.md).
