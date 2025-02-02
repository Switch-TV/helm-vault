[![Gitpod Ready-to-Code](https://img.shields.io/badge/Gitpod-Ready--to--Code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/Switch-TV/helm-vault) 

[![License](https://img.shields.io/github/license/Switch-TV/helm-vault.svg)](https://github.com/Switch-TV/helm-vault/blob/master/LICENSE)
[![Current Release](https://img.shields.io/github/release/Switch-TV/helm-vault.svg)](https://github.com/Switch-TV/helm-vault/releases/latest)
[![Production Ready](https://img.shields.io/badge/production-ready-green.svg)](https://github.com/Switch-TV/helm-vault/releases/latest)
[![GitHub issues](https://img.shields.io/github/issues/Switch-TV/helm-vault.svg)](https://github.com/Switch-TV/helm-vault/issues)
[![GitHub pull requests](https://img.shields.io/github/issues-pr/Switch-TV/helm-vault.svg?style=flat-square)](https://github.com/Switch-TV/helm-vault/pulls)
[![Total alerts](https://img.shields.io/lgtm/alerts/g/Switch-TV/helm-vault.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/Switch-TV/helm-vault/alerts/)
[![Language grade: Python](https://img.shields.io/lgtm/grade/python/g/Switch-TV/helm-vault.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/Switch-TV/helm-vault/context:python)
[![CI](https://github.com/Switch-TV/helm-vault/actions/workflows/main.yml/badge.svg)](https://github.com/Switch-TV/helm-vault/actions/workflows/main.yml)

# Helm-Vault

Helm-Vault stores private data from YAML files in Hashicorp Vault. Helm-Vault should be used if you want to publicize your YAML configuration files, without worrying about leaking secret information.

## Table of Contents

- [Helm-Vault](#helm-vault)
  - [Table of Contents](#table-of-contents)
- [About the Project](#about-the-project)
- [Project Status](#project-status)
- [Getting Started](#getting-started)
  - [Dependencies](#dependencies)
  - [Getting the Source](#getting-the-source)
  - [Running Tests](#running-tests)
    - [Other Tests](#other-tests)
  - [Installation](#installation)
    - [Using Helm plugin manager (> 2.3.x)](#using-helm-plugin-manager--23x)
  - [Usage and Examples](#usage-and-examples)
    - [Environment Variables](#environment-variables)
    - [Basic commands:](#basic-commands)
    - [Available Flags](#available-flags)
    - [Usage examples](#usage-examples)
      - [Encrypt](#encrypt)
      - [Decrypt](#decrypt)
      - [View](#view)
      - [Edit](#edit)
      - [Clean](#clean)
    - [vault path templating](#vault-path-templating)
    - [Wrapper Examples](#wrapper-examples)
      - [Install](#install)
      - [Template](#template)
      - [Upgrade](#upgrade)
      - [Lint](#lint)
      - [Diff](#diff)
- [Release Process](#release-process)
  - [Versioning](#versioning)
- [How to Get Help](#how-to-get-help)
- [Contributing](#contributing)
- [Further Reading](#further-reading)
- [License](#license)
- [Authors](#authors)
- [Acknowledgments](#acknowledgments)

# About the Project

Helm-Vault supports the following features:

- [X] Encrypt YAML files
- [X] Decrypt YAML files
- [X] View decrypted YAML files
- [X] Edit decrypted YAML files
- [X] Clean up decrypted YAML files
- [X] Helm Wrapper, automatically decrypts and cleans up during helm commands
  - [X] Install
  - [X] Upgrade
  - [X] Template
  - [X] Lint
  - [X] Diff

Helm-Vault was created to provide a better way to manage secrets for Helm, with the ability to take existing public Helm Charts, and with minimal modification, provide a way to have production data that is not stored in a public location.

```
$ helm vault enc values.yaml
Input a value for /mariadb/db/password:
Input a value for /externalDatabase/user:
Input a value for /nextcloud/password:
```

**[Back to top](#table-of-contents)**

# Project Status

Build Status:

[![CI](https://github.com/Switch-TV/helm-vault/actions/workflows/main.yml/badge.svg)](https://github.com/Switch-TV/helm-vault/actions/workflows/main.yml)

Helm-Vault is in a production state. It should work across platforms, and should be able to handle most YAML thrown at it.

**[Back to top](#table-of-contents)**

# Getting Started

To get started with Helm-Vault, follow these steps:

1. Clone the repository to your machine

2. Install the requirements

    `pip3 install -r requirements.txt`

3. Test it out! This will test out encrypting an example YAML file

    `./src/vault.py enc ./tests/test.yaml`

## Dependencies

- [ ] Python 3.7+
- [ ] pip3
- [ ] Working Hashicorp Vault environment
- [ ] Hashicorp Vault token
- [ ] Environment Variables for Vault
  - [ ] VAULT_ADDR: The HTTP Address of Vault
  - [ ] VAULT_TOKEN: The token for accessing Vault
- [ ] YAML files must be in a git repo or have the full path specified in the file. See [Vault Path Templating](#vault-path-templating).

## Getting the Source

This project is [hosted on GitHub](https://github.com/Switch-TV/helm-vault). You can clone this project directly using this command:

```
git clone git@github.com:Justin-Tech/helm-vault.git
```

## Running Tests

Helm-Vault has built-in unit tests using pytest, you can run them with the command below:

```
pip3 install -r ./tests/requirements.txt
python3 -m pytest
```

for running tests using docker, you can use the following command:

```
./run-test.sh
```

### Other Tests

Unittesting and integration testing is automatically run via Github Actions on commit and PRs.

Additionally, code quality checking is handled by LGTM.com

Both of these checks must pass before PRs will be merged.

## Installation

### Using Helm plugin manager (> 2.3.x)

```
pip3 install git+https://github.com/Switch-TV/helm-vault
helm plugin install https://github.com/Switch-TV/helm-vault
```

## Usage and Examples

```
$ helm vault --help
usage: vault.py [-h] {enc,dec,clean,view,edit} ...

Store secrets from Helm in Vault

Requirements:

Environment Variables:

VAULT_ADDR:     (The HTTP address of Vault, for example, http://localhost:8200)
VAULT_TOKEN:    (The token used to authenticate with Vault)

positional arguments:
  {enc,dec,clean,view,edit}
    enc                 Parse a YAML file and store user entered data in Vault
    dec                 Parse a YAML file and retrieve values from Vault
    clean               Remove decrypted files (in the current directory)
    view                View decrypted YAML file
    edit                Edit decrypted YAML file. DOES NOT CLEAN UP AUTOMATICALLY.

optional arguments:
  -h, --help            show this help message and exit
```

Any YAML file can be transparently "encrypted" as long as it has a deliminator for secret values.

Decrypted files have the suffix ".yaml.dec" by default

### Environment Variables

**Note:** Flags take precedent over Environment Variables.

|Environment Variable|Default Value<br>(if unset)|Overview|Required|
|--------------------|---------------------------|--------|--------|
|`VAULT_ADDR`|`null`|The HTTP(S) address fo Vault|Yes|
|`VAULT_TOKEN`|`null`|The token used to authenticate with Vault|Yes|
|`VAULT_NAMESPACE`|`null`|The Vault namespace used for the command||
|`VAULT_PATH`|`secret/helm`|The default path used within Vault||
|`VAULT_MOUNT_POINT`|`secret`|The default mountpoint used within Vault||
|`SECRET_DELIM`|`changeme`|The value which will be searched for within YAML to prompt for encryption/decryption||
|`SECRET_TEMPLATE`|`VAULT:`|Used for [Vault Path Templating](#vault-path-templating)||
|`EDITOR`| - Windows: `notepad` <br> - macOS/Linux: `vi`|The editor used when calling `helm vault edit`||
|`KVVERSION`|`v1`|The K/V secret engine version within Vault||

More detailed information available below:

<details>
<summary>VAULT_ADDR</summary>

The HTTP(S) address of Vault, for example, http://localhost:8200

Default when not set: `null`, the program will error and inform you that this address needs to be set as an environment variable.
</details>

<details>
<summary>VAULT_TOKEN</summary>

The token used to authenticate with Vault.

Default when not set: `null`, the program will error and inform you that this value needs to be set as an environment variable.
</details>

<details>
<summary>VAULT_NAMESPACE</summary>

The Vault namespace used for the command. Namespaces are isolated environments that functionally exist as "Vaults within a Vault." They have separate login paths and support creating and managing data isolated to their namespace. Namespaces are only available in Vault Enterprise.

Default when not set: `null`.
</details>

<details>
<summary>VAULT_PATH</summary>

This is the path within Vault that secrets are stored. It should start with the name of the secrets engine being used and an optional folder within that secrets engine that all Helm-Vault secrets will be stored.

Default when not set: `secret/helm`, where `secret` is the secrets engine being used, and `helm` is the folder in which all secrets will be stored.
</details>

<details>
<summary>VAULT_MOUNT_POINT</summary>

This is the mountpoint within Vault that secrets are stored. Vault stores secrets in the following url format `/{mount_point}/data/{path}`. Mountpoint in this case could also include any namespaces, e.g. `namespace1/subnamespace/mountpoint` = `/namespace1/subnamespace/mountpoint/data/{path}`.

Default when not set: `secret`, where `secret` is the mountpoint being used.
</details>

<details>
<summary>SECRET_DELIM</summary>

This is the value which Helm-Vault will search for within the YAML files to prompt for encryption, or replace when decrypting.

Default when not set: `changeme`.
</details>

<details>
<summary>SECRET_TEMPLATE</summary>

This is the value that Helm-Vault will search for within the YAML files to denote [Vault Path Templating](#vault-path-templating).

Default when not set: `VAULT:`
</details>

<details>
<summary>EDITOR</summary>

This is the editor that Helm-Vault will use when requesting `helm vault edit`.

Default when not set:

- Windows: `notepad`
- macOS/Linux: `vi`

</details>

<details>
<summary>KVVERSION</summary>

This is the K/V secret engine version within Vault, currently `v1` and `v2` are supported.

Default when not set: `v1`

**Note:** Expect this to change in a later version, as Vault now defaults to `v2` K/V secrets engines.
</details>

### Basic commands:

```
  enc           Encrypt file
  dec           Decrypt file
  view          Print decrypted file
  edit          Edit file (decrypt before, manual cleanup)
  clean         Delete *.yaml.dec files in directory (recursively)
```

Each of these commands have their own help, referenced by `helm vault {enc,dec,clean,view,edit} --help`.

### Available Flags

|Flag|Usage|Default|Availability|
|----|-----|-------|------------|
|`-d`, `--deliminator`|The secret deliminator used when parsing|`changeme`|`enc`, `dec`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-vp`, `--vaultpath`|The Vault Path (secret mount location in Vault)|`secret/helm`|`enc`, `dec`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-mp`, `--mountpoint`|The Vault Mount Point|`secret`|`enc`, `dec`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-vt`, `--vaulttemplate`|Substring with path to vault key instead of deliminator.|`VAULT:`|`enc`, `dec`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-kv`, `--kvversion`|The version of the KV secrets engine in Vault|`v1`|`enc`, `dec`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-v`, `--verbose`|Verbose output||`enc`, `dec`, `clean`, `view`, `edit`, `install`, `template`, `upgrade`, `lint`, `diff`|
|`-s`, `--secret-file`|File containing secrets for input, rather than using stdin, must end in `.yaml.dec`||`enc`|
|`-f`, `--file`|The specific YAML file to be deleted, without `.dec`||`clean`|
|`-ed`, `--editor`|Editor name|Windows: `notepad`, macOS/Linux: `vi`|`edit`|
|`-f`, `--values`|The encrypted YAML file to decrypt on the fly||`install`, `template`, `upgrade`, `lint`, `diff`|
|`-e`, `--environment`|Environment that secrets should be stored under||`enc`, `dec`, `clean`, `install`|


### Usage examples

#### Encrypt

The encrypt operation encrypts a values.yaml file and saves the encrypted values in Vault:

```
$ helm vault enc values.yaml
Input a value for nextcloud.password: asdf1
Input a value for externalDatabase.user: asdf2
Input a value for .mariadb.db.password: asdf3
```

If you don't want to enter the secrets manually on stdin, you can pass a file containing the secrets. Copy `values.yaml` to `values.yaml.dec` and edit the file, replacing "changeme" (the deliminator) with the secret value. Then you can save the secret to vault by running: 

```
$ helm vault enc values.yaml -s values.yaml.dec
```

By default the name of the secret file has to end in `.yaml.dec` so you can add this extension to gitignore to prevent committing a secret to your git repo.

In addition, you can namespace your secrets to a desired environment by using the `-e` flag.

```
helm vault enc values.yaml -e prod
Input a value for nextcloud.password: asdf1
Input a value for externalDatabase.user: asdf2
Input a value for mariadb.db.password: asdf3
```

#### Decrypt

The decrypt operation decrypts a values.yaml file and saves the decrypted result in values.yaml.dec:

```
$ helm vault dec values.yaml
```

The values.yaml.dec file:
```
...
nextcloud:
  host: nextcloud.example.com
  username: admin
  password: asdf1
...
mariadb:
parameters
  enabled: true

  db:
    name: nextcloud
    user: nextcloud
    password: asdf2
...
```

If leveraging environment specific secrets, you can decrypt the desired environment by specifying with the `-e` flag.

Doing so will result in a decrypted file that is stored as `my_file.yaml.{environment}.dec`

For example

```
$ helm vault dec values.yaml -e prod
```

Will result in your production environment secrets being dumped into a file named `values.yaml.prod.dec`

#### View

The view operation decrypts values.yaml and prints it to stdout:
```
$ helm vault view values.yaml
```

#### Edit

The edit operation will decrypt the values.yaml file and open it in an editor.

```
$ helm vault edit values.yaml
```

This will read a value from $EDITOR, or be specified with the `-e, --editor` option, or will choose a default of `vi` for Linux/MacOS, and `notepad` for Windows.

Note: This will save a `.dec` file that is not automatically cleaned up.

#### Clean

The operation will delete all decrypted files in a directory:

```
$ helm vault clean
```

### Vault Path Templating

It is possible to setup vault's path inside helm chart like this

```
key1: VAULT:helm1/test/key1
key2: VAULT:/helm2/test/key2
```
This mean that key1 will be storing into base_path/helm1/test/key1 and key2 into /helm2/test/key2 . Where is helm2 is root path enabled via secrets enable. For example:

```
vault secrets enable  -path=helm2 kv-v2
```

To override default value of template path pattern use **SECRET_TEMPLATE** variable. By default this value is VAULT: . This is mean that all keys with values like VAULT:something will be stored inside vault.

#### String Interpolation

When using Vault Path Templating, it's possible to interpolate the vault values with other strings like this:

```yaml
key1: "keyValue=$(VAULT:helm1/test/key1)"
key2: "secondKeyValue=$(VAULT:/helm2/test/key2)"
combinedKeys: "key1=$(VAULT:helm1/test/key1), key2=$(VAULT:/helm2/test/key2)"
```

Encrypting values referenced in this manner is also supported if entered manually but **will not work with --secret-file**

### Wrapper Examples

#### Install

The operation wraps the default `helm install` command, automatically decrypting the `-f values.yaml` file and then cleaning up afterwards.

```
$ helm vault install stable/nextcloud --name nextcloud --namespace nextcloud -f values.yaml
```

Specifically, this command will do the following:

1. Run `helm install` with the following options:
  1. `stable/nextcloud` - the chart to install
  1. `--name nextcloud` - the Helm release name will be `nextcloud`
  1. `--namespace nextcloud` - Nextcloud will run in the nextcloud namespace on Kubernetes
  1. `-f values.yaml` - the (encrypted) values file to use

#### Template

The operation wraps the default `helm template` command, automatically decrypting the `-f values.yaml` file and then cleaning up afterwards.

```
$ helm vault template ./nextcloud --name nextcloud --namespace nextcloud -f values.yaml
```

1. Run `helm template` with the following options:
  1. `./nextcloud` - the chart to template
  1. `--name nextcloud` - the Helm release name will be `nextcloud`
  1. `--namespace nextcloud` - Nextcloud will run in the nextcloud namespace on Kubernetes
  1. `-f values.yaml` - the (encrypted) values file to use

#### Upgrade

The operation wraps the default `helm upgrade` command, automatically decrypting the `-f values.yaml` file and then cleaning up afterwards.

```
$ helm vault upgrade nextcloud stable/nextcloud -f values.yaml
```

1. Run `helm upgrade` with the following options:
  1. `nextcloud` - the Helm release name
  1. `stable/nextcloud` - the chart path
  1. `-f values.yaml` - the (encrypted) values file to use

#### Lint

The operation wraps the default `helm lint` command, automatically decrypting the `-f values.yaml` file and then cleaning up afterwards.

```
$ helm vault lint nextcloud -f values.yaml
```

1. Run `helm upgrade` with the following options:
  1. `nextcloud` - the Helm release name
  1. `-f values.yaml` - the (encrypted) values file to use

#### Diff

The operation wraps the `helm diff` command (diff is another Helm plugin), automatically decrypting the `-f values.yaml` file and then cleaning up afterwards.

```
$ helm vault diff upgrade nextcloud stable/nextcloud -f values.yaml
```

1. Run `helm diff upgrade` with the following options:
  1. `nextcloud` - the Helm release name
  1. `stable/nextcloud` - the Helm chart
  1. `-f values.yaml` - the (encrypted) values file to use

**[Back to top](#table-of-contents)**

# Release Process

Releases are made for new features, and bugfixes.

To get a new release, run the following:

```
helm plugin upgrade vault
```

## Versioning

This project uses [Semantic Versioning](http://semver.org/). For a list of available versions, see the [repository tag list](https://github.com/Switch-TV/helm-vault/tags).

**[Back to top](#table-of-contents)**

# How to Get Help

If you need help or have questions, please open a new discussion Q&A section.

# Contributing

We encourage public contributions! Please review [CONTRIBUTING.md](docs/CONTRIBUTING.md) for details on our code of conduct and development process.

**[Back to top](#table-of-contents)**

# Further Reading

[Helm](https://helm.sh/)
[Hashicorp Vault](https://www.vaultproject.io/)

**[Back to top](#table-of-contents)**

# License

Copyright (c) 2019 Justin Gauthier

This project is licensed under GPLv3 - see [LICENSE.md](LICENSE.md) file for details.

**[Back to top](#table-of-contents)**

# Authors

* **[Justin Gauthier](https://github.com/ThatsInsane)**

**[Back to top](#table-of-contents)**

# Acknowledgments

The idea for this project comes from [Helm-Secrets](https://github.com/futuresimple/helm-secrets)

Special thanks to the [Python Discord](https://discord.gg/python) server.

**[Back to top](#table-of-contents)**
