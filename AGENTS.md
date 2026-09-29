# AGENTS.md — layer-vectorchord

Standalone candy repo for the `vectorchord` layer — the VectorChord vector-search
extension installed into the PostgreSQL layer. The candy lives in `charly.yml` at
the repo root: the `require:` on `pod-postgresql`, the `VECTORCHORD_VERSION`
env/var, the download-and-install `run:` step, the `check:` assertions, and the
embedded `skill:` entity projected into the marketplace corpus as
`/charly-infrastructure:vectorchord`.

Canonical files:

- `charly.yml` — the `vectorchord:` candy entity and the `vectorchord-skill:`
  skill entity.
- `CHANGELOG/` — per-CalVer release history; read it before changing baked
  checks.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:vectorchord` — the owning skill. The extension install,
  the `vchordrq` index type, and the `shared_preload_libraries` contract. Load
  before editing or troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:` and `agent-check:`, per-distro `distro:`
  arms, package/repo sections, service declarations). Load before editing any
  entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence. The install
  step detects the Postgres major version and the pkglibdir/sharedir at build
  time — keep those detection paths valid on both Fedora and Arch layouts.
- `VECTORCHORD_VERSION` is duplicated between `env:` and `var:` (build-time vs
  runtime); keep the two in sync.

## Modify this repo

- Edit the `vectorchord:` candy entity AND the `vectorchord-skill:` skill entity
  in `charly.yml` together. The skill is the projected usage source, so a
  behaviour change not mirrored in the skill leaves the corpus stale.
- A version bump moves `env.VECTORCHORD_VERSION`, `var.VCHORD_VERSION`, the
  download URL template, and the `check:` assertions together.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
