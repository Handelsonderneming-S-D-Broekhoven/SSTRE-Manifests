# Licensing of SSTRE-Manifests

Copyright © 2026 Handelsonderneming S.D. Broekhoven, trading as ITEagle Europe, and contributors (each contributor retains copyright in their own contributions).

This repository contains data, not program code. It uses two licenses, selected **by kind of content**:

| Kind of content | License | Where | Why |
|---|---|---|---|
| **Factual manifest metadata**: stable asset identifiers, archive-relative paths, source hashes, dimensions and format facts, edition fingerprints, HD→Classic mapping records with evidence and confidence, machine-generated per-asset recipes and frozen lock manifests, schemas | [CC0-1.0](LICENSES/CC0-1.0.txt) | `schemas/`, `mappings/`, `generated/`, `games/<game>/editions/`, `layers/` (asset-layer build configurations, platform and quality profiles), `community/` (references to community packs) | Facts and mechanical output carry little or no copyright. A public-domain dedication lets anyone, including other tools and other licenses, reuse them without friction |
| **Original creative recipes and curated configuration**: global defaults, material profiles, curated per-asset overrides, prompts and negative prompts written by people, game-level processing choices, documentation | [CC-BY-SA-4.0](LICENSES/CC-BY-SA-4.0.txt) | `defaults/`, `materials/`, `overrides/`, `approvals/` (per-asset approvals for the opt-in HD layers), the rest of `games/<game>/`, `docs/`, `README.md`, and anything not listed in the row above | These encode curatorial and artistic judgement. Attribution and share-alike keep improvements open. CC-BY-SA-4.0 is one-way compatible with GPLv3 |

The machine-readable assignment lives in [`REUSE.toml`](REUSE.toml). A file may declare a different license with an `SPDX-License-Identifier` header or a `.license` sidecar if a reviewer agrees it belongs to the other category.

## What this repository never licenses

- **Third-party game content.** File names, paths and hashes that refer to *Serious Sam* game files are factual references. Including them grants no rights in, and claims no ownership of, the referenced content, which belongs to its respective rights holders. No asset bytes, extracted images, AI outputs or model weights are ever committed (SSTRE ADR-0002).
- Per owner decision of 2026-11-27, asset identifiers, paths and hashes of proprietary game files are **published only after their provenance and the applicable restrictions have been reviewed** (SSTRE backlog GATE-05). Until then they are used in local workspaces only.

See also SSTRE [ADR-0006](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE/blob/main/docs/adr/0006-licensing-boundaries.md).
