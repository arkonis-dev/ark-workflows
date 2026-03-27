# ark-workflows

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)

Reusable GitHub Actions workflows for all [arkonis-dev](https://github.com/arkonis-dev) repositories.

Full documentation at **[docs.arkonis.dev](https://docs.arkonis.dev)**.

## Available workflows

| Workflow | Purpose |
|---|---|
| [`go-ci.yml`](.github/workflows/go-ci.yml) | Go test + lint |
| [`docker-build-push.yml`](.github/workflows/docker-build-push.yml) | Build and push multi-arch image to ghcr.io |
| [`go-binary-release.yml`](.github/workflows/go-binary-release.yml) | Cross-compile Go binary and upload to GitHub Release |
| [`helm-ci.yml`](.github/workflows/helm-ci.yml) | Helm lint + version bump check on PRs |
| [`helm-release.yml`](.github/workflows/helm-release.yml) | Package Helm charts and publish to GitHub Pages |
| [`scan-image.yml`](.github/workflows/scan-image.yml) | Grype CVE scan + SARIF upload to GitHub Security tab |
| [`scan-secrets.yml`](.github/workflows/scan-secrets.yml) | TruffleHog secret scan across full git history (Apache 2.0) |

## How to call a workflow

In any `arkonis-dev` repository, create a caller workflow:

```yaml
jobs:
  go-ci:
    uses: arkonis-dev/ark-workflows/.github/workflows/go-ci.yml@main
    with:
      test-command: make test
      lint-command: make lint
```

All workflows are called with `@main` - there are no versioned releases of this repo.

## Workflow inputs

### `go-ci.yml`

| Input | Default | Description |
|---|---|---|
| `go-version-file` | `go.mod` | Path to go.mod used to select Go version |
| `test-command` | `go test ./...` | Command to run tests |
| `lint-command` | _(empty - skip)_ | Command to run linter |
| `working-directory` | `.` | Working directory for all steps |

### `docker-build-push.yml`

| Input | Default | Description |
|---|---|---|
| `image` | **required** | Full image name (e.g. `ghcr.io/arkonis-dev/ark`) |
| `dockerfile` | **required** | Path to Dockerfile relative to context |
| `context` | `.` | Docker build context |
| `platforms` | `linux/amd64,linux/arm64` | Target platforms |
| `checkout-path` | _(root)_ | Check out caller repo into a subdirectory |
| `extra-repo` | _(none)_ | `org/repo` of a sibling repo to check out |
| `extra-repo-path` | _(none)_ | Local path for the extra repo |

### `go-binary-release.yml`

| Input | Default | Description |
|---|---|---|
| `binary-name` | **required** | Output binary name (e.g. `ark`) |
| `main-package` | **required** | Go package path (e.g. `./cmd/main.go`) |
| `version-var` | `main.version` | Linker variable for version injection |

Builds for `linux/amd64`, `linux/arm64`, `darwin/amd64`, `darwin/arm64`, `windows/amd64`. Creates a GitHub Release with all binaries and `.sha256` checksums.

### `helm-ci.yml`

| Input | Default | Description |
|---|---|---|
| `charts-path` | `charts` | Path to charts directory |

On PRs, also checks that `Chart.yaml` version was bumped.

### `helm-release.yml`

| Input | Default | Description |
|---|---|---|
| `charts-path` | `charts` | Path to charts directory |
| `pages-url` | **required** | GitHub Pages base URL for the Helm repo index |

Packages charts and commits the `.tgz` + updated `index.yaml` to the `gh-pages` branch.

### `scan-image.yml`

| Input | Default | Description |
|---|---|---|
| `image` | **required** | Full image reference to scan (including tag) |
| `sarif-category` | **required** | Unique label for SARIF upload in Security tab |
| `fail-on-severity` | `high` | Minimum severity that fails the job |

## Design rules

- **No 3rd-party actions** - only `actions/*`, `docker/*`, and `github/codeql-action/*`.
- **Pinned action versions** - every `uses:` line references an exact version tag, not `@main` or `@latest`.
- **No secrets hardcoded** - all credentials flow via `secrets: inherit` from the caller.
- **`gh` CLI for releases** - GitHub Releases are created with the `gh` CLI (pre-installed on all runners), not 3rd-party release actions.

## Adding a new workflow

1. Create `.github/workflows/<name>.yml` with `on: workflow_call`.
2. Document inputs/outputs in this README.
3. Follow the design rules above - no new 3rd-party actions.
4. Open a PR; all `arkonis-dev` repos will pick it up via `@main` after merge.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](./CODE_OF_CONDUCT.md).

## License

Apache 2.0 - see [LICENSE](LICENSE).
