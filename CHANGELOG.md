# Changelog

All notable changes to this project are documented in this file.

## [1.1.0] – 2026-06-10

### Added

- MITRE ATT&CK annotations on every attack phase output (`[T1046 | Network Service Scanning]`).
- `MITRE_ATTACK.md` — full ATT&CK coverage matrix for attack simulation and deception capabilities.
- `DISCLAIMER.md` — authorized-use-only policy at repository root.
- `quickstart.sh` — single-command lab startup with health-check wait and ASCII ready screen.
- `scripts/verify.sh` — automated post-startup health check (containers, HTTP endpoints, Elasticsearch).
- `CONTRIBUTING.md` — developer guide covering code conventions, adding attack phases, and PR workflow.
- Structured Python `logging` across all attacker components (replaces `print()` statements).
- Type hints and docstrings on all public methods in attacker, honeypot, and fake-activity modules.
- `mitre.*` fields (`tactic`, `technique_id`, `technique_name`) added to Elasticsearch events via Filebeat processors for SQL injection, command injection, path traversal, SSH brute force, credential probing, and honeytoken access.
- Pre-built Kibana dashboards committed to `dashboards/ndjson/` and auto-imported on stack startup: Attack Timeline, ATT&CK Technique Distribution, Top Attacker IPs, SSH Honeypot Activity, Injection Attacks Detail, Deception Triggers.
- `.github/workflows/ci.yml` — GitHub Actions CI with Python linting (flake8), docker-compose validation, and secret scanning (gitleaks).
- `.gitleaks.toml` — allowlist for intentional lab credentials to prevent CI false positives.
- `docs/deception-levels.rst` — full technical documentation for all four deception levels: architecture, what each level activates, what an attacker observes, and demo recommendations.
- README deception levels table expanded with all 14 capabilities mapped across levels.
- `lateral_movement.py` — Phase 6.5: SSH lateral movement (T1021.004) using credentials from Phase 6 to pivot to other discovered hosts via `paramiko`; wired into `main_attacker.py`.

### Changed

- Attack phase numbering corrected: Phase 4.5 (Port Scanning) renamed to Phase 5; subsequent phases renumbered.
- All bare `except:` clauses replaced with typed `except Exception as e:` with structured error logging.
- `CONTRIBUTING.md` moved from `docs/contributing.rst` to repository root for GitHub discoverability.

## [1.0.0] – 2024

### Added

- Initial release: Cyber Deception Playground.
- Fictitious financial environment (Andesfinance) with vulnerable frontend/backend and MySQL.
- Configurable deception levels: None, Basic, Complete, Impossible.
- SSH honeypot with configurable user database and connection logging.
- Fake activity generator for decoy usage patterns.
- Decoy API endpoints and monitored database columns (Complete/Impossible).
- Advanced deception: modified banners, anti-forensics, tampered executables (Impossible).
- Attacker container with reconnaissance, port scanning, SQL injection, command injection, SSH brute force, data exfiltration.
- Elastic Stack integration (Filebeat, Elasticsearch, Kibana) for centralized logging and dashboards.
- Docker Compose orchestration; startup/shutdown scripts for Linux and Windows.

### License

- MIT License.
