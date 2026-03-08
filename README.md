# skill-tester

**OpenClaw skill: automated skill testing and validation before publishing.**

Discovers skills in your workspace, validates structure and metadata, runs configured or heuristic tests, and outputs pass/fail reports.

## Features

- Discover all skills in `workspace/skills/`
- Validate required files (SKILL.md, _meta.json) and metadata fields
- Python syntax checking for all scripts
- Configured test commands via `skill_tests.json`
- Heuristic testing for skills without explicit test configs
- JSON output for CI integration
- Configurable timeouts per test or globally via `--timeout`

## Install

```bash
clawhub install skill-tester
# or:
git clone https://github.com/RuneweaverStudios/skill-tester.git
cp -r skill-tester ~/.openclaw/workspace/skills/
```

## Quick start

```bash
# List discovered skills
python3 scripts/skill_tester.py --list

# Test all skills
python3 scripts/skill_tester.py --all

# Test a specific skill
python3 scripts/skill_tester.py --skill gateway-guard

# JSON report with custom timeout
python3 scripts/skill_tester.py --all --json --timeout 60
```

## Requirements

- Python 3.6+
- `OPENCLAW_HOME` set (or default `~/.openclaw`) with `workspace/skills/`

## License

MIT
