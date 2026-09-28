This is a Rust workspace (`crates/`, one crate per reader and writer around
`docspec-core`); the Rust toolchain is installed and the locked dependencies'
sources are fetched.

- Search in Rust: `rg -n -t rust 'fn next_event' crates/`, `fd name crates/`.
  For Rust syntax rather than text, ast-grep:
  `ast-grep run -l rust -p 'Event::StartHeading { $$$ }' crates/` or
  `ast-grep run -l rust -p '$X.read_to_end($$$)' crates/`.
- The event model: `crates/docspec-core/src/event.rs`, `types.rs`,
  `traits.rs`, `stack.rs`; each reader and writer's README says which events
  it handles or drops.
- The workspace: `cargo metadata --format-version 1 --no-deps --offline | jq`
  (its crates, features and targets), `cargo tree --offline -i <crate>`.
- A dependency's source, to check what a library call really does (zip,
  quick-xml, pulldown-cmark, html5gum, axum...):
  `~/.cargo/registry/src/*/<crate>-<version>/` (versions are in `Cargo.lock`).
- Tests: `crates/*/tests/`, `#[cfg(test)]` modules, the DOCX corpus under
  `tests/fixtures/docx/` with insta snapshots under `tests/snapshots/`; they
  show the intended contract.
- Do not run `cargo build`, `check`, `test` or `clippy`: CI runs those, and a
  build would use up your turns.
