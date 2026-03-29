---
name: "dependency-auditor"
description: "Comprehensive dependency auditing — vulnerability scanning, license compliance, outdated detection, upgrade planning across Python and Node.js ecosystems."
---

# Dependency Auditor - POWERFUL Tier Skill

## Overview

Analyzes, audits, and manages dependencies across multi-language projects. Identifies vulnerabilities, ensures license compliance, detects unused deps, and plans safe upgrades.

## Core Capabilities

### 1. Vulnerability Scanning
- Scans against CVE databases with CVSS scores
- Analyzes transitive dependency vulnerabilities
- Multi-ecosystem support: `requirements.txt`, `pyproject.toml`, `poetry.lock`, `package.json`, `package-lock.json`, `yarn.lock`

### 2. License Compliance
- **Permissive**: MIT, Apache 2.0, BSD, ISC
- **Copyleft (strong)**: GPL v2/v3, AGPL v3 — flag for distribution review
- **Copyleft (weak)**: LGPL, MPL
- Detects GPL contamination in permissive projects

### 3. Outdated Dependency Detection
- Categorizes updates: patch / minor / major
- Identifies abandoned or unmaintained packages
- Tracks last release dates and EOL status

### 4. Dependency Bloat
- Detects unused imports/packages
- Identifies redundant deps with overlapping functionality
- Maps bundle size impact

### 5. Upgrade Path Planning
- Low risk: patch updates, security fixes
- Medium risk: minor updates
- High risk: major version updates with API changes
- Provides rollback strategies

### 6. Supply Chain Security
- Verifies package signatures and checksums
- Detects typosquatting and suspicious packages
- Tracks ownership changes and maintainer shifts

## Scanning Frequency

| Scan Type | Frequency |
|-----------|-----------|
| Security scans | Every commit or daily |
| License audits | Weekly |
| Upgrade planning | Monthly |
| Full audit | Quarterly |

## CI/CD Integration

```bash
# Security gate — fail build on high/critical CVEs
python dep_scanner.py /project --format json --fail-on-high

# License compliance check
python license_checker.py /project --policy strict

# Pre-commit quick scan
python dep_scanner.py . --quick-scan
```

## Risk Management
1. Address high/critical CVEs immediately
2. Verify license compliance before shipping
3. Update dependencies incrementally, not all at once
4. Test thoroughly after updates
5. Monitor continuously in CI

## Cross-References

- **ci-cd-pipeline-builder** — add dep auditing as a pipeline gate
- **systematic-debugging** — diagnose issues introduced by dependency updates
- **verification-before-completion** — run audit before marking work done
