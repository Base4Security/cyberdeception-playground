# Contributing to Cyber Deception Playground

Thank you for your interest in contributing! This guide covers everything you need to get started.

## Prerequisites

- Docker Desktop (v24+) with the Compose plugin
- Python 3.10+ (for linting/testing attacker scripts locally)
- Node.js 18+ (for frontend/backend development)

## Development Setup

```bash
git clone https://github.com/Base4Security/cyberdeception-playground
cd cyberdeception-playground
./quickstart.sh complete          # spin up the full stack
./scripts/verify.sh               # confirm everything is healthy
```

## Code Conventions

### Python (attacker, honeypot, fake-activity)

- Use `logging` — never `print()` for diagnostic output.
- Every public method must have a Google-style docstring.
- Add type hints to all function signatures.
- Handle exceptions explicitly: `except Exception as e: logger.error(...)` — no bare `except:`.
- Pin all dependencies to exact versions in `requirements.txt`.

### JavaScript (frontend, backend)

- Follow the existing Express patterns; do not add new frameworks.
- All intentional vulnerabilities must be marked with an inline `// VULNERABILITY:` comment.
- Do not fix the intentional vulnerabilities — they are the point.

## How to Add a New Attack Phase

1. Create `attacker/attack_scripts/components/<phase_name>.py`.
2. Subclass `BaseAttacker` from `base_attacker.py`.
3. Add a `MITRE_TTPS` entry in `base_attacker.py` for your phase.
4. Call `print_mitre_header("<phase_key>")` at the start of the main method.
5. Register the phase in `main_attacker.py` (import, instantiate, add `ask_continue` block, fix phase number).
6. Update `MITRE_ATTACK.md` with the new TTP row.

## How to Add a New Deception Level

1. Create a new Docker Compose profile in `docker-compose.yml`.
2. Add a MySQL init script `mysql/init-<level>.sql` with the deceptive data.
3. Update `scripts/startup.sh` and `scripts/startup.bat` to handle the new level name.
4. Document the new level in `docs/` and update the capability table in `MITRE_ATTACK.md`.

## Pull Request Workflow

1. Fork the repository and create a feature branch (`feat/my-phase`).
2. Ensure `python3 -m flake8 attacker/` and `docker compose config` pass locally.
3. Open a PR against `main` with a clear description of what the change adds or fixes.
4. Reference any relevant MITRE ATT&CK techniques in the PR description.

## Ethical Use Reminder

All contributions must comply with the [DISCLAIMER](DISCLAIMER.md). This tool is for authorized research and training only. Do not add features designed to evade detection on systems you do not own.
