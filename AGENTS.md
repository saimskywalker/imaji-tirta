# AGENTS.md

Rules for AI coding agents working in this repository. People should read
[CONTRIBUTING.md](CONTRIBUTING.md); everything here applies to them too. The
`PR rules` check enforces the rules marked (checked); a pull request that
breaks one cannot be merged.

## What imaji-tirta is

A Rust library of irrigation design calculations following the Indonesian
Kriteria Perencanaan KP-01 to KP-07: uniform flow and normal depth
(`src/uniform_flow.rs`), section geometry (`src/section.rs`), freeboard
(`src/freeboard.rs`), canal design (`src/design.rs`), water requirement
(`src/water.rs`), tertiary blocks (`src/tertiary.rs`), weirs, gates and drops
(`src/structures.rs`) and culverts and siphons (`src/conduit.rs`). Every
quantity is a newtype (`src/units.rs`); every error is a `KpError`
(`src/error.rs`). It is in maintenance, as a library only.

Read before you touch:

- any calculation: "Things to be careful with" in CONTRIBUTING.md and "Design
  rules" in README.md.
- units: "Unit convention" in README.md and `src/units.rs`, which holds the
  only mm/day to l/s/ha conversion (`MM_PER_DAY_TO_LPS_PER_HA`).
- tests: `tests/design.rs`, `tests/hydraulics.rs`, `tests/structures.rs` and
  `tests/water.rs`, with the hand calculations in the comments.

## Commands

Use the stable Rust toolchain with rustfmt and clippy. Every one of these must
pass before you open or update a pull request. CI runs the same ones:

```bash
cargo fmt --all -- --check
cargo clippy --locked --all-targets -- -D warnings
cargo test --locked
cargo doc --no-deps
```

Run one test file while iterating: `cargo test --locked --test hydraulics`, or
one test by name: `cargo test --locked trapezoid_geometry_matches_hand_calculation`.
The examples print full reports:
`cargo run -p imaji-tirta --example trapezoidal_channel`.

## Stop and ask the human

Some things must come from the real world. An agent must never invent them.
Stop and ask the person you work for to provide them:

- **The KP part and section.** Every function names the KP part and section
  it follows in its doc comment, read from the KP document itself. Never write
  a part or section number from memory.
- **Coefficients and table values.** Discharge and loss coefficients, Manning
  `n`, freeboard against discharge, side slopes, permissible velocities and
  efficiencies come from the criteria or the project. They are parameters or
  caller-supplied tables; never embed or invent them. If a value cannot be
  confirmed, leave it as a parameter and say so in the pull request.
- **`Default` values.** The representative values behind `FreeboardTable`,
  `TertiaryCriteria`, `EfficiencyChain` and `RotationCriteria` defaults change
  users' results. Never add or change one without a source the human names.
- **Worked examples.** A test for a new calculation reproduces a published
  example from the KP part or a well-known hydraulics textbook, with document,
  edition and page in a comment, or a hand calculation written out step by
  step as in the existing tests. Never invent a source, a page number or an
  expected value.
- **An accepted issue.** If the change has no issue labelled `accepted`, stop
  and ask the human to open one and wait for a maintainer.

## Code

- **English only** for code, identifiers, comments, commit messages and docs,
  apart from the Indonesian terms listed under "Terms" in README.md. Answer
  the person you work for in their language.
- Target Rust 1.75 (`rust-version` in `Cargo.toml`), edition 2021. CI builds on
  stable, so a newer feature passes CI and still breaks 1.75; do not use one.
- `src/lib.rs` denies `missing_docs`: every public item has a doc comment.
- No panics in library code: return `KpError`. `unwrap` and `expect` belong in
  tests and examples only. Iterative solvers are bounded and return
  `DidNotConverge` or `NoBracket` instead of an unconverged answer.
- Comments are sparse: only where the code is not self-evident. Do not add
  comments or doc comments to code you did not change.

## Tests (checked)

- Every change to `src/` comes with a test change under `tests/`. A bug fix
  has a test that fails without the fix; check that it does before you open
  the pull request.
- A new calculation has a worked-example test with its source (see "Stop
  and ask the human").

## Never

- Never silence rustc, clippy or rustfmt (checked): no new `#[allow(`,
  `#![allow(`, `#[expect(`, `#[rustfmt::skip]`, no lint set to `"allow"` in
  `Cargo.toml`, no `clippy.toml` or `rustfmt.toml`. Fix the finding instead.
- Never delete, skip (`#[ignore]`), loosen or rewrite an existing test or
  expected value to make CI pass. If a test looks wrong, say so in the issue.
- Never rename or remove a public item (modules, functions, input and result
  structs and their fields, `KpError` variants, unit types and their named
  conversions such as `Discharge::from_litres_per_second` or
  `DepthRate::over_area`). Callers depend on them.
- Never add a function that takes a bare `f64` for a physical quantity, or a
  second place where mm/day becomes l/s/ha.
- Never copy text or design tables out of the KP documents.
- Never bump the version in `Cargo.toml`, publish, tag or release.
- Never add a dependency (the crate uses only the standard library) or change
  CI workflows unless the issue asks for it.
- Never add unrelated "improvements": no drive-by refactors, renames,
  reformatting of untouched code or dead code.
- Never open pull requests, issues or comments on your own. The human reviews
  your work and posts it.

## Pull requests (checked)

- First an issue, labelled `accepted` by a maintainer. The pull request says
  `Closes #N`.
- One issue per pull request, at most 400 changed lines.
- At most 2 open pull requests per person.
- Fill in the pull request template, including `### AI use` with the tool and
  model.
- The person submitting has read, run and understood every line, and writes
  the description in their own words. Pull requests that are raw, unreviewed
  agent output will be closed.
