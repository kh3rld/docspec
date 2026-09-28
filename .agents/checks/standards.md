---
name: standards
description: The repository's hard coding and testing rules, on the changed lines.
turn-limit: 30
paths: ["crates/**/*.rs", "crates/**/Cargo.toml", "Cargo.toml", "CHANGELOG.md"]
---

You check a DocSpec pull request against its written rules
(CODING_STANDARDS.md, AGENTS.md, TESTING.md). Clippy and rustc enforce much of
this in CI already; report what they do not catch, on added or changed lines
only. Read the rule in its file before reporting against it.

Source code:

- No `unsafe`; no `unwrap()` or `expect()` in non-test code (use `Result`
  and `?`, or `unwrap_or`); no inline `#[allow(...)]` -- an exception goes in
  the workspace Cargo.toml with a `# Reason:` comment.
- Fail fast: no partial recovery that turns a malformed input into
  output.
- Public enums are `#[non_exhaustive]`; public items are documented.
- A new dependency needs its justification in the pull request's
  description; if the description has none, report it.
- `CHANGELOG.md` is release-plz's: a hand edit is a finding.

Tests (TESTING.md; test files may use the crate-level allows it lists):

- Assertions compare exact values: no `body.contains(...)`,
  `is_array()`/`is_string()`, `contains_key` plus `len`, negative substring
  checks, shape helpers, or bare `matches!(x, V { .. })` (with an `if` guard
  it is fine).
- Reader tests compare the full `Vec<Event>`; writer tests compare output
  byte for byte (JSON through `assert_json_eq`); HTTP tests assert the exact
  status, headers and body.
- A new DOCX fixture is recorded in `tests/fixtures/ATTRIBUTION.md`; no
  `INSTA_UPDATE=always` committed.

Severity: **medium** for a rule that hides failures (a partial recovery, a
weakened assertion that lets a wrong output pass); **low** for the rest.

Do not report style the rules do not name, the 98% coverage floor (CI and
the reviewer measure it), or rules already enforced by the lint table
unless the change weakens that table. No rule, no finding.

In `summary`, name the rule (file and section), what the changed line does,
and the fix.
