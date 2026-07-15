# Contributing to devrandom-labs

> How we work here. One tested layer at a time.

This is the **org-wide** guide — it applies to every devrandom repo (`cesr`,
`mnesis`, `bombay`, and the rest). Some repos add their own `CONTRIBUTING.md`
with release specifics or extra rules; when they do, **the repo's own file wins**
and this one is the baseline underneath it.

All contributions are welcome: bug reports, failing tests, docs, examples, and
code. If you're here to help build accountable-agent infrastructure in Rust,
you're in the right place.

---

## The one thing to internalise first

We build load-bearing infrastructure — identity, memory, and control for
software that acts on its own. A subtly-wrong offset, a swallowed error, or a
silent overflow in this layer isn't a cosmetic bug; it's a hole in something
other people are trusting. So the culture is:

- **Facts only.** No guesses about how a crate, an algorithm, or the CESR/KERI
  wire format behaves. If you don't know, read the spec, the reference
  implementation (`keripy`/`signify-ts` for KERI), or the source — then cite it.
  Uncertainty stated plainly beats confidence invented.
- **The type system does the enforcing.** Make illegal states unrepresentable
  rather than checking for them at runtime. An invariant encoded in a newtype or
  a typestate can't rot.
- **Every rule earned its place.** The engineering rules below each exist because
  they caught a real bug in this codebase. They aren't style preferences.

---

## Getting set up

Everything is built and tested through **[Nix](https://nixos.org/)** — you never
install toolchains or dev tools imperatively (no `rustup`, no
`cargo install`). The flake pins the exact Rust toolchain plus every tool the CI
gate needs.

```bash
git clone https://github.com/devrandom-labs/<repo>.git
cd <repo>
direnv allow      # auto-enters the dev shell (or: nix develop)
```

That shell gives you the pinned **stable** Rust (edition 2024, no nightly),
`cargo-nextest`, `taplo`, `cargo-deny`, `cargo-audit`, and friends. If a command
isn't found, you're outside the shell — run `nix develop` or let `direnv` in.

---

## The workflow

### 1 · Track it on GitHub

Work is tracked with **GitHub Issues and Project boards** — that's the single
source of truth. Each repo has a board; the umbrella roadmap lives on the
org-level board.

- **Before a feature or a big refactor, open an issue first** so the design can
  be discussed before code exists. A refactor premise ("this is bloated") is a
  hypothesis to verify with measurements, not a fact to execute — surface the
  reframe in the issue.
- Small fixes and doc changes can go straight to a PR.

### 2 · Branch and build

Branch off `main`. Keep commits focused — one logical change each, no mixing
unrelated work.

Iterate with the fast **inner loop** (per-repo `justfile` where present):

```bash
just check          # fmt + clippy + tests + doctests, incremental
just test-fast      # skip the slow crypto proptests while iterating
just watch          # re-run on file change
# or directly:
cargo nextest run --all-features
```

The inner loop is for speed. It is **not** the gate.

### 3 · Pass the gate — `nix flake check`

```bash
nix flake check
```

**This is the only command that matters before you commit or push.** It runs, in
order: clippy (deny warnings), `rustfmt`, `taplo` TOML fmt, `cargo audit`,
`cargo deny`, the full `nextest` suite across feature combinations, doctests,
and — where applicable — the `wasm32` and `no_std` build checks.

Never short-circuit it with a bare `cargo test`/`cargo clippy`: those miss the
TOML, audit, deny, wasm, and no_std legs, which is exactly where cross-cutting
regressions hide.

### 4 · Open the PR

- Describe **what and why**, not just the diff.
- Non-trivial changes add or update **tests and docs**.
- **Call out breaking changes explicitly** in the PR description and the
  `CHANGELOG` — allowed while a crate is `0.x`, but never as an accidental
  side-effect of unrelated work.
- CI runs the same `nix flake check` you ran locally. Green gate, then review.

> **Signed commits:** some repos (e.g. `cesr`) require **GPG-signed** commits on
> `main` — commits made through the GitHub web/API are unsigned and get rejected.
> Commit locally with signing configured (`git config commit.gpgsign true`).

---

## Engineering rules

These are shared across every devrandom Rust repo. Each repo's `CLAUDE.md`
carries the canonical, worked-example version plus any project-specific
additions (storage atomicity, testing categories); this is the summary.

- **Arithmetic safety.** No bare arithmetic on sizes, offsets, lengths, or
  counts in production paths — use `checked_add`/`checked_sub`/`checked_mul` and
  return `Err` on overflow. `saturating_*` is banned in these paths (it hides
  overflow as a misleading downstream error). `debug_assert` is not a safety
  check — it compiles out in release.
- **Error handling.** All error types use `thiserror`. One variant = one failure
  domain; never jam unrelated failures into an existing variant, never erase a
  typed error into `Box<dyn Error>` that callers match on, never discard the
  source with `|_|` (wrap via `#[source]`/`#[from]`). Unknown values are
  `Option`, not sentinels (`version: 0`). Input-validation errors are distinct
  from corruption errors. Read-path and write-path enforce the same invariants.
- **API design.** No unused generics or associated types (YAGNI — add at the
  second concrete use). Internal helpers are `pub(crate)`, not `pub`;
  `#[doc(hidden)]` is not access control. Parsing untrusted input **never
  panics** — that's a DoS bug; return `Result`. Panics are for programmer bugs
  only. `new_unchecked` means *no validation* — if it asserts, it's `new`.
- **Concurrency.** `Relaxed` memory ordering needs a structural proof, not a
  comment. Prefer `Acquire`/`Release` or a mutex that makes the invariant
  self-contained.
- **Functional-first, allocate-last.** Prefer combinators over imperative
  control flow for simple data flow; lazy over eager (`.collect()` only when you
  need the concrete collection); borrow before own (`&str` over `String`,
  `&[u8]` over `Vec<u8>`, `Cow` where it fits). Every `clone`/`Box::new` on a hot
  path justifies itself. All `use` imports at the top of the file — no inline
  `use`, no deep path qualification (alias collisions with `as`).
- **Self-documenting code.** Comments explain the *why* behind non-obvious
  logic, never the *what*.

The clippy config (`all` + `pedantic` + `nursery` at deny, plus the restriction
suite) **is the law.** Never relax a lint level or touch `[lints]` / `clippy.toml`
without explicit maintainer approval. Every `#[allow]` is on a specific item and
carries a `reason`.

---

## Testing — categories first

Before reaching for narrow per-function tests, every new feature covers the
cross-cutting categories (the exact set is per-repo; the shape is universal):

1. **Round-trip / sequence** — `decode(encode(x)) == x`; multi-step interactions
   on the same object, not operations in isolation.
2. **Lifecycle** — for anything that persists: write → close → reopen → verify,
   and write → corrupt → reopen → detect.
3. **Defensive boundary** — feed each module inputs that violate its upstream's
   guarantees (truncated frames, oversize lengths, invalid codes). It must reject
   them as typed errors, never panic.
4. **Linearizability / isolation** — concurrent readers and writers with real
   overlap (`tokio::spawn` + `Barrier`), asserting snapshot consistency — not
   sequential-then-check.

Every test must: call the **actual** code under test (never a reimplementation),
be able to **fail** when the invariant breaks, and assert the **specific** value
(`assert_eq!` with exact values — `println!` is not an assertion). A test
documenting a known bug **fails** while the bug exists (`#[ignore]` if accepted)
— never a green test that hides it. Proptest ranges include the boundaries: `0`,
`1`, `MAX-1`, `MAX`.

---

## Commits, licensing, releases

- **Conventional commits** with an optional scope:
  `feat(store): …`, `fix: …`, `refactor: …`, `docs: …`, `test: …`, `chore: …`.
  Some repos derive version bumps from these (a `feat:`/`fix:` on `src/**` cuts a
  release; `docs:`/`chore:` don't) — so scope honestly.
- **Rust edition 2024**, pinned stable toolchain in `rust-toolchain.toml`. No
  nightly, no `#![feature(...)]`. Bump the toolchain and `rust-version` in
  lockstep.
- **Dual licensed MIT OR Apache-2.0.** By contributing you agree your work is
  licensed under both, at the user's choice.
- **Releases are automated per repo** (release-plz or git-tags — see the repo's
  own docs). Don't hand-edit versions or changelogs.

---

## Where to start

- Read the repo's `README.md` and `CLAUDE.md` — the latter is the deep map of the
  architecture and the full rule set with worked examples.
- Pick a `good first issue` on the repo's board, or open an issue proposing one.
- Ship the smallest correct, tested slice. One layer at a time — in the open.

<sub>Questions? Open an issue. Built in the open, in Rust — the receipts are in CI.</sub>
