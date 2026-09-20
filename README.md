# pg17

A mirror of the upstream [`pgvector/pgvector:pg17`](https://hub.docker.com/r/pgvector/pgvector) image, republished to
`ghcr.io/okou-ai/pg17` so CI does not depend on anonymous Docker Hub pulls.

## Why

`okou-ai/okou` runs the API test shards against a `pgvector/pgvector:pg17` service container. Anonymous Docker Hub pulls
from GitHub-hosted runners fail intermittently, and because the failure happens during `Initialize containers` the job
dies before a single test runs. Two examples on 2026-07-25 alone:

- [run 30157827913](https://github.com/okou-ai/okou/actions/runs/30157827913) - `test-api (4)`, three `registry-1.docker.io` timeouts
- [run 30162893832](https://github.com/okou-ai/okou/actions/runs/30162893832) - `test-api (3)`, same failure, ejected a PR from the merge queue

The toolchain images are already served from GHCR and pull reliably in the same jobs, so mirroring removes the last
Docker Hub dependency from the critical path.

## What gets published

`.github/workflows/mirror.yml` resolves the upstream tag to a digest, copies the whole multi-arch index
(`linux/amd64` and `linux/arm64`) with `docker buildx imagetools create`, then verifies the mirrored digest matches
upstream. Nothing is rebuilt, so the bits are identical to what Docker Hub serves.

Tags published:

- `ghcr.io/okou-ai/pg17:pg17` - tracks upstream `pg17`
- `ghcr.io/okou-ai/pg17:pg17-<YYYYMMDD>` - immutable snapshot of each mirror run

The workflow runs weekly and on manual dispatch. Use the `source_tag` input to mirror a different upstream tag.

## Usage

```yaml
services:
  postgres:
    image: ghcr.io/okou-ai/pg17:pg17
```
