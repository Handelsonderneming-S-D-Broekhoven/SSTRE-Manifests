# Contributing to SSTRE-Manifests

This repository follows the shared SSTRE contribution guide:
**[SSTRE CONTRIBUTING.md](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE/blob/main/CONTRIBUTING.md)**
(commit conventions, sign-off, branch naming, ADR process, the no-game-data rule
and the AI-generated-asset rule). Security reports: see the umbrella
[SECURITY.md](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE/blob/main/SECURITY.md).

Repository-specific notes:

- This repository holds **declarative data only**. Do not add scripts or executable hooks; tooling lives in [SSTRE-AssetCompiler](https://github.com/Handelsonderneming-S-D-Broekhoven/SSTRE-AssetCompiler) and [Remaster-Toolkit](https://github.com/Handelsonderneming-S-D-Broekhoven/Remaster-Toolkit).
- Manifests reference game assets by path and hash; they never embed asset content. See [docs/layout.md](./docs/layout.md) for where each kind of file belongs.
- Hand-edit `overrides/`, `materials/`, `defaults/` and `mappings/`; never hand-edit `generated/`.
- Never commit game files, extracted assets, AI derivatives of game assets, model
  weights or secrets. The [`.gitignore`](./.gitignore) blocks the common cases; it is a
  safety net, not permission.

Licensing: `CC0-1.0` (factual metadata) / `CC-BY-SA-4.0` (creative recipes and curation); see [LICENSE.md](LICENSE.md). Sign every commit off under the DCO (`git commit -s`).
