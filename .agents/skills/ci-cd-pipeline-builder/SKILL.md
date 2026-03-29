# CI/CD Pipeline Builder - POWERFUL Tier Skill

## Overview

Automates CI/CD pipeline creation by detecting project characteristics rather than relying on assumptions. Generates pragmatic GitHub Actions or GitLab CI configurations tailored to the actual stack.

## Core Purpose

Two primary workflows: **stack detection** and **pipeline generation**.

Reads repository files to identify language, runtime, and tooling signals — lockfiles, language manifests, build scripts — then generates appropriate pipeline YAML with caching and matrix strategies.

## Key Safeguards

- Confirm commands exist in project (`test`, `lint`, `build`) before adding them to pipeline steps
- Start with CI-only stages before adding production deployment
- Keep deploy jobs gated by protected branches/environments
- Never hardcode secrets in YAML — always use CI secret stores
- Validate generated YAML parses correctly before committing

## Workflow

1. **Detect stack** — scan lockfiles, manifests, Dockerfiles
2. **Generate pipeline** — produce GitHub Actions / GitLab CI YAML
3. **Validate** — ensure referenced commands exist, cache keys are correct
4. **Add deployment stages** — only after CI stages are proven stable
5. **Iterate** — add optimizations incrementally, not all at once

## Generation Strategy

- Version generated baselines in git from day one
- Use matrix strategy for multi-version testing
- Align cache strategy with package manager (pip, npm, poetry)
- Reproducible builds: pin action versions with SHA, not tags

## Platform Decision

| Signal | Use |
|--------|-----|
| GitHub repo | GitHub Actions |
| GitLab repo | GitLab CI |
| Either | GitHub Actions (default) |

## Cross-References

- **dependency-auditor** — add vulnerability scanning as a pipeline gate
- **verification-before-completion** — validate pipeline runs before claiming done
- **architecture-patterns** — infrastructure and deployment boundary design
