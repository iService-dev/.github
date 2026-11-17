# .github

Shared GitHub Actions workflows.

## `plugin-checks.yml`

Quick checks for a WordPress plugin, in one job:

1. **Plugin header** – `Requires PHP`, `Requires at least`,
   `Domain Path: /languages`, `Text Domain` equal to the plugin slug, and a
   valid `$required_base_version`.
2. **PHP syntax** – `php -l` on the plugin's `Requires PHP` version and on
   the newest PHP. Fails on syntax errors, warnings and deprecations.
3. **Build** – `npm ci`, `npm run build`, Composer, and the plugin zip
   (attached to the run for 5 days).
4. **Release pull requests** (into `main` only) – must come from `dev`, and
   the `Version` header must be exactly one patch, minor or major step above
   the latest `v*` tag.

### Usage

```yaml
name: Checks

on:
  push:
    branches: [dev]
  pull_request:
    branches: [dev, main]
  workflow_dispatch:

concurrency:
  group: checks-${{ github.ref }}
  cancel-in-progress: true

jobs:
  checks:
    uses: iService-dev/.github/.github/workflows/plugin-checks.yml@main
```

## `plugin-release.yml`

Releases the zip that `plugin-checks.yml` built in the same run: checks the
version again, pushes the tag `v<version>` and creates the GitHub release.
Nothing is committed to the repository; the version is set by hand on `dev`.

```yaml
name: Build & Release WordPress Plugin

on:
  push:
    branches: [main]

permissions:
  contents: write

jobs:
  checks:
    uses: iService-dev/.github/.github/workflows/plugin-checks.yml@main

  release:
    needs: checks
    uses: iService-dev/.github/.github/workflows/plugin-release.yml@main
```

Changes on `main` apply to every caller on its next run, so test them on
one plugin first.
