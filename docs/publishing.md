# Publishing the Flatpak repository

## Updating Horizon

**Check for Horizon releases** runs daily and can be started manually. It
uses Flatpak External Data Checker to update the launcher URL and SHA-256
checksum in `flatpak/dev.playhorizon.Launcher.yml`, and the release in
`flatpak/dev.playhorizon.Launcher.metainfo.xml`. Only `launcher@` releases
count, since the upstream repository also publishes nightly game builds.

Each upstream release gets a PR on its own branch, such as
`automation/update-horizon-0.2.64`, opened by `tbot516[bot]` with auto-merge
enabled. It requests a review from `TB516` so the PR sends a notification. An
existing open PR for that version is left alone, so fixes under review are
preserved. The PR merges itself once **Validate Flatpak update** passes, and
that merge publishes the update. If validation fails, the PR stays open.

Squash-merging a PR with several commits uses its title as the commit
message, so PR titles use the `type(scope): description` format, such as
`chore(flatpak): update Horizon to 0.2.64`.

To run the same update locally:

```sh
podman run --rm -v "$PWD:/repo:z" -w /repo ghcr.io/flathub/flatpak-external-data-checker:latest \
    --edit-only --filter-type archive flatpak/dev.playhorizon.Launcher.yml
```

For packaging fixes users will notice without a new upstream release, add an
AppStream release that appends a hyphen and package revision to the upstream
version, such as `0.2.64-1`, with a note describing the fix. Increment that
revision for each such release. Changes users won't notice, such as CI or build
tweaks, don't need a release entry.

**Validate Flatpak update** builds the package and validates the desktop entry
and AppStream metadata. It does not launch the game. Website-only changes skip
the build and do not change the build cache key.

The updater opens PRs as the `tbot516` GitHub App, so validation and
publishing start on their own. Install the app on this repository, store its
client ID as the Actions variable `BOT_CLIENT_ID` and its private key as the
Actions secret `BOT_PRIVATE_KEY`, and enable **Allow auto-merge** in the
repository settings. Make **Validate** a required status check on `main` so
auto-merge waits for it.

## First publication

The workflows target the public `TB516/horizon-flatpak` GitHub repository and
`https://horizon.thomasberrios.com`.

1. Set **Settings → Pages → Source** to **GitHub Actions**.
2. Configure `horizon.thomasberrios.com` as the Pages custom domain, with a
   DNS CNAME pointing to `tb516.github.io`, and enable HTTPS when available.
3. Add the repository signing key as described below.
4. Run **Publish Flatpak repository** with **first-publish** selected.
5. Check the website and install link.

## Subsequent publications

**Publish Flatpak repository** runs on every push to `main` that changes
`flatpak/`, and can be run manually with **first-publish** off. It restores
and verifies the published history before building and signing the new
commit. If restoration fails, publishing stops.

It then generates AppStream metadata, signs the repository summary, and
deploys the website and repository together as a Pages artifact.

The repository retains the current release and two parent commits. Users can
inspect these with `flatpak remote-info --log` and select a previous build
with `flatpak update --commit`.

The templates in `flatpak/pages/` receive the signing public key during
publishing. Changing the signing key requires a separate key migration;
**first-publish** is only for a genuinely new repository.

## Signing key

Use a dedicated GPG signing key without a passphrase for this unattended
workflow. Keep an encrypted backup in Bitwarden.

In **Settings → Secrets and variables → Actions → New repository secret**,
create `FLATPAK_GPG_PRIVATE_KEY` with the complete ASCII-armored private key
as its value. No key files belong in the repository, and local package builds
do not need the signing key.
