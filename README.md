# binst

A curated collection of portable installation scripts for binary tools, powered by [binstaller](https://github.com/binary-install/binstaller).

## Overview

This repository provides POSIX-compliant shell scripts that simplify the installation of popular binary tools from GitHub releases. Each script is:

- **Portable**: Works across Linux, macOS, and Windows (Git Bash/WSL)
- **Secure**: Includes checksum verification for downloaded binaries
- **Simple**: Single command installation with no dependencies
- **Self-contained**: No package manager or runtime required

## Table of Contents

- [Quick Start](#quick-start)
- [Available Tools](#available-tools)
- [Usage](#usage)
  - [Installing Binaries](#installing-binaries)
  - [Using Task Commands](#using-task-commands)
- [Creating New Scripts](#creating-new-scripts)
  - [Prerequisites](#prerequisites)
  - [Automatic Generation](#automatic-generation)
  - [Manual Configuration](#manual-configuration)
- [Configuration](#configuration)
  - [Binary Names](#binary-names)
- [Contributing](#contributing)
- [License](#license)

## Quick Start

Install any binary directly using its installation script:

```bash
# Install trufflehog
./scripts/trufflehog-install.sh

# Install task
./scripts/task-install.sh

# Install checkov
./scripts/checkov-install.sh
```

Binaries are installed to `~/.local/bin` by default (customizable via environment variables).

## Available Tools

Installation scripts are located in the `scripts/` directory. Run `ls scripts/` to see all available tools, or browse the directory on GitHub.

## Usage

### Installing Binaries

Run any installation script directly:

```bash
./scripts/<tool-name>-install.sh
```

The script will:
1. Detect your OS and architecture
2. Download the latest release from GitHub
3. Verify checksums
4. Install the binary to your PATH

### Using Task Commands

This repository includes [Taskfile](https://taskfile.dev) automation for common workflows:

```bash
# List all available tasks
task --list

# Install a specific binary via Task
task install-trufflehog
task install-aichat
task install-checkov
```

Available tasks:
- `task` - Create a new installation script (requires `REPO` variable)
- `task install-*` - Install a binary using its script
- `task init` - Initialize binstaller configuration for a GitHub project
- `task embed-checksums` - Embed checksums into a configuration file
- `task gen` - Generate installation script from configuration
- `task latest-release` - Check the latest release version

## Creating New Scripts

### Prerequisites

First, install binstaller:

```bash
./scripts/binstaller-install.sh
```

### Automatic Generation

The easiest way to add a new installation script is using the default Task workflow:

```bash
# Create script for a GitHub repository
task REPO=owner/repo-name

# Example: Add installation script for trufflehog
task REPO=trufflesecurity/trufflehog
```

This command will:
1. Initialize a binstaller configuration file
2. Fetch and embed checksums from the latest release
3. Generate the installation script

For more details on the default task:

```bash
task --summary
```

### Manual Configuration

For advanced use cases, you can manually create or edit configuration files:

1. **Initialize configuration**:
   ```bash
   task init REPO=owner/repo-name
   ```

2. **Edit the configuration** in `config/repo-name.binstaller.yml` as needed

3. **Embed checksums**:
   ```bash
   task embed-checksums BINARY=repo-name
   ```

4. **Generate the script**:
   ```bash
   task gen BINARY=repo-name
   ```

## Configuration

### Binary Names

In some cases, the binary executable name differs from the GitHub repository name. When this occurs, specify the actual binary name in the `asset.binaries` section of the configuration file.

**Example**: The [stacklok/toolhive](https://github.com/stacklok/toolhive) repository releases a binary named `thv`. The [configuration file](config/toolhive.binstaller.yml) handles this:

```yaml
repo: stacklok/toolhive
asset:
  binaries:
    - name: thv
      path: thv
```

After generation, you may wish to rename the installation script from `thv-install.sh` to `toolhive-install.sh` for consistency.

## Contributing

Contributions are welcome! To add a new binary installation script:

1. Fork this repository
2. Create a new script using `task REPO=owner/repo-name`
3. Test the installation script
4. Submit a pull request

Please ensure:
- The binary is a popular, well-maintained tool
- The installation script works across all supported platforms
- Checksums are embedded for security

## License

MIT. See [LICENSE](LICENSE) for details.
