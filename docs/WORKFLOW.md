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
