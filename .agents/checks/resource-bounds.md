---
name: resource-bounds
description: Unbounded memory, depth or time on untrusted input, and buffering where DocSpec streams.
turn-limit: 40
paths: ["crates/docspec-core/src/**", "crates/*-reader/src/**", "crates/*-writer/src/**", "crates/docspec-json/src/**", "crates/docspec-http/src/**", "crates/docspec-cli/src/**", "crates/docspec/src/**"]
---

You review a DocSpec pull request for resource use an untrusted document or
request can make unbounded. AGENTS.md: "Never buffer full documents --
stream always." SECURITY.md promises an inline asset cap and a nesting
depth limit. Existing bounds to reuse: `MAX_STYLE_DEPTH` (docspec-core
style.rs), the saturating depth counter (depth.rs), `MAX_LIST_LEVEL`
(docx-reader document/mod.rs), counted rather than recursive drawing
nesting (document/media.rs), and `streaming_archive.rs`, which bounds ZIP
reads with `take`.

Report a change that adds, on a path untrusted input reaches:

- a read without a bound: `read_to_end`, `read_to_string`, a `Vec` or
  `String` that grows with the input, a ZIP entry decompressed whole (a
  zip bomb: tiny compressed, huge expanded);
- recursion or nesting that follows the input's structure without a depth
  limit;
- buffering a whole document, part or subtree where the data could stream;
- a loop whose iterations the input controls without bound.

For each, show the input that triggers it and what grows (memory, stack,
time). Name the existing bound to reuse where there is one.

## Severity

- **high**: a small input (a few KB, or a small ZIP) exhausts memory or
  stack, reachable over HTTP or the CLI.
- **medium**: growth proportional to a large input where the design
  requires streaming, or a missing depth limit on a new parser.
- **low**: a bounded but needlessly large buffer on a common path.

## Do not report

Existing unbounded reads the change does not touch (package.rs and the
Markdown reader read whole today; the HTTP server has no body limit by
documented choice), buffering the format requires and documents (e.g. the
BlockNote writer's table subtrees), test code, and "consider a limit"
without a concrete trigger. No proof, no finding.

In `summary`, state the input, what grows, and the fix.
