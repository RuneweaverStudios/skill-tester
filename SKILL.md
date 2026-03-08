---
name: skill-tester
displayName: Skill Tester
version: 1.1.0
description: Run local tests for OpenClaw skills before publishing. Validates structure, metadata, syntax, and configured test commands with configurable timeouts.
---

# Skill Tester

Run robust local tests on your OpenClaw skills before uploading to ClawHub. Discovers skills in `workspace/skills`, runs test commands (from `skill_tests.json` or heuristics), and outputs a pass/fail report.

## When to use

- Before publishing skills to ClawHub: run `--all` to verify every skill.
- After changing a skill: run `--skill SLUG` to test that one.
- In CI or as a sub-agent task: use `--json` for machine-readable report.

## Commands (use absolute path for exec from any cwd)

```bash
python3 <skill-dir>/scripts/skill_tester.py --list
python3 <skill-dir>/scripts/skill_tester.py --all [--json] [-v]
python3 <skill-dir>/scripts/skill_tester.py --skill <slug> [--json] [-v]
```

- **--list** — List discoverable skills (have SKILL.md or _meta.json). No tests run.
- **--all** — Run tests for every discovered skill.
- **--skill SLUG** — Run tests only for the given skill (e.g. agent-swarm, gateway-guard).
- **--json** — Output a JSON report: `{ "skills": { ... }, "passed": N, "failed": M }`. Exit 1 if any failed.
- **-v / --verbose** — Print each test name and result per skill.
- **--timeout SECONDS** — Default timeout for test commands (default: 30).

## Test config

Tests are defined in `scripts/skill_tests.json` (next to `skill_tester.py`). Each key is a skill slug; value is a list of test objects:

- **name** — Test label in output.
- **cmd** — List of command + args (e.g. `["python3", "scripts/router.py", "spawn", "--json", "hello"]`).
- **cwd** — Optional subdir of the skill (default: skill root).
- **expect_exit** — Expected process exit code (default 0).
- **expect_json_keys** — Optional list of keys that must exist in stdout JSON.
- **timeout** — Seconds (default 25).

If a skill has no entry in `skill_tests.json`, the tester runs a multi-level heuristic:

1. **Structure check**: Verifies SKILL.md and _meta.json exist
2. **Metadata validation**: Parses _meta.json and checks for required fields (name, version, description)
3. **Syntax check**: Runs `py_compile` on all Python scripts in `scripts/`
4. **Execution check**: For each `scripts/*.py`, tries `--help` or `--json` and treats success as pass

## Requirements

- `OPENCLAW_HOME` set (or `~/.openclaw`) with `workspace/skills/` containing skill dirs.
- Python 3.6+.
- Skills under test must have their own dependencies (e.g. gateway-guard needs `openclaw` CLI for full ensure; status --json can still be tested).

## Exit codes

- 0 — All tested skills passed.
- 1 — At least one test failed.
- 2 — Usage error (e.g. missing --skill/--all, or skills dir not found).
