# Godimas101 / .github

Personal-account `.github` repo for [Godimas101](https://github.com/Godimas101). Hosts:

- **Shared workflows** — cron jobs and infrastructure that operate across
  Chris's personal repos and boards.
- **Community health defaults** — issue templates, `CONTRIBUTING.md`, etc.
  fall back to whatever's here for any personal repo that doesn't override.

## Workflows

| File | What it does | Trigger |
|---|---|---|
| `.github/workflows/auto-archive-personal-projects.yml` | Moves Done items > 30 days old to Archived on the [Personal Projects board](https://github.com/users/Godimas101/projects/2) | Weekly cron, Sun 05:00 UTC |

## Secrets required

- **`PERSONAL_PROJECT_TOKEN`** — fine-grained PAT with User → Projects (Read+Write) permission on `Godimas101`. Set at repo scope:

```powershell
gh secret set PERSONAL_PROJECT_TOKEN -R Godimas101/.github
```

## Release + changelog automation (desktop-tool repos)

The desktop-tool repos — `gdc-sound`, `universal-audio-converter`,
`universal-image-converter` (and future ones) — share two automations.

### Auto-release — `.github/workflows/release.yml` (per repo)

Push to `main` → if `VERSION` names a tag that doesn't exist yet, a Windows
runner builds the PyInstaller `.exe` and publishes a GitHub Release with it
attached. Release notes come from the matching `## [x.y.z]` section of
`CHANGELOG.md`; a headless `--check` smoke run confirms the packaged exe
constructs before publishing.

**To cut a release:** bump `VERSION`, add a `## [x.y.z]` section to
`CHANGELOG.md`, commit, push. Done.

Gotchas:
- `*.spec` must be **tracked** (not gitignored) so the runner can build it.
- Bundle runtime deps in the spec (e.g. `sounddevice` for the audio app, `PIL`
  for the image app) or they won't work in the packaged exe. Drop unused
  `collect_all(...)` calls — they fail the CI build if the package isn't installed.

### Auto-changelog on issue close — `.github/workflows/on-issue-close.yml` (per repo)

Closing an issue as *completed* calls the shared reusable workflow
`gitpush-mod/.github/.github/workflows/append-changelog.yml`, which appends a
`- <title> (#N) — closed <date> by @<user>` line under `## [Unreleased]` and
pushes it.

**Requirements (each one caused a failure until fixed):**
- The caller **must** set `permissions: contents: write`. A called (reusable)
  workflow can't escalate above its caller, and repos default the token to
  read-only — so without this the call dies at **startup** (`startup_failure`).
- `MOD_PROJECT_TOKEN` is a **gitpush-mod org** secret, so it's unavailable to
  **Godimas101** personal repos; those fall back to `GITHUB_TOKEN` (hence the
  `contents: write` requirement above).
- `GITHUB_TOKEN` can't push to a **protected** `main` (the bot isn't on the
  bypass list, though repo admins are). These solo tool repos keep `main`
  **unprotected** so the bot can push directly.
- Give `CHANGELOG.md` an `## [Unreleased]` section. The reusable workflow will
  create one before the first release section if it's missing, and its push is
  retry/rebase-safe against simultaneous closes.

## Related

- Org-level equivalent: [`gitpush-mod/.github`](https://github.com/gitpush-mod/.github) (which hosts mod-repo profile + community health + org-wide auto-archive)
- Personal profile: [`Godimas101/Godimas101`](https://github.com/Godimas101/Godimas101)
