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

## Related

- Org-level equivalent: [`gitpush-mod/.github`](https://github.com/gitpush-mod/.github) (which hosts mod-repo profile + community health + org-wide auto-archive)
- Personal profile: [`Godimas101/Godimas101`](https://github.com/Godimas101/Godimas101)
