# layer-vectorchord

The [VectorChord](https://github.com/tensorchord/VectorChord) vector-similarity
extension installed into the OpenCharly PostgreSQL layer.

The `vectorchord` candy downloads the per-Postgres-major release zip and installs
the `vchord.so` shared library into the server's `pkglibdir`, plus the
`vchord.control` manifest and `vchord--*.sql` migration files into the extension
`sharedir`. It then sets `POSTGRES_SHARED_PRELOAD_LIBRARIES=vchord.so` so the
running server preloads it. VectorChord provides the `vchordrq` index type —
an approximate nearest-neighbor search that has no result cap, unlike pgvector's
`ivfflat`.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `vectorchord` |
| Version | `1.1.1` (`VECTORCHORD_VERSION` env) |
| Artifacts | `vchord.so` (pkglibdir), `vchord.control` + `vchord--*.sql` (sharedir/extension) |
| Env | `POSTGRES_SHARED_PRELOAD_LIBRARIES=vchord.so` |
| Requires | `pod-postgresql` |
| Packages | `unzip` (arch / fedora) |
| Service / port | none |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list, alongside the
PostgreSQL layer:

```yaml
my-immich-box:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/pod-postgresql'
      - '@github.com/opencharly/layer-vectorchord:v2026.251.1508'
      - immich
```

Then, against the running server:

```sql
CREATE EXTENSION vchord CASCADE;
```

The candy's `plan:` asserts the `vchord.control` manifest in the extension
sharedir, the `vchord.so` library beside the server's `plpgsql.so`, an
`agent-check` that `CREATE EXTENSION vchord` succeeds against the running server,
and the `POSTGRES_SHARED_PRELOAD_LIBRARIES` env at runtime.

## Layout

- `charly.yml` — the `vectorchord:` candy entity (the `require:`, the
  `VECTORCHORD_VERSION` env/var, the download-and-install `run:` step, the
  `check:` assertions) and the embedded `vectorchord-skill:` skill entity.
- `CHANGELOG/` — per-CalVer release history.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-infrastructure:vectorchord`
- `/charly-infrastructure:postgresql` — required dependency (server + pgvector)
- `/charly-immich:immich` — primary consumer (its migration script creates the extension)
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
