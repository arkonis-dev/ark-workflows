# Contributing to ark-workflows

Thank you for your interest in contributing. This repo contains reusable GitHub Actions workflows for all [arkonis-dev](https://github.com/arkonis-dev) repositories. Contributions are YAML-only - no Go code required.

## Before you start

- Check existing workflows under `.github/workflows/` to avoid duplicating functionality.
- For new workflows, open an issue first to discuss whether it fits the shared-workflow model.
- Bug fixes and documentation improvements can go straight to a PR.

## Adding or modifying a workflow

Each workflow lives in `.github/workflows/` and follows these conventions:

- **Reusable workflows** use `on: workflow_call:` and expose inputs/secrets explicitly.
- **Caller workflows** in other repos use `uses: arkonis-dev/ark-workflows/.github/workflows/<name>.yml@main`.
- All inputs must have descriptions and types declared.
- Secrets must be passed explicitly - never rely on ambient org secrets without declaring them.

### Checklist before submitting

- [ ] The workflow runs correctly when called from a test caller in a fork.
- [ ] All `actions/checkout` steps are pinned to `@v6`.
- [ ] All `actions/setup-go` steps are pinned to `@v5.5.0`.
- [ ] Any third-party actions are pinned to a specific SHA (not a tag).
- [ ] `permissions:` is set to the minimum required - never use `write-all`.
- [ ] No hardcoded org names, repo names, or internal URLs.
- [ ] Inputs have sensible defaults where applicable.

## Security practices

- **No secrets in workflow files** - never echo or log secret values; always use masked variables.
- **Pin action versions to SHAs** for third-party actions to prevent supply-chain attacks.
- **Minimal permissions** - declare only what the workflow actually needs (`contents: read`, `packages: write`, etc.).
- **No `pull_request_target` without explicit trust checks** - this trigger has write access and is a common attack vector.

## Testing a workflow change

The easiest way to test is to create a branch and point a caller workflow in a fork at your branch:

```yaml
uses: arkonis-dev/ark-workflows/.github/workflows/go-ci.yml@your-branch
```

Verify the workflow runs and produces the expected outputs before opening a PR against `main`.

## Submitting a pull request

1. Fork the repo and create a branch from `main`.
2. Make your changes with focused commits.
3. Open a PR against `main` with a clear description of what changed and why.
4. Include a link to a test run showing the workflow worked correctly.

We use **Rebase and merge** to keep a linear history on `main`.

## Reporting issues

Open a [GitHub issue](https://github.com/arkonis-dev/ark-workflows/issues/new) with:

- The workflow name
- The caller repo and workflow that triggered it
- The error or unexpected behavior observed
- A link to the failing run (if public)

## Security vulnerabilities

See [SECURITY.md](./SECURITY.md) - please do **not** open a public issue.

## License

By contributing, you agree that your contributions will be licensed under the [Apache 2.0 License](./LICENSE).
