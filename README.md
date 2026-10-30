# backplane-validate

A GitHub Action that checks Backplane `manifest.yml` and `rfcs.yml` files with `bp validate`, and the released `bp` binaries it runs. Releases are published here from `crates/bp` in the private `wellcometrust/backplane` repo, so do not edit this repo by hand.

## Use the action

```yaml
name: backplane
on: pull_request
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: wellcometrust/backplane-validate@v0
```

The job fails when a document breaks its schema. A reference to an entity this repo does not declare, such as a `dependsOn` on another repo's component, is printed as a warning and does not fail the job, because Backplane resolves references across the org when it reads the repo.

| Input | Default | Meaning |
|---|---|---|
| `path` | `.` | Directory to check |
| `org` | `false` | `true` when `path` holds a whole org (`backplane-config/` and team repos), to run the org-level checks as well |
| `version` | the release the action was published with | Which `bp` release to download |

The action runs on Linux x86_64 and arm64 runners and on macOS arm64 runners. It reads every `manifest.yml` and `rfcs.yml` under `path` except in `.git`, so run it before steps that add dependency trees such as `node_modules`.

## Run it locally

Download `bp-<target>` for your platform from the latest release, make it executable, and run:

```sh
bp validate              # every manifest.yml and rfcs.yml under the current directory
bp validate --org DIR    # a whole org
```

It exits 1 when there are errors and 2 when it cannot read the directory.
