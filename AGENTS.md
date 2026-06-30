# AI Agent Instructions

## Stream specific information
> Stream/topic: `<<FILL IN: e.g. payment, mortgage, card, pension, wealth>>`  
> Repo: `swissfintechinnovations/ca-<<topic>>` · OpenAPI version: `<<FILL IN: 3.0 or 3.1>>`  
> Bundled spec at repo root: `<<topic>>API.yaml`  
> Repo with config files, reusable workflows, and wiki: `swissfintechinnovations/.github`  

## What you are allowed to edit
- **Edit only** the split source components under `src/components/{schemas,parameters,headers,responses,...}`.
- **Do not edit** the bundled root file — it is generated from `src/*` by the Redocly bundle workflow on PR. Editing it directly will be overwritten.
- **Do not touch** anything in `.github/` or the reusable workflows in `swissfintechinnovations/.github`.

## Editing rules and patterns
- Only do small, focused changes, not large refactors.
- Prefer optional additive changes over modifying existing semantics.
- When adding or renaming schemas/parameters: update the file in `src/components/...` and ensure any file-based `$ref:` (e.g. `./components/schemas/Foo.yaml`) matches the new path.
- Expect bundling to rewrite refs.
- Error responses follow RFC7807: `application/problem+json` and use `src/components/responses/standard400.yaml` and `standard500.yaml`; include headers like `X-Correlation-ID` and `Content-Language` where applicable.
- Header/param conventions: client/correlation/agent headers are defined under `src/components/parameters/header` (e.g. `client.yaml`, `correlation.yaml`, `agent.yaml`) and should be referenced consistently.

## Naming convention & style guide
The full rules are in the `swissfintechinnovations/.github` wiki — **read these once if you have web access**, otherwise follow the conventions already visible in this repo.

Hard rules for this repo:
- `camelCase` for property names, `PascalCase` for schema/type names, `kebab-case` for URL path segments.
- Boolean fields as affirmative statements (`isActive`, `hasConsent`).
- Singular schema names, plural collection/endpoint names.
- Enum values: readable, stable, explicitly documented; don't reorder or rename existing ones.
- Every new schema/parameter needs a descriptive title, a description and an example.
- When unsure, match the pattern already used in this repo rather than inventing a new one.

## Schema Design Guidelines
- Preserve backward compatibility whenever possible. Do not introduce breaking API changes without explicit versioning discussion.
- Keep schemas reusable and avoid duplication.

## Workflows
- There are various workflows (see swissfintechinnovations/.github wiki `Github Actions`); for working with the repo, only the following are important:
    1. SFTI Bundle Workflow: bundles files in `src/*` to a full OAS compliant API specification on root level `/`
    2. SFTI Linter Workflows: Checking the files against the SFTI naming convention, SFTI style guides and OAS specifications (SFTI Lint PRs, SFTI Lint Specifications: OpenAPI Compliance, SFTI Lint Specifications: Yaml Compliance)
- Linting runs against source files and the bundled output. `lint-yaml.yaml` and `lint-openapi.yaml` call reusable workflows in `swissfintechinnovations/.github` (inspect that repo to see exact linters/versions used).

## Local checks before opening a PR
Before pushing, check Linter rules and run local commands, if possible:
- Style Guide: [wiki page](https://github.com/swissfintechinnovations/.github/wiki/Style-Guide-Common-APIs)
- Naming Conventions: [wiki page](https://github.com/swissfintechinnovations/.github/wiki/Naming-Conventions)
> Confirm the exact scripts against the workflow files — adjust if they differ.
- Bundle:  `npx @redocly/cli bundle --config .github/redocly.yaml`
- Lint OpenAPI:  `npx @redocly/cli lint --config=github/.github/redocly.yaml <<topic>>API.yaml`
- Lint yamllint src files: `yamllint -d "{extends: github/.github/.yamllint, rules: {line-length: {max: 170}}}" -f github "<<file>>"`
- Lint yamllint root API files: `yamllint -c github/.github/.yamllint -f github "<<file>>"`
- The same checks run in CI via these workflows (mirror them locally):
  `SFTI Bundle`, `SFTI Lint PRs`, `SFTI Lint Specifications: OpenAPI Compliance`,
  `SFTI Lint Specifications: Yaml Compliance`.
Make sure the bundle command works and fix all linter errors and warnings. 

## PR review checklist for agents
- All new schemas/params have descriptive titles and follow the naming pattern where applicable (see .github wiki).
- `$ref` paths are correct for the file layout and remain valid after bundling (run the bundler workflow locally or via CI to confirm).
- Responses still include standard `400/500` responses and headers as applicable.
- No breaking semantic changes to tags, paths, or required fields without a clear changelog entry.
- run local commands and fix all issues
