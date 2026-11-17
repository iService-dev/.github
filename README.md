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

Changes on `main` apply to every caller on its next run, so test them on
one plugin first.
