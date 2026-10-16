<!-- Organization profile: https://github.com/devrandom-labs -->

<div align="center">

# /dev/random

### Let agents do the work. You stay in control.

[Explore the code](#open-source-foundations) · [How we compare](https://github.com/devrandom-labs/.github/blob/main/docs/comparison.md) · [Talk to Joel](mailto:joel@devrandom.co)

</div>

We're building **Agency**: software for giving AI agents permission to act on
behalf of people and businesses—and keeping control as they work.

**Know who an agent represents. Decide what it can do. See what happened.**

An agent might read a customer case and draft a reply. Sending it should require
approval of that exact message. If the connection drops after sending, the system
should check the outcome before risking a duplicate.

That's the job we're bringing together:

- **Identity you control:** verify who an agent represents, with a history of who controls its identity.
- **Permission for the task:** limit access, require approvals, and apply changes to permission before further actions.
- **Work you can account for:** record decisions and results, recover after interruptions, and keep uncertain outcomes visible.

Our focus is the whole path from permission to result, including what happens
when access changes or something fails.

## Open-source foundations

These Rust libraries are public today:

| Project | What it does |
| --- | --- |
| **[CESR / KERI](https://github.com/devrandom-labs/cesr)** | Verify identities, changes to their keys, and supported credentials. |
| **[Behavior](https://github.com/devrandom-labs/bombay-behavior)** | Make application decisions explicit and testable. |
| **[Bombay](https://github.com/devrandom-labs/bombay)** | Run the work, coordinate messages, and manage shutdown. |
| **[Mnesis](https://github.com/devrandom-labs/mnesis)** | Record changes and rebuild application state after a restart. |

Each repository documents its current scope and examples. The complete Agency
workflow is still being built.

**Building agents that act for your customers or your business?**
[Talk to Joel](mailto:joel@devrandom.co) · [Compare approaches](https://github.com/devrandom-labs/.github/blob/main/docs/comparison.md) · [Contribute](https://github.com/devrandom-labs/.github/blob/main/CONTRIBUTING.md)

---

<sub>Open-source Rust foundations · MIT OR Apache-2.0</sub>
