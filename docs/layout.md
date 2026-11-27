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
| `overrides/` | Curated per-asset exceptions for prominent or problematic assets | Humans | Yes |

Nothing in this repository contains asset bytes, extracted images, AI outputs or
model weights. Assets are referenced by game, in-archive path, stable asset ID
and source hash.

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
  asset (HD or Classic fallback), not *how* it is processed.
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
