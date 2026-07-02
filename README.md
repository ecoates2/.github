# ecoates2/.github

Central home for shared GitHub Actions workflows across ecoates2's Rust repos.
Reusable workflows live in [`.github/workflows/`](.github/workflows) and are
consumed by other repos via `uses:` references pinned to a version tag.

## What's here

| File | Purpose |
| --- | --- |
| [`.github/workflows/rust-ci.yml`](.github/workflows/rust-ci.yml) | Reusable Rust CI: fmt, clippy, test (Linux + Windows), cargo-deny, typos, conventional commits, cargo-hack, rustdoc, coverage, semver-checks. |
| [`.github/workflows/release-plz.yml`](.github/workflows/release-plz.yml) | Reusable release automation (Release PR + publish to crates.io). |
| [`examples/ci.yml`](examples/ci.yml) | Copy into each repo at `.github/workflows/ci.yml`. |
| [`examples/release-plz.yml`](examples/release-plz.yml) | Copy into each publishable repo at `.github/workflows/release-plz.yml`. |

## How reusable workflows are referenced

Because this repo is literally named `.github`, the path has `.github` twice:

```yaml
uses: ecoates2/.github/.github/workflows/rust-ci.yml@v1
#      └── owner ──┘└─repo─┘└──── path inside the repo ────┘└tag┘
```

## Versioning

Callers pin to a moving major tag (`@v1`). Publish changes like this:

```sh
# make + commit changes to the workflows, then:
git tag -f v1            # move the major tag to the new commit
git push -f origin v1
```

Cut a new major (`v2`) only when you change the input contract in a
backwards-incompatible way, so existing callers on `@v1` don't break.

## rust-ci.yml inputs

| Input | Default | Notes |
| --- | --- | --- |
| `rust-container-image` | `rust:latest` | Container for the Linux jobs. |
| `linux-apt-packages` | `""` | Space-separated apt packages for Linux jobs. |
| `run-semver-checks` | `true` | Disable for unpublished crates (nothing to diff against). |
| `run-commit-check` | `true` | Conventional Commits validation via cocogitto. |
| `run-coverage` | `true` | lcov report uploaded as an artifact. |
| `workspace-flag` | `""` | Set to `--workspace` for a Cargo workspace/monorepo. |

## Onboarding a repo

1. Add [`examples/ci.yml`](examples/ci.yml) as `.github/workflows/ci.yml`,
   editing the `with:` inputs for that repo.
2. For publishable crates, add [`examples/release-plz.yml`](examples/release-plz.yml)
   as `.github/workflows/release-plz.yml`, and in that repo:
   - Settings -> Actions -> General -> Workflow permissions:
     enable "Allow GitHub Actions to create and approve pull requests".
   - Add the `CARGO_REGISTRY_TOKEN` secret.
3. Delete the repo's old standalone CI/release YAML.
