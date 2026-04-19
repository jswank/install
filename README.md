# install

Portable installation scripts for binary tools, powered by [binstaller](https://github.com/binary-install/binstaller).

Each script is a self-contained POSIX shell script that detects your platform, downloads the correct release from GitHub, verifies checksums, and installs the binary to `~/.local/bin`.

## Quick Start

Install any tool via a stable URL (no repo clone required):

```bash
curl -sSL https://jswank.github.io/install/trufflehog-install.sh | bash
```

Scripts accept flags: `-b` (bindir), `-d` (debug), `-q` (quiet), `-n` (dry-run). Pass a tag argument to install a specific version:

```bash
curl -sSL https://jswank.github.io/install/trufflehog-install.sh | bash -s -- v3.94.3
```

Power users can also run scripts directly from a clone:

```bash
bash scripts/trufflehog-install.sh

# Or with Task
task install-trufflehog
```

## Available Tools

| Tool | Repo | Install |
|------|------|---------|
| [aichat](https://github.com/sigoden/aichat) | sigoden/aichat | `curl -sSL https://jswank.github.io/install/aichat-install.sh \| bash` |
| [binstaller](https://github.com/binary-install/binstaller) | binary-install/binstaller | `curl -sSL https://jswank.github.io/install/binstaller-install.sh \| bash` |
| [checkov](https://github.com/bridgecrewio/checkov) | — | `curl -sSL https://jswank.github.io/install/checkov-install.sh \| bash` |
| [claude](https://claude.ai) | — | `curl -sSL https://jswank.github.io/install/claude-install.sh \| bash` |
| [eksctl](https://github.com/eksctl-io/eksctl) | eksctl-io/eksctl | `curl -sSL https://jswank.github.io/install/eksctl-install.sh \| bash` |
| [gh-md-toc](https://github.com/ekalinin/github-markdown-toc.go) | ekalinin/github-markdown-toc.go | `curl -sSL https://jswank.github.io/install/gh-md-toc-install.sh \| bash` |
| [gh-pmu](https://github.com/rubrical-studios/gh-pmu) | rubrical-studios/gh-pmu | `curl -sSL https://jswank.github.io/install/gh-pmu-install.sh \| bash` |
| [opentofu](https://github.com/opentofu/opentofu) | opentofu/opentofu | `curl -sSL https://jswank.github.io/install/opentofu-install.sh \| bash` |
| [task](https://github.com/go-task/task) | go-task/task | `curl -sSL https://jswank.github.io/install/task-install.sh \| bash` |
| [tenv](https://github.com/tofuutils/tenv) | tofuutils/tenv | `curl -sSL https://jswank.github.io/install/tenv-install.sh \| bash` |
| [tflint](https://github.com/terraform-linters/tflint) | terraform-linters/tflint | `curl -sSL https://jswank.github.io/install/tflint-install.sh \| bash` |
| [toolhive](https://github.com/stacklok/toolhive) | stacklok/toolhive | `curl -sSL https://jswank.github.io/install/toolhive-install.sh \| bash` |
| [trivy](https://github.com/aquasecurity/trivy) | aquasecurity/trivy | `curl -sSL https://jswank.github.io/install/trivy-install.sh \| bash` |
| [trufflehog](https://github.com/trufflesecurity/trufflehog) | trufflesecurity/trufflehog | `curl -sSL https://jswank.github.io/install/trufflehog-install.sh \| bash` |
| [zk](https://github.com/zk-org/zk) | zk-org/zk | `curl -sSL https://jswank.github.io/install/zk-install.sh \| bash` |

Tools without a repo listed (checkov, claude) have hand-written install scripts that don't use the binstaller pipeline.

## Taskfile Commands

All automation uses [Task](https://taskfile.dev). Run `task --list` for a summary.

### Update an existing tool (most common)

After bumping `default_version` in a config file:

```bash
task update BINARY=opentofu
```

This re-embeds checksums for the pinned version and regenerates the install script.

### Add a new tool

```bash
task new REPO=owner/repo-name
```

This initializes a binstaller config, embeds checksums, and generates the install script in one step.

### Individual steps

```bash
task init REPO=owner/repo-name               # create config
task embed-checksums BINARY=trufflehog        # embed checksums (uses default_version from config)
task embed-checksums BINARY=trufflehog VERSION=v3.94.3  # or specify a version
task gen BINARY=trufflehog                    # generate install script
task latest-release REPO=owner/repo-name      # show latest upstream release tag
task install-trufflehog                       # run the install script
```

### Prerequisites

- `binst` — install via `bash scripts/binstaller-install.sh`
- `task` — install via `bash scripts/task-install.sh`
- `gh` — GitHub CLI (needed for `latest-release`)
- `yq` — YAML processor (needed for `embed-checksums` version detection)

## Version Pinning and Updates

Each config file in `config/` pins a `default_version` with embedded checksums for that version. [Renovate](https://docs.renovatebot.com/) watches these files and opens PRs when new upstream releases are available. A CI workflow automatically re-embeds checksums and regenerates install scripts on Renovate PRs.

The update cycle:

1. Renovate detects a new release and opens a PR bumping `default_version`
2. CI runs `task embed-checksums` and `task gen` for the affected tool
3. A human reviews and merges

## Configuration

Config files live in `config/<tool>.binstaller.yml` and follow the [binstaller schema](https://github.com/binary-install/binstaller). Key fields:

- `repo` — GitHub owner/name
- `default_version` — pinned version with a `# renovate:` comment for automated updates
- `asset.template` — release asset filename pattern
- `checksums.embedded_checksums` — version-specific checksums baked into the config

### Binary name mismatches

When the binary executable name differs from the repo name, use `asset.binaries` to specify it. For example, [stacklok/toolhive](https://github.com/stacklok/toolhive) releases a binary named `thv`:

```yaml
asset:
  binaries:
    - name: thv
      path: thv
```

## Repository Structure

```
config/              .binstaller.yml configs for each tool
scripts/             generated (and hand-written) install scripts
Taskfile.yaml        automation for init/update/gen workflows
renovate.json        Renovate config for automated version bumps
.github/workflows/   CI: regenerate scripts on Renovate PRs
```

## License

MIT. See [LICENSE](LICENSE) for details.
