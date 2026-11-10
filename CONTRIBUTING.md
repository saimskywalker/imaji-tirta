# Contributing

Contributions are welcome. A few things worth knowing before you spend time.

## How changes land

Every change reaches `main` through a pull request that the maintainer reviews
and merges. That includes changes from forks, which is the normal path: you
cannot push to this repository directly, and nothing merges without a review.
Fork the repository, branch from the latest `main`, and open the PR against
`main`. Keep one topic per PR. If a PR sits without a response, a nudge is
fine.

Commit messages follow the short conventional style already in the log:
`fix: ...`, `docs: ...`, `chore: ...`, with the PR number added on merge.

## Before opening a PR

```bash
cargo fmt --all -- --check
cargo clippy --locked --all-targets -- -D warnings
cargo test --locked
cargo doc --no-deps
```

CI runs exactly these four on `ubuntu-latest` with the stable toolchain.
Warnings are denied, and that includes `missing_docs`: a new public item
without a doc comment fails the build.

## Pull requests

Every CI check must pass before a pull request can be merged. The `PR rules`
check enforces these:

- **Issue first.** Open an issue and wait until a maintainer labels it
  `accepted`, then write `Closes #N` in the pull request.
- **Tests.** Every change to `src/` needs a test: a new `#[test]` or a
  change under `tests/`. A bug fix needs a test that fails without the fix.
- **Small.** At most 400 changed lines (`Cargo.lock` not counted), one issue
  per pull request.
- **No suppressions.** No `#[allow(`, `#[expect(` or `#[rustfmt::skip]`, no
  lint set to `"allow"` in `Cargo.toml`, no `clippy.toml` or `rustfmt.toml`.
  Fix the finding.
- **At most 2 open pull requests** per person.

If a rule really does not fit your change, say why in the description. A
maintainer can waive it with a label (`no-tests-needed`, `large-change` or
`suppression-ok`).

## Legal notice

By submitting a contribution you confirm that you wrote it, that you have the
right to submit it, and that it may be distributed under the project licence
(see [LICENSE-MIT](LICENSE-MIT)).

## AI-assisted contributions

Using AI tools is fine; please just be open about it.

- Say in the PR description which AI tools you used, if any: none, some or most, plus the tool or agent and the model (for example "Claude Code, Opus 5.5" or "Copilot, GPT-5").
- Please write the PR description in your own words; short and plain is fine. It shows us what you understood about the change. Please don't paste generated text.
- Read, run and test every line you submit, and be ready to explain it in review. If you don't understand what a line does, don't submit it.
- Keep the change focused. Agents like to add unrelated "improvements", long comments and dead code; remove them before you open the pull request.
- Pull requests that are raw, unreviewed agent output will be closed.
- If you use an agent, point it at [AGENTS.md](AGENTS.md).
- If the disclosure is missing, a check adds a reminder to the PR. It does not fail the PR, but a maintainer will ask about it before merging.

## What a good change looks like

A test built on a worked example with a stated source. Every equation here
follows a KP part, and the test for it should reproduce a published example
from that part or from a well-known hydraulics textbook, with a comment naming
the document, edition and page. A test that only checks the code against
itself does not show the formula is right.

For a bug fix, the test fails without the fix.

## Things to be careful with

**Cite the KP part.** Every new function says in its doc comment which KP part
and section it follows. If you cannot find a source for a value, do not
guess one: leave it as a parameter and say so in the PR.

**Do not reproduce the criteria.** The crate implements equations and
procedures and cites where they come from. It does not copy text out of the KP
documents and does not embed their design tables. A value an engineer reads
off a table or chart is a parameter or a caller-supplied table. A `Default`
with representative values is allowed only when it is documented as a
convenience the engineer must confirm against the current edition.

**Units.** Every quantity is a newtype, and conversions are named methods.
Do not add a function that takes a bare `f64` for a physical quantity, and do
not add a second place where mm/day becomes l/s/ha.

**No panics, no unbounded loops.** Library code returns `Result` with a
`KpError`; `unwrap` and `expect` belong in tests and examples only. Iterative
solvers are bounded and report `DidNotConverge` or `NoBracket` instead of
returning an unconverged answer.

**A failed check does not stop the calculation.** Return the result with the
failure recorded, so the engineer can see by how much a limit is exceeded.

## Scope

Irrigation hydraulics following KP-01 to KP-07. Structural member design
belongs in
[imaji-structural-sni](https://github.com/saimskywalker/imaji-structural-sni).
The crate has no dependencies beyond the standard library, and a PR that adds
one needs a strong reason.

## Stay in touch

Don't forget to follow [@saimskywalker](https://github.com/saimskywalker) on
GitHub to see new releases and other open-source projects as they come out.
