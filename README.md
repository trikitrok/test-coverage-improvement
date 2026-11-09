# Test Coverage Improvement

A Codex skill for assessing and improving whether production behavior is adequately protected
by tests before a behavior-preserving refactoring.

The skill combines:

- direct, indirect, and integration-test inspection;
- targeted coverage analysis for statements, branches, functions, lines, and
  observable outcomes; and
- targeted mutation testing after relevant branch coverage is strong enough.

It also provides a decision gate: proceed when the behavior is adequately
covered, explain accepted mutation survivors, or stop and request
characterization tests when coverage is insufficient.

## Repository layout

```text
.
├── README.md
└── skills/
    └── test-coverage-improvement/
        └── SKILL.md
```

`SKILL.md` is the skill entry point. Its YAML frontmatter supplies the skill
name and discovery description; the Markdown body contains the instructions
loaded when the skill is used. The current skill is intentionally
self-contained, so it does not need `scripts/`, `references/`, `assets/`, or
`agents/` directories.

## Installation

Once this repository is published on GitHub, users can install the skill with
Codex's skill installer:

```text
$skill-installer Install `skills/test-coverage-improvement` from https://github.com/<owner>/<repository>
```

The installer places it in the user's local Codex skills directory and Codex
can then discover it by name. It can also be invoked explicitly as
`$test-coverage-improvement`.

Manual copying into `~/.codex/skills/` is still possible, but is not required.

## Intended use

Use this skill when assessing test protection around a refactoring or another
behavior-preserving production change. It supports assessment, test improvement, and refactoring decisions;
it does not impose a universal coverage percentage and does not recommend
blindly adding tests just to improve a mutation score.

## Validation

The skill can be checked with the Codex skill validator:

```bash
python /path/to/codex/skills/.system/skill-creator/scripts/quick_validate.py \
  skills/test-coverage-improvement
```

## License

This project is licensed under the [MIT License](LICENSE).
