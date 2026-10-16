<!-- Organization profile: https://github.com/devrandom-labs -->

<div align="center">

# /dev/random

### Give agents authority. Keep control.

Identity, permissions, and dependable execution for AI agents.<br>
Built on open-source Rust foundations.

[Explore the foundations](#open-source-foundations) · [Read the identity overview](https://github.com/devrandom-labs/cesr#readme) · [Talk to Joel](mailto:joel@devrandom.co)

</div>

---

An agent that can use your tools can also change your business. You need to know
who it represents, what it may do, and what actually happened when it acted.

**We're building the connection between identity and action:** identities that
people and organizations control, explicit permission to act on their behalf,
and an execution system that enforces those decisions as work happens.

## What we're building

The goal is to make delegated work answer four concrete questions:

| Question | What it means in practice |
| --- | --- |
| **Who does this agent represent?** | Verify the agent's identity and the evidence connecting it to a person or organization. |
| **What may it do?** | Limit access to particular resources and actions. Require approval for the actions that need it. |
| **Can I change my mind?** | Withdraw permission and prevent further actions once that change is known to the execution system. |
| **What actually happened?** | Recover the recorded decisions and outcomes after an interruption, and resolve uncertain results before retrying. |

Identity supplies the evidence. The resource owner decides what that evidence
permits. The execution system must apply that decision when the agent acts.

## A concrete example

Ask an agent to investigate a customer issue. Give it access to the relevant
records and permission to draft a response. Sending that response requires
approval of the exact message.

The workflow we're building toward keeps those decisions explicit: access to
records, permission to investigate, and approval to send. If access is withdrawn,
further work must respect that change. If the system restarts after sending but
before receiving confirmation, it must resolve the outcome before trying again.

**Useful autonomy means you can delegate the work and retain authority over it.**

## Open-source foundations

The public libraries already contain working code and examples. Each has its
own scope and development status; the integrated agent product is still being
built.

| Project | What exists today | Start here |
| --- | --- | --- |
| **[CESR / KERI](https://github.com/devrandom-labs/cesr)** | Cryptographic identity foundations: identifiers, key histories, rotation, delegation of identifier control, and credential verification for supported protocol profiles. | [Identity example](https://github.com/devrandom-labs/cesr/blob/main/crates/keri/examples/direct_mode.rs) · [Supported capabilities](https://github.com/devrandom-labs/cesr/blob/main/docs/capability-matrix.md) |
| **[Behavior](https://github.com/devrandom-labs/bombay-behavior)** | Predictable, testable actor logic with explicit actions and reusable policies for supervision, timing, and shutdown. | [Overview and examples](https://github.com/devrandom-labs/bombay-behavior#readme) |
| **[Bombay](https://github.com/devrandom-labs/bombay)** | An actor framework that runs that logic, delivers messages, manages lifetimes, and coordinates application shutdown. | [Application examples](https://github.com/devrandom-labs/bombay/tree/main/examples) |
| **[Mnesis](https://github.com/devrandom-labs/mnesis)** | Event storage and replay: record state changes, rebuild application state, and support recovery. | [Overview and examples](https://github.com/devrandom-labs/mnesis#readme) |

These libraries are under active development. Their READMEs and capability
records describe current support and limitations.

## Bringing it together

**Agency** is the product direction: let organizations adopt agent applications,
grant access, delegate work, and review results under their own rules.

The execution engine comes first. Its initial goal is to carry out an approved
action, survive a lost reply or restart, and recover the outcome without blindly
repeating the action. Permissions, approvals, withdrawal of access, and recovery
are being developed as one execution problem. The complete workflow is not yet
available.

## Build with us

If you're building identity infrastructure, agent applications, or systems that
need accountable automation, we'd like to compare requirements and work through
a concrete use case.

**[Talk to Joel](mailto:joel@devrandom.co)** · [Explore the repositories](https://github.com/orgs/devrandom-labs/repositories) · [Contribute](https://github.com/devrandom-labs/.github/blob/main/CONTRIBUTING.md)

---

<sub>Public foundations in Rust · MIT OR Apache-2.0 · Built in the open</sub>
