# repo-mirror

Server-side repository copier for the OpenWRT build-actions migration.

Why it exists: pushing git packs from the local machine (China / unstable uplink) keeps
failing with `RPC failed; curl 56 ... Connection was reset`. A GitHub runner can clone the
source and push it into a repository on this account much more reliably.

## Usage

`.github/workflows/copy.yml` is `workflow_dispatch` only, with inputs:

| input | meaning |
|---|---|
| `src` | source repository, `owner/name` |
| `dst` | destination repository name under this account (created if missing) |
| `default_branch` | branch to set as the default afterwards (optional) |
| `force` | `true` to force-update existing refs |

It requires the repository secret `SYNC_TOKEN`: a PAT with `repo` + `workflow` scopes.
It copies all branches and tags. The created repository has **no fork relationship** to
the source.

## Notes

* Only branches and tags are copied - `refs/pull/*` is deliberately excluded, because
  GitHub rejects pushes to those refs.
* Releases and their assets are **not** copied by git; they must be recreated separately.
