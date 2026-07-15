<!-- This file renders on https://github.com/devrandom-labs -->
<!-- It lives at devrandom-labs/.github → profile/README.md -->

# `devrandom-labs`

> **Agents you can prove. Agents you can stop.**

Open-source Rust infrastructure for accountable AI agents — verifiable identity,
a tamper-proof record, and a real kill switch. On your hardware; cloud optional.

```console
joel@devrandom.co:~$ ./manifesto

The next decade of software is autonomous — and autonomy without
accountability is a liability, not a feature. So we build the boring,
load-bearing layer underneath: identity, memory, and control for AI
agents, as open-source infrastructure anyone can verify.

No black boxes. Built in the open, in Rust, one tested layer at a time.
```

---

## The problem we're shipping against

You're handing AI agents your shell, your APIs, your money. Today that means:

```diff
  joel@devrandom.co:~$ diff status-quo devrandom

- no real record         a log anyone could rewrite
+ a record no one can doctor — signed, ordered, replayable

- no real kill switch     the API key can't stop what's already running
+ revoke mid-task — the next action is refused, even offline

- not really yours        agents live in a vendor's account
+ IDs you issue — portable across models, clouds, vendors

- nowhere but the cloud    offline, your agents die
+ your devices form their own network — even on a plane
```

---

## The stack — four Rust layers

An ID card, a flight recorder, a place to run, and a leash. Each is a crate you
can adopt on its own.

```console
joel@devrandom.co:~$ tree ./stack

./stack
├─ cesr .......... an ID card for every agent .......... SHIPPED  v0.9.0
├─ mnesis ........ a flight recorder ................... building
├─ bombay ........ a place to run ..................... building
└─ agency ........ a leash ............................ soon
```

| Layer | Role | What it gives you | Status |
|---|---|---|---|
| **[cesr](https://github.com/devrandom-labs/cesr)** | An ID card | Verifiable IDs you issue, rotate, and revoke. Implements [KERI](https://keri.one), the open standard. `no_std` / WASM-capable. | **Shipped** — [`cesr-rs`](https://crates.io/crates/cesr-rs) v0.9.0 |
| **[mnesis](https://github.com/devrandom-labs/mnesis)** | A flight recorder | Every action signed, hash-chained, replayable. Don't investigate — rewind. Strongly-typed CQRS / event-sourcing kernel. | Building |
| **[bombay](https://github.com/devrandom-labs/bombay)** | A place to run | Agents live on your devices, talk directly, keep working offline. A Zenoh-native actor runtime. | Building |
| **agency** | A leash | Grant exact powers, watch them used, take them back mid-task. The product that ties the stack together. | Soon |

---

## The demo we're building

```console
joel@devrandom.co:~$ agency create researcher
✓ new agent — ID issued by you, not by a vendor

joel@devrandom.co:~$ agency watch researcher
● signed ✓ read repo    signed ✓ opened pull request

joel@devrandom.co:~$ agency kill researcher
✗ stopped mid-task — authority gone, provably
```

Two nodes, one delegated agent doing real work. Revoke the delegation from your
laptop and its **next signed action fails verification mid-task** — every step
replayable from the event log. That's the whole thesis in three commands.

---

## Start here

```console
joel@devrandom.co:~$ cargo add cesr-rs
   # the first layer, installable today
```

- 🌐 **[devrandom.co](https://devrandom.co)** — what we're building and why
- 📦 **[crates.io/crates/cesr-rs](https://crates.io/crates/cesr-rs)** · 📖 **[docs.rs/cesr-rs](https://docs.rs/cesr-rs)**
- 🔭 **[cesr](https://github.com/devrandom-labs/cesr)** · **[mnesis](https://github.com/devrandom-labs/mnesis)** · **[bombay](https://github.com/devrandom-labs/bombay)** — the public layers, built in the open

---

<sub>Built in the open, in Rust — the receipts are in CI. Dual-licensed MIT OR Apache-2.0.</sub>
