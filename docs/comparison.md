# Why /dev/random?

**Give an agent a job without giving up control.**

We're building Agency to connect three things: who an agent represents, what
it has permission to do, and what actually happens when it acts. That connection
must survive changed permissions, lost replies, and restarts.

For example: approve a customer reply, send only the approved message, and check
whether it arrived before retrying after a failure.

Our bet is that teams will choose a system that handles that whole job together.
The [public foundations](https://github.com/devrandom-labs#open-source-foundations)
are available; the complete Agency workflow is in development.

## How we compare

These technologies compete with different parts of our stack. Customers can
also combine them. The practical question is how much work remains to connect
permission, action, and recovery.

| Alternatives | What they do well | Tradeoff for this job |
| --- | --- | --- |
| **[OAuth / OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)** | Sign-in and delegated access; [extensions](https://www.rfc-editor.org/rfc/rfc9396.html) can describe precise permissions. | Applications still connect those permissions to the actual action and its result. |
| **[Microsoft Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/agent-identities), [Google Agent Identity](https://docs.cloud.google.com/iam/docs/agent-identity-overview), [Auth0](https://auth0.com/solutions/ship-ai-agents), [Keycloak](https://www.keycloak.org/securing-apps/token-exchange)** | Manage identities and access. Hosted options reduce operations; Keycloak offers self-hosting. | Provider choices shape identity management. Approval and recovery still need application integration. |
| **[MCP](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization)** | Connect agents to tools, with authorization for HTTP access. | Access to a tool does not settle which business action is approved or whether it completed. |
| **[A2A](https://a2a-protocol.org/v1.0.0/specification/)** | Let agents discover each other and exchange tasks and results. | Each service enforces its own permissions; cancellation is best effort. |
| **[Personal Agent Protocol](https://personalagentprotocol.org/docs/spec)** | Give personal agents scoped access to company accounts and sessions. | Companies must implement and enforce the rules; the specification is a draft. |
| **[Principal Agent Protocol](https://baursoftware.com/zero-trust/principal-agent-protocol/docs)** | Signed permissions, delegation, receipts, and execution restrictions. | A close competitor. Restrictions vary by execution environment; signed receipts cannot resolve a missing external result. |
| **[AWS AgentCore Policy](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html), [Cedar](https://docs.cedarpolicy.com/auth/authorization.html), [OPA](https://www.openpolicyagent.org/docs), [OpenFGA](https://openfga.dev/blog/conditional-tuples-announcement), [UCAN](https://github.com/ucan-wg/spec)** | Express and check permissions; AgentCore can enforce rules on tool calls. | Checks must cover every route to an action and use current permission information. |
| **[LangGraph](https://docs.langchain.com/oss/python/langgraph/persistence), [Google ADK](https://adk.dev/tools-custom/confirmation/), [Microsoft Agent Framework](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints)** | Build agents, pause for input, and manage execution state. | Teams still connect approvals, access changes, and external results across the workflow. |
| **[Temporal](https://temporal.io/blog/idempotency-and-durable-execution), [Restate](https://restate.dev/blog/why-we-built-restate), [Dapr](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)** | Keep work running and recover saved progress after failures. | Recovery still needs permission checks and a way to prevent or detect repeated external actions. |
| **[Akka](https://doc.akka.io/libraries/akka-core/current/typed/persistence.html), [Orleans](https://learn.microsoft.com/en-us/dotnet/orleans/overview), [Erlang/OTP](https://www.erlang.org/doc/system/sup_princ.html)** | Coordinate independent workers and manage failures. | Strong alternatives to Bombay and Behavior; restarting a worker does not establish whether its last action succeeded. |
| **[SPIFFE](https://spiffe.io/docs/latest/spiffe-about/overview/), [DIDs](https://www.w3.org/TR/did/), [verifiable credentials](https://www.w3.org/TR/2025/REC-vc-data-model-2.0-20250515/), [KERIpy](https://github.com/WebOfTrust/keripy) / [KERIA](https://github.com/WebOfTrust/keria) / [Signify](https://github.com/WebOfTrust/signify-ts)** | Identify software or verify identities and claims, using different trust models. | Identity evidence still needs task-specific permissions. Our CESR library also has a [defined support scope](https://github.com/devrandom-labs/cesr/blob/main/docs/capability-matrix.md). |
| **[AP2](https://ap2-protocol.org/ap2/agent_authorization/), [Agentic Commerce Protocol](https://www.agenticcommerce.dev/docs/concepts/architecture), [x402](https://github.com/x402-foundation/x402/blob/main/docs/introduction.md), [ERC-8004](https://eips.ethereum.org/EIPS/eip-8004)** | Cover payment approval, checkout, service payments, or agent identity and reputation. | Each addresses a particular transaction or trust problem; broader business tasks need additional controls. |

## What we need to prove

The same standard applies to Agency: a changed action needs fresh approval,
withdrawn permission must stop further work once received, and a lost reply
must not trigger a blind repeat. External services must support checking the
outcome or preventing duplicates; otherwise the result stays unresolved.

Our advantage has to be demonstrated in that complete workflow. We have not yet
published a head-to-head benchmark or demonstrated integrations with these protocols.

*Based on primary documentation reviewed 16 October 2026. Links describe each
alternative; the tradeoffs are our assessment.*
