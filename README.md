# gh-artifact-purge

A `gh` extension to list and delete GitHub Actions artifacts based on new retention policy.

## Quickstart

1. Download and install [jq](https://stedolan.github.io/jq/download/)
1. `gh extension install andyfeller/gh-artifact-purge`
1. `gh artifact-purge <organization> <retention_in_days>`
1. `gh artifact-purge --run <organization> <retention_in_days>`
1. Profit! :moneybag: :money_with_wings: :money_mouth_face: :money_with_wings: :moneybag:

## Usage

> **Note**
> `gh-artifact-purge` requires the use of coarse-grained v1 PAT token with `repo` scope.

```shell
$ gh artifact-purge --help

List and delete GitHub Actions artifacts based on new retention policy.

USAGE
  gh-artifact-purge [options] <owner> <retention_in_days>
  gh-artifact-purge [options] <owner>/<repo> <retention_in_days>

FLAGS
  -d, --debug                          Enable debugging
  -h, --help                           Displays help usage
  -r, --run                            Apply changes; defaults to dryrun
  -p, --purge-expired                  Purge expired artifacts; defaults to skip
```

### How does it work

`gh-artifact-purge` evaluates GitHub Actions artifacts by calculating an `Amended` expiration date based on the original `Created` date and the new retention setting.

There are 3 actions taken depending on the amended expiration:

1. `skipping` is when the amended expiration is in the future, resulting in no changes
2. `nominating` is when the amended expiration is in the past but the `-r,--run` flag is missing, resulting in no changes
3. `deleting` is when the amended expiration is in the past and `-r,--run` flag is provided, resulting in the artifact being deleted

Example of extension output:

```shell
$ ./gh-artifact-purge andyfeller 5
Evaluating artifacts against 2023-06-19T20:33:50Z (1687221230 seconds)
andyfeller:  processing
andyfeller/too-new:  processing
andyfeller/too-new:  skipping 758779777
    Name:    nonsensical
    ID:      758779777
    Size:    129144534 bytes
    Created: 2023-06-20T00:12:15Z  (1687234335 seconds)
    Amended: 2023-06-25T00:12:15Z  (1687666335 seconds)
    Expires: 2023-06-27T00:10:01Z  (1687839001 seconds)
    Workflow:
        Name:  Generated scan workflow
        ID:    48463692
        Path:  .github/workflows/whatever.yml
        State: active
andyfeller/too-new:  skipping 758779776
    Name:    equally_nonsensical
    ID:      758779776
    Size:    137773796 bytes
    Created: 2023-06-20T00:12:15Z  (1687234335 seconds)
    Amended: 2023-06-25T00:12:15Z  (1687666335 seconds)
    Expires: 2023-06-27T00:00:00Z  (1687838400 seconds)
    Workflow:
        Name:  Generated scan workflow
        ID:    48463692
        Path:  .github/workflows/whatever.yml
        State: active
andyfeller/codeql-action-testing--spree--spree:  processing
andyfeller/codeql-action-testing--spree--spree:  nominating 732080845
    Name:    just_right
    ID:      732080845
    Size:    2362575 bytes
    Created: 2023-06-05T13:50:02Z  (1685987402 seconds)
    Amended: 2023-06-10T13:50:02Z  (1686419402 seconds)
    Expires: 2023-12-02T13:49:27Z  (1701542967 seconds)
    Workflow:
        Name:  demo
        ID:    59281797
        Path:  .github/workflows/release.yml
        State: active
```

## Setup

Like any other `gh` CLI extension, `gh-artifact-purge` is trivial to install or upgrade and works on most operating systems:

- **Installation**

  ```shell
  gh extension install andyfeller/gh-artifact-purge
  ```
  
  _For more information: [`gh extension install`](https://cli.github.com/manual/gh_extension_install)_

- **Upgrade**

  ```shell
  gh extension upgrade gh-artifact-purge
  ```

  _For more information: [`gh extension upgrade`](https://cli.github.com/manual/gh_extension_upgrade)_
