# Workflow

When making a project, setting up an automatic build/test workflow is a must, and it should be easily extendable for publishing the build.

I'm very picky about the beauty of code and CI/CD workflow (and probably many other things), and it stings my eyes and gets stuck in the
corner of my mind if it's not clean. So this is a `standard` or call it a `checklist` that I want to follow for the CI/CD setup.


## Versioning

- Use semantic versioning format: `MAJOR.MINOR.PATCH`
  - `PATCH` for bug fixes, code formatting
  - `MINOR` for backward-compatible feature additions
  - `MAJOR` for breaking changes or major changes

- Append a `BUILD` identifier like `_dev`, `_beta`, or `_rc` to represent development stages

- Tag each version release in git as: `vMAJOR.MINOR.PATCH_BUILD`
- Include the version and tag in build artifacts (e.g., `my-app-v1.2.3_dev.zip`)

- The CI/CD should:
  - Auto-fetch the version from a project version file (like `version.txt`, `package.json`, etc.)
  - Generate a draft GitHub release if no existing tag for the current version
  - Skip if version tag already exists (to avoid duplicate releases)
  - Auto-generate changelogs using conventional commit messages or PR descriptions


## Workflow Steps (Standard for Every Project)

1. **Trigger Events**
   - Pull Request to main branch (build, tests)
   - Release `v*` tags (Release)
   - Manual dispatch (test build)

2. **Testing/Building**
   - Run unit/integration tests
   - Fail the job on test failure
   - Clean and build the project
   - Ensure output artifacts are versioned properly

3. **Pre-Release/Tag Check**
   - Compare current version with git tags
   - Fail early if duplicate
   - Create draft release (if required)

4. **Release/Publish (Optional)**
   - Upload built artifacts to GitHub release or other target
  
## Workflow jobs template

```yml
name: Build and Release

on:
  push:
    branches:
      - main
  pull_request:
  workflow_dispatch:

jobs:
  check_version:
    outputs:
      version: ${{ steps.version.outputs.version }}
      tag_exists: ${{ steps.check_tag.outputs.tag_exists }}

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Extract version
        id: version

      - name: Check if tag exists
        id: check_tag

  build:
    needs: check_version
    if: ${{ needs.check_version.outputs.tag_exists == 'false' || github.event_name == 'pull_request' || github.event_name == 'workflow_dispatch' }}

    steps:
      - name: Checkout repo

      - name: Install Dependencies

      - name: Build

      - name: Upload Artifacts

  release:
    needs: [check_version, build]
    if: github.event_name == 'push' && github.ref == 'refs/heads/main' && needs.check_version.outputs.tag_exists == 'false'

    steps:
      - name: Download all artifacts

      - name: Create Draft Release

```

