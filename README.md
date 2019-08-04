# terraform-module-versions

Checks for updates of external terraform modules referenced in given `.tf` files. Outputs JSONL (one JSON object per line). Pretty-printed output TBD.

Supported module sources:
- **Git** (`git::`, `github.com/...`, or `git@github.com:...` values for `source`) with SemVer tags.
- **Terraform Registry** (public `<NAMESPACE>/<NAME>/<PROVIDER>` and private `<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>`)

## Contents

- [Examples](#examples)
  - [List current modules with their versions](#list-current-modules-with-their-versions)
  - [Check for module updates](#check-for-module-updates)
  - [Check for updates of specific modules](#check-for-updates-of-specific-modules)
- [Get it](#get-it)
- [Usage](#usage)

## Examples

```sh
$ cat main.tf
```

```terraform
module "consul" {
  source = "hashicorp/consul/aws"
  version = "> 0.1.0"
}

module "example" {
  source = "git::ssh://git@github.com/sgreben/terraform-module-versions?ref=0.10.0"
  version = "~> 0.10"
}
```

### List current modules with their versions

```sh
# default operation: list current modules with their versions and version constraints (if specified)
$ terraform-module-versions main.tf
```

```json
{
  "path": "main.tf",
  "name": "consul",
  "source": "hashicorp/consul/aws",
  "versionConstraint": "> 0.1.0",
  "type": "terraform-registry"
}
{
  "path": "main.tf",
  "name": "example",
  "source": "git::ssh://git@github.com/sgreben/terraform-module-versions?ref=0.10.0",
  "version": "0.10.0",
  "versionConstraint": "~> 0.10",
  "type": "git"
}
```

### Check for module updates

```sh
# -updates: check for module updates from (usually) remote sources
$ terraform-module-versions -updates main.tf
```

```json
{
  "path": "main.tf",
  "name": "consul",
  "source": "hashicorp/consul/aws",
  "versionConstraint": "> 0.1.0",
  "type": "terraform-registry",
  "latestMatchingUpdate": "0.7.3",
  "updates": [
    "0.1.1",
    "0.1.2",
    ...,
    "0.7.2",
    "0.7.3"
  ],
  "hasMinorUpdate": true,
  "hasPatchUpdate": true
}
{
  "path": "main.tf",
  "name": "example",
  "source": "git::ssh://git@github.com/sgreben/terraform-module-versions?ref=0.10.0",
  "version": "0.10.0",
  "versionConstraint": "~> 0.10",
  "type": "git",
  "latestMatchingUpdate": "0.11.0",
  "updates": [
    "0.11.0"
  ],
  "hasMinorUpdate": true
}
```

### Check for updates of specific modules

```sh
# -updates and -module: check for updates of specific modules
$ terraform-module-versions -updates -module=example main.tf
```

```json
{
  "path": "main.tf",
  "name": "example",
  "source": "git::ssh://git@github.com/sgreben/terraform-module-versions?ref=0.10.0",
  "version": "0.10.0",
  "versionConstraint": "~> 0.10",
  "type": "git",
  "latestMatchingUpdate": "0.11.0",
  "updates": [
    "0.11.0"
  ],
  "hasMinorUpdate": true
}
```

## Get it

Using go get:

```bash
go get -u github.com/sgreben/terraform-module-versions
```

Or [download the binary](https://github.com/sgreben/terraform-module-versions/releases/latest) from the releases page.

```bash
# Linux
curl -LO https://github.com/sgreben/terraform-module-versions/releases/download/0.11.2/terraform-module-versions_0.11.2_linux_x86_64.zip
unzip terraform-module-versions_0.11.2_linux_x86_64.zip

# OS X
curl -LO https://github.com/sgreben/terraform-module-versions/releases/download/0.11.2/terraform-module-versions_0.11.2_osx_x86_64.zip
unzip terraform-module-versions_0.11.2_osx_x86_64.zip

# Windows
curl -LO https://github.com/sgreben/terraform-module-versions/releases/download/0.11.2/terraform-module-versions_0.11.2_windows_x86_64.zip
unzip terraform-module-versions_0.11.2_windows_x86_64.zip
```

## Usage

```text
terraform-module-versions [PATHS...]

Usage of terraform-module-versions:
  -module value
    	include this module (may be specified repeatedly. by default, all modules are included)
  -q	(alias for -quiet)
  -quiet
    	suppress log output (stderr)
  -u	(alias for -updates)
  -updates
    	check for updates
  -version
    	print version and exit
```