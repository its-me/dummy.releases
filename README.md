# dummy.releases

A dummy repository used to exercise fully automated, calendar-driven
semantic versioning with GitHub Actions. It has no application code —
its only purpose is to keep producing commits and releases on a fixed
schedule, following [SemVer](https://semver.org/) (`MAJOR.MINOR.PATCH`).

## How it works

The repository was seeded with an initial `v0.0.0` release. From there,
four scheduled workflows keep it moving forward:

| Workflow | Schedule (UTC) | Effect |
|---|---|---|
| [`daily-commit.yaml`](.github/workflows/daily-commit.yaml) | `0 0 * * *` — daily at 00:00 | Appends a UTC timestamp to `activity.log` and pushes a commit, so the repository never goes stale. |
| [`weekly-patch-release.yaml`](.github/workflows/weekly-patch-release.yaml) | `0 0 * * 0` — every Sunday at 00:00 | Reads the latest release tag and creates a new one with the **patch** number incremented (`vX.Y.Z` → `vX.Y.(Z+1)`). |
| [`monthly-minor-release.yaml`](.github/workflows/monthly-minor-release.yaml) | `0 0 1 * *` — 1st of the month at 00:00 | Increments the **minor** version and resets patch to `0` (`vX.Y.Z` → `vX.(Y+1).0`). |
| [`yearly-major-release.yaml`](.github/workflows/yearly-major-release.yaml) | `0 0 1 1 *` — January 1st at 00:00 | Increments the **major** version and resets minor/patch to `0` (`vX.Y.Z` → `v(X+1).0.0`). |

Each schedule fires at the start of its period, so on January 1st (when
it falls on a Sunday) the daily, weekly, monthly, and yearly workflows
can all run together — each is independent and simply reads the latest
release at the time it runs. All four can also be triggered manually
via `workflow_dispatch`, and all run on the lightweight `ubuntu-slim`
runner.

Version bumps are computed at runtime with `gh release view` and basic
shell arithmetic — there's no versioning tool or state file beyond the
GitHub Releases list itself.

## License

[MIT](LICENSE)
