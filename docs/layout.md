# Manifest layout and precedence

Status: **Proposal (Phase 0).** None of these directories exist yet; they are
created when they receive their first real file (no empty placeholder folders).
File formats are defined by the cross-repo schemas in the SSTRE umbrella
`schemas/` directory, primarily `asset-recipe.schema.json`.

## Directories

| Directory | Contents | Edited by | Committed |
|---|---|---|---|
| `defaults/` | Global default processing settings that apply to every asset of a kind (for example "diffuse textures: conventional super-resolution ×4, no diffusion") | Humans | Yes |
| `materials/` | Material profiles: reusable settings for a class of surface (stone, metal, foliage, sky, UI, technical mask, normal map ...) | Humans | Yes |
| `mappings/` | HD → Classic asset correspondences, with confidence and evidence; also records known-unmatched and ambiguous assets | AssetCompiler proposes, humans confirm | Yes |
| `games/tfe/`, `games/tse/` | Per-game data: supported editions and expected source hashes, game-level defaults that differ from `defaults/`, game-specific material assignments | Humans | Yes |
| `generated/` | Machine-written per-asset recipes (development-time classification) and frozen lock manifests for release builds | Tools only | Yes, after review |
| `overrides/` | Curated per-asset exceptions for prominent or problematic assets; curated override packs for asset layer 7 (see below) | Humans | Yes |
| `layers/configs/` | Asset-layer build configurations (`build-config.schema.json`): which ADR-0009 layers are on, the AI quality profile, the platform profile, and approvals and packs referenced by version and hash | Humans | Yes |
| `layers/profiles/` | Platform profiles (`platform-profile.schema.json`) and AI quality profiles beyond the AssetCompiler built-ins | Humans | Yes |
| `approvals/` | Per-asset approvals for the opt-in HD Artwork and HD Interface layers (`layer-approvals.schema.json`) | Owner and curators | Only after GATE-05 (see below) |
| `community/` | Index of opt-in community override packs: pack ID, version, index hash and where to obtain it. Never the pack content | Humans | Yes |

Nothing in this repository contains asset bytes, extracted images, AI outputs or
model weights. Assets are referenced by game, in-archive path, stable asset ID
and source hash.

## Asset layers (ADR-0009)

The SSTRE umbrella ADR-0009 decides *which source* supplies each asset through
seven layers in a fixed order. This repository holds the data for those layers.
The AssetCompiler (`sstre-ac plan`) resolves it into a build manifest:

| Order | Layer | Data in this repository |
|---|---|---|
| 1 | Classic Base | none (always on) |
| 2 | HD Quality Improvements | none: decided per asset by the source selector; its records stay in the local workspace until GATE-05 |
| 3 | HD Material Maps | none (follows the selected albedo) |
| 4 | HD Artwork | opt-in switch in `layers/configs/`; per-asset approvals in `approvals/` |
| 5 | HD Interface (menus, HUD, Netricsa) | its own opt-in in `layers/configs/`; approvals in `approvals/` |
| 6 | AI Enhancement | quality profile in `layers/configs/` or `layers/profiles/`. AI outputs are never stored here; packs are referenced by ID, version and hash |
| 7 | Community and Curated Overrides | curated packs in `overrides/`, community pack references in `community/` |

Platform profiles (`layers/profiles/`) are independent of the layers. They set
texture size limits, formats, mip policy, memory budget and preprocessing for
one target.

Rules for this data:

- Every reference to an approvals file or a pack carries its version and
  `sha256` (a JCS hash; `sstre-ac validate-config` prints it), so a build
  configuration identifies its inputs exactly.
- Release builds read only the frozen build manifest (`build-manifest.schema.json`).
  Manifests worth keeping go to `generated/` like other lock manifests.
- **GATE-05.** Approvals, curated override packs and build manifests name
  proprietary asset IDs, and often HD candidate IDs and hashes. Until GATE-05
  clears publication, they live in a local, uncommitted mirror of this layout,
  for example `<workspace>/sstre-manifests-local/approvals/`. Only synthetic
  examples and asset-free files (configurations, profiles) may be committed
  before then.
- Licensing follows [`LICENSE.md`](../LICENSE.md):
  - **CC0-1.0** for factual configuration: `layers/` (which layers and
    profiles a build uses, technical limits and formats), `community/`
    (references to packs) and `generated/` build manifests;
  - **CC-BY-SA-4.0** for curated choices: `approvals/` (per-asset artistic
    approvals) and `overrides/`.
- Community pack content keeps its own licence and is never committed here.

## Precedence

When a recipe is resolved for one asset, layers are applied from lowest to
highest priority; a higher layer overrides fields set by a lower one
(plan §4: curated override > generated per-asset recipe > material profile >
global defaults):

| Priority | Layer | Source |
|---|---|---|
| 1 (highest) | Curated override | `overrides/` |
| 2 | Generated per-asset recipe | `generated/` |
| 3 | Material profile | `materials/` (profile selected by the asset's material assignment) |
| 4 | Game-level defaults | `games/<game>/` |
| 5 (lowest) | Global defaults | `defaults/` |

Notes on the proposal:

- Layer 4 (game-level defaults) is an addition to the four layers named in the
  plan. It only refines layer 5 and never outranks a material profile. If it is
  not needed in practice it can be dropped without affecting the others.
- `mappings/` is not a recipe layer: it decides *which source file* feeds an
  asset (HD or Classic fallback), not *how* it is processed. Source choice is
  governed by the asset layers above; the recipe precedence in this section
  applies to the AI Enhancement layer's processing of the chosen source.
- Merge semantics (whether nested objects merge or replace, how lists combine)
  are defined by `asset-recipe.schema.json` and the resolver, not by convention.
  Until defined, treat each top-level field as replaced wholesale.
- For release builds the fully resolved recipe per asset is frozen into a lock
  manifest under `generated/`, together with workflow and model identifiers
  and hashes. Release builds read only the lock; they never re-resolve layers
  or call a live model.

## Rules

- No executable code, scripts or hooks. Tools that read or write this data live
  in SSTRE-AssetCompiler and Remaster-Toolkit.
- `generated/` is written by tools and reviewed in pull requests like any other
  change; hand edits there are lost on the next generation, so fix the input
  layer or add an override instead.
- Serialize canonically (UTF-8, LF, sorted keys) so diffs and hashes are stable.
- Every file declares the schema version it conforms to.

## Open questions

1. Is publishing per-asset paths and source hashes of proprietary game data
   acceptable, or must mappings be keyed differently? To be answered by the
   licensing review (SSTRE `docs/research/RD-007-licensing.md`).
2. JSON or YAML as the authoring format (YAML is easier to hand-edit; JSON is
   what the schemas validate directly). Locks should be JSON either way.
3. Whether TFE and TSE share most material profiles (likely) and how shared
   per-asset entries are deduplicated without losing per-game metadata.
