# GitHub Copilot / AI Agent Instructions

Purpose: Short, actionable notes to help an AI coding agent be immediately productive working on the Common API Stream spec.

## Big picture (what this repo is)
- This repo contains OpenAPI v3.0/v3.1 specifications for the API interface of a dedicated topic according to the file name (e.g. account, cards, mortgage, pension etc.). Primary sources are under `src/` (split components). Top-level YAMLs (e.g. `streamtopicAPI.yaml`) are generated/bundled versions used for publishing.
- Design rationale, API levels and naming conventions are documented in the .github wiki and the repo wiki (see descriptions and requirements).

## Key files & structure (quick map)
- `src/*.yaml` — canonical, split specifications; components live in `src/components/{schemas,parameters,headers,responses,...}`.
- Top-level API spec e.g. `cardInfoAPI-Level1.yaml` (at repo root) — bundled artifacts used by consumers. Avoid editing generated files directly.
- `.github/workflows/*` — CI checks (yaml & openapi linting, bundling, release).

## Workflows & checks (how PRs are validated)
- PRs trigger the `bundle` job first (uses `swissfintechinnovations/.github` reusable workflow). The bundle output must match the PR HEAD; subsequent lint jobs only run if bundle succeeds and commit-SHA matches.
- Linting: `lint-yaml.yaml` and `lint-openapi.yaml` call reusable workflows in `swissfintechinnovations/.github` (inspect that repo to see exact linters/versions used).
- Bundle job caution: Do not rename `bundle-spec.yaml` since other workflows depend on its filename.
- Releases use a reusable release workflow and are gated by a small set of maintainers (see `.github/workflows/release.yaml`).

## Editing rules and patterns (concrete, reproducible rules)
- Make edits in `src/` (component files). The bundler composes these into the single-level specs; tests/lint run against the bundled output.
- When adding or renaming schemas/parameters: update the file in `src/components/...` and ensure any file-based `$ref:` (e.g. `./components/schemas/Foo.yaml`) matches the new path.
- Expect bundling to rewrite refs.
- Error responses follow RFC7807: `application/problem+json` and use `src/components/responses/standard400.yaml` and `standard500.yaml`; include headers like `X-Correlation-ID` and `Content-Language`.
- Header/param conventions: client/correlation/agent headers are defined under `src/components/parameters/header` (e.g. `client.yaml`, `correlation.yaml`, `agent.yaml`) and should be referenced consistently.

## How to validate locally (investigate first)
- The repo relies on reusable workflows for linting; inspect `github.com/swissfintechinnovations/.github` for exact commands. Typical local equivalents are `yamllint` + OpenAPI linters (e.g., Spectral/OpenAPI CLI), but verify versions and rules in the reusable workflow.
- To preview a spec, point Swagger Editor to the hosted/raw URL (README includes an example for Level 1): `https://editor.swagger.io/?url=<raw-file-url>`.

## PR review checklist for agents
- Changes are in `src/` (not directly in the generated top-level file).
- All new schemas/params have descriptive titles and follow the naming pattern where applicable (see .github wiki).
- `$ref` paths are correct for the file layout and remain valid after bundling (run the bundler workflow locally or via CI to confirm).
- Responses still include standard `400/500` responses and headers as applicable.
- No breaking semantic changes to tags, paths, or required fields without a clear changelog entry.

## Where to look for more detail
- `.github/workflows/*.yaml` (exact CI behavior and guard rails) ✅
- `src/components/*` for canonical schema/parameter/response patterns ✅
- `https://github.com/swissfintechinnovations/.github` (reusable workflows implementing lint/bundle/release) ✅

If anything above is unclear or you'd like more examples (e.g. a specific ref/rename workflow or the exact local lint commands), tell me which part to expand and I'll iterate. 🙏
