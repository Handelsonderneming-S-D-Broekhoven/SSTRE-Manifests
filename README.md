# SSTRE-Manifests

Declarative asset recipes, mappings, curated overrides and generated lock
manifests for **SSTRE — Serious Sam: The Raytraced Encounter**, an unofficial,
open-source modernization of Serious Sam: The First Encounter and The Second
Encounter. Independent of Croteam and Devolver Digital.

## Status

**Phase 0 — bootstrap and research. No manifests exist yet.** The directory
layout and recipe precedence are a proposal in [docs/layout.md](./docs/layout.md);
directories are created when they receive their first real file. File formats
are being drafted as JSON Schemas in the SSTRE umbrella `schemas/`.

## Role

This repository answers *how each asset should be processed*, as data:

- global defaults and material profiles;
- HD → Classic asset mappings, including known-unmatched assets;
- per-game data for TFE and TSE;
- machine-generated per-asset recipes (development time only);
- curated overrides for prominent or problematic assets;
- frozen lock manifests that make release builds reproducible.

Precedence: curated override > generated per-asset recipe > material profile >
global defaults (details and the proposed game-level layer in
[docs/layout.md](./docs/layout.md)).

### Non-goals

- Executable code. Tools that read and write this data live in
  [SSTRE-AssetCompiler](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE-AssetCompiler)
  and [Remaster-Toolkit](https://github.com/Handelsonderneming-S-D-Broekhoven/Remaster-Toolkit).
- Asset content of any kind: no game files, extracted images, AI outputs or
  model weights. Assets are referenced by path, stable ID and hash.

## Architecture and dependencies

- Depends on: cross-repo schemas in the
  [SSTRE umbrella](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE) `schemas/`
  (pinned by commit).
- Read by: SSTRE-AssetCompiler and Remaster-Toolkit; written by curators and,
  for `generated/`, by tools.
- Boundaries, interfaces and pinning: SSTRE
  [`docs/architecture/cross-repo-contracts.md`](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE/blob/main/docs/architecture/cross-repo-contracts.md).

## Prerequisites

| Item | Status |
|---|---|
| Schemas (`asset-recipe.schema.json` and related) | Drafts in progress in the umbrella |
| Authoring format (JSON vs. YAML) | **TBD**; locks will be JSON |
| Supported game editions and source hashes | **TBD** — research in SSTRE `docs/research/RD-002-hd-assets.md` |

## Development

There is no data to validate yet. What works today:

```sh
git clone https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE-Manifests.git
git clone https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE.git
# Link and forbidden-file checks for this repository
# (requires Python 3.10+; see SSTRE CONTRIBUTING.md):
python SSTRE/scripts/validate_repo.py --root SSTRE-Manifests --skip-schemas
```

Validation of manifest files against the umbrella schemas will be added when
the first manifests and accepted schemas exist.

## Licensing

Copyright © 2026 Handelsonderneming S.D. Broekhoven, trading as ITEagle Europe, and contributors.

Two licenses, by kind of content — see [`LICENSE.md`](LICENSE.md) for the exact distinction:

- **`CC0-1.0`** for factual manifest metadata (identifiers, paths, hashes, mappings, generated locks, schemas).
- **`CC-BY-SA-4.0`** for original creative recipes, material profiles, curated overrides and documentation.

References to game files grant no rights in that content. Asset identifiers, paths and hashes of proprietary game files are published only after provenance and restrictions are reviewed.


## Related repositories

- [SSTRE](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE) — umbrella, documentation, release pins
- [SSTRE-Engine](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE-Engine) — engine fork and runtime
- [SSTRE-AssetCompiler](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE-AssetCompiler) — Serious Sam-specific extraction, mapping and packaging
- [Remaster-Toolkit](https://github.com/Handelsonderneming-S-D-Broekhoven/Remaster-Toolkit) — game-agnostic processing toolkit

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).
