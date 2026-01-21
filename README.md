# binst

A collection of installation scripts for binary tools, generated using [binstaller](https://github.com/binary-install/binstaller).  These scripts are portable POSIX shell scripts that work across Linux, macOS, and Windows (Git Bash/WSL) and are designed to be a simple, secure way to install self-contained binaries from GitHub releases.

* [Quick Start](#quick-start)
* [Workflows](#workflows)
    * [Creating New Installation Scripts](#creating-new-installation-scripts)
* [Notes](#notes)
    * [Binary Names](#binary-names)

## Quick Start

Install any binary using its generated script:

```bash
# install trufflehog
$ scripts/trufflehog-install.sh

# install task
$ scripts/task-install.sh
```

## Workflows

Common workflows for the creation, maintantenance, usage of the installation scripts are encapsulated in [Task](https://taskfile.dev) tasks. 

```
# list availale tasks
$ task --list

task: Available tasks for this project:
* default:               Create a new installation script for a binary
* embed-checksums:       Embed checksums into a binst configuration file.
* gen:                   Generate installation script from binst configuration.
* init:                  Initialize binst configuration for a GitHub project.
* install-*:             Install a binary using its installation script.
* latest-release:        Determine the latest release available for a given repo.

# install a binary
$ task install-trufflehog
```

### Creating New Installation Scripts

The default task creates a new installation script for a binary from its GitHub
repository. This is often the only step needed to add a new installation script
to the collection.

```
# create a new installation script by providing the GitHub repository as an argument:
$ task REPO=owner/repo-name

# summarize the default task
$ task --summary
task: default

Create a new installation script for a binary by initializing a binst config,
embedding checksums, and generating the installation script. The latest release, as
determined by the latest-release task, will be used unless a specific version is
provided.

Invoke this task like:  task default REPO=trufflesecurity/trufflehog

vars:
  CONFIG_DIR: "./config"
  SCRIPT_DIR: "./scripts"
  BINARY: "{{.REPO | base}}"
  VERSION: "latest"

requires:
  vars:
    - REPO

commands:
 - Task: init
 - Task: embed-checksums
 - Task: gen
```

## Notes

### Binary Names

Sometimes the binary executable name differs from the GitHub repository name. When this occurs, the `asset.binaries` section in the configuration file defines the actual binary name. If this is the case, the generated installation script will need to be manually edited.

As an example, the released artifact for [stacklok/toolhive](https://github.com/stacklok/toolhive) is named `thv` rather than `toolhive`. The [configuration file](config/toolhive.binstaller.yml) specifies the correct name for the binary in the `asset.binaries` section.

```yaml
repo: stacklok/toolhive
asset:
binaries:
  - name: thv
    path: thv
```

## License
MIT. See [LICENSE](LICENSE) for details.
