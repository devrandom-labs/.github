# Agent identity, permissions, and execution: a critical comparison

**Reviewed 16 October 2026.** This is /dev/random's assessment of primary specifications and vendor documentation, not a security audit or a benchmark. Documented features are distinguished from our assessment of their limits. A feature outside a protocol's scope is not a defect. Product documentation establishes what a vendor describes, not independently measured performance or reliability.

**/dev/random competes across identity, authorization, agent execution, and recovery.** Our product direction is to carry a person's or organization's authority all the way from a grant to an action and its recorded outcome. We compete both with individual products and with stacks assembled from several of them. The comparison is about customer outcomes, deployment choices, and engineering tradeoffs; it does not depend on being first to a feature.

The public foundations exist today. Integrated Agency capabilities are development goals, identified separately below. This review covers the major overlapping categories and named alternatives, rather than claiming an exhaustive inventory of every vendor.

## Competitive map

Five questions make comparisons useful: who controls the identity; who grants authority; where actions are checked; how permission changes reach that check; and how uncertain outcomes are recovered.

OAuth and OpenID Connect are authorization and authentication standards. MCP and A2A are interaction protocols. PAP names two different projects here. Cloud identity products, permission services, and workflow engines are implementations that can be combined. They overlap at different layers, but can still compete for the same customer, budget, and deployment.

| Technology | Strongest documented contribution | Critical boundary for our use case |
| --- | --- | --- |
| [OAuth / OpenID Connect](#oauth-and-openid-connect) | Sign-in, delegated API access, and extensions for detailed grants and token exchange. | The application still owns action semantics and recovery. |
| [MCP](#mcp) | A common tool interface with an HTTP authorization profile. | Tool access alone does not establish safe execution of a business action. |
| [A2A](#a2a) | Agent discovery, task exchange, status, and requests for additional authorization. | Task cancellation and permission withdrawal have different effects. |
| [Personal Agent Protocol](#personal-agent-protocol-poppy) | Company discovery and scoped account access across browsing, APIs, and conversations. | Companies must implement and enforce the session rules. |
| [Principal Agent Protocol](#principal-agent-protocol-pap) | Signed mandates, narrowed delegation, receipts, and documented sandbox backends. | Enforcement varies by backend; signatures do not resolve uncertain external effects. |
| [Microsoft Entra Agent ID](#microsoft-entra-agent-id) | Dedicated agent identities, accountable sponsors, and delegated or application access. | Identity and token administration are anchored in Entra. |
| [Google Cloud Agent Identity](#google-cloud-agent-identity) | Attested agent identities, certificate-bound tokens, and gateway integration. | Identities are tied to Google-hosted resources and their lifecycle. |
| [AWS AgentCore](#aws-agentcore-identity-and-policy) | Credential management and policy checks on tool calls, including session conditions. | The documented enforcement boundary is the configured gateway path. |
| [Auth0 for AI Agents](#auth0-for-ai-agents) | Token management, fine-grained authorization, and human approvals. | Applications must connect those decisions to effects and recovery. |
| [AP2 / ACP](#ap2-and-agentic-commerce-protocol) | Verifiable payment mandates; constrained payment tokens and checkout flows. | Their concrete business workflows focus on commerce. |
| [SPIFFE / SPIRE](#spiffe-and-spire) | Workload identity, attestation, and short-lived credentials. | A workload's identity does not establish a user's task permission. |
| [UCAN](#ucan) | Verifiable capability delegation with separate invocation and revocation specifications. | Resources still need to interpret capabilities and obtain sufficiently fresh evidence. |
| [Temporal](#temporal) | Durable workflow history, replay, and retries. | External effects still need safe retry or reconciliation rules. |
| [DIDs, VCs, and OpenID4VP](#decentralized-identifiers-and-verifiable-credentials) | Portable identifiers, verifiable claims, and credential presentation. | Trust, status, and key-management rules depend on the chosen profile. |
| [KERIpy, KERIA, and Signify](#other-keri-implementations) | KERI implementation, agent hosting, and client libraries. | Shared protocol roots do not establish compatible client APIs or complete profile parity. |
| [Keycloak](#keycloak) | Self-hosted sign-in, federation, and token exchange. | Feature coverage differs between supported and preview exchange flows. |
| [Cedar, OPA, and OpenFGA](#cedar-opa-and-openfga) | Policy decisions and relationship-based permissions. | Applications must supply trustworthy context and enforce the result. |
| [LangGraph, ADK, and Microsoft Agent Framework](#agent-frameworks) | Agent orchestration, tool hooks, approvals, and persistence options. | Saving execution state alone does not establish an external action's outcome. |
| [Restate and Dapr](#restate-and-dapr) | Durable service execution, workflows, and stateful components. | Destination contracts still govern effects beyond the runtime. |
| [Akka, Orleans, and Erlang/OTP](#actor-systems) | Stateful actors, supervision, distribution, and persistence patterns. | Business authority remains an application responsibility. |
| [ERC-8004 and x402](#erc-8004-and-x402) | Agent registries and reputation; HTTP-based payments. | Registration and payment do not establish permission for an arbitrary task. |
| [/dev/random](#where-devrandom-stands) | Public identity, actor, and event-storage foundations. | Integrated authority enforcement and recovery remain unproven as a product. |

## OAuth and OpenID Connect

**Strength.** OAuth already addresses delegated access; OpenID Connect adds authentication. Token Exchange can represent subject and actor, Rich Authorization Requests can express structured permissions, and DPoP binds tokens to a client's key. Comparing our plans only with broad, reusable bearer tokens would misrepresent the available standards. [OIDC Core](https://openid.net/specs/openid-connect-core-1_0.html), [Token Exchange](https://www.rfc-editor.org/rfc/rfc8693.html), [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396.html), [DPoP](https://www.rfc-editor.org/rfc/rfc9449.html).

**Limit and assessment.** Grant formats do not themselves define whether this exact message was approved or whether a timed-out send succeeded. Revocation and token introspection exist, but deployments must decide how resource servers learn changes and how long cached decisions remain usable. OAuth is not inherently a hosted service; an organization can operate its own authorization server. [Token revocation](https://www.rfc-editor.org/rfc/rfc7009.html), [Token introspection](https://www.rfc-editor.org/rfc/rfc7662.html).

**Our position.** Our focus is the continuing relationship between identity, granted authority, and execution. Access to an OAuth-protected service still depends on permissions that service accepts. OAuth 2.1 remains an Internet-Draft in the reviewed record, revision 16; calling it a finalized RFC would be inaccurate. [IETF record](https://datatracker.ietf.org/doc/draft-ietf-oauth-v2-1/).

## MCP

**Strength.** MCP's reviewed **2026-07-28** HTTP authorization profile includes resource discovery, intended-audience checks, scope challenges, and step-up authorization. Authorization is optional overall, with different guidance for local standard-input/output servers. “MCP has no authorization” is an outdated criticism. [Authorization specification](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization).

**Limit and assessment.** An authorized tool call may still carry an unapproved recipient, amount, or document. The tool or a controlling service must bind permission to those inputs. MCP's own security guidance addresses token forwarding, confused deputies, and local-server compromise; implementing the interface is not proof that these risks are controlled. [Security guidance](https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices).

**Our position.** MCP is a plausible tool boundary to support. The competitive value would be connecting a tool invocation to an exact grant and recoverable outcome. An Agency adapter and its conformance tests remain to be built.

## A2A

**Strength.** A2A **1.0.0** defines discovery, tasks, progress, artifacts, authentication schemes, and an authorization-required state that can support human approval. It is more than message transport. [Released specification](https://a2a-protocol.org/v1.0.0/specification/).

**Limit and assessment.** The server applies its own authorization policies. Cancellation is an attempt and can fail; message submission is not universally guaranteed to be idempotent. Consequently, neither a task identifier nor a cancellation response proves that a downstream action was prevented or performed only once. These are execution responsibilities, not evidence that A2A is broken. [Authorization, cancellation, and operation semantics](https://a2a-protocol.org/v1.0.0/specification/).

**Our position.** Consider A2A for interoperable task exchange. Our execution engine would still have to preserve the owner, approved action, permission state, and outcome through each handoff. No A2A conformance claim is made here. This assessment uses the pinned release; the moving `latest` page includes additional changes.

## Personal Agent Protocol (Poppy)

**Strength.** The reviewed **Draft 0.1** connects personal agents with companies through discoverable metadata, agent identity, per-company user identifiers, sessions, scoped sign-in, and account-token revocation. It builds on OAuth and supports both OpenAPI and MCP. It tackles practical adoption by existing businesses. [Introduction](https://personalagentprotocol.org/docs/introduction), [draft specification](https://personalagentprotocol.org/docs/spec).

**Limit and assessment.** Token protection differs across interfaces: the draft uses DPoP for OpenAPI and bearer tokens for MCP. Optional mediated sign-in passes user credentials through the agent, so deployments must choose permitted sign-in methods deliberately. The company enforces session scopes and sign-out effects; a common session alone does not supply durable action outcomes.

**Our position.** Poppy competes for the personal-agent/company relationship. Our planned scope extends to authority across organizations and recovery of approved work. The relevant choice is whether the customer needs session-based account access or that broader execution responsibility. Broad adoption and conformance were not measured in this review.

## Principal Agent Protocol (pap://)

**Strength.** Baur Software documents signed mandates, delegation that narrows scope and lifetime, a handshake with co-signed receipts, and sandbox execution. This directly overlaps our intended authority and execution story. [Developer documentation](https://baursoftware.com/zero-trust/principal-agent-protocol/docs).

**Limit and assessment.** Its documentation explicitly distinguishes enforcement backends: Linux seccomp does not provide path-based filesystem restriction; Docker supplies stronger isolation; the described native macOS, BSD, and Windows backend lacks syscall enforcement. A signed mandate proves the signed grant, not that a person understood it. A receipt exchange also needs a recovery rule when the external action succeeds but the receipt is lost.

**Evidence limit.** The linked full specification redirected to a sign-in page during this review. These findings rely on the public developer documentation; implementation, interoperability, and crash behavior were not independently tested.

**Our position.** This is a direct competitor in signed delegation and controlled execution. Compare the precision of resource limits, the consent experience, and recovery after receipt loss. Our approach aims to preserve those decisions through the actor and event-storage layers.

## Microsoft Entra Agent ID

**Strength.** Agent ID provides specialized agent identities, reusable blueprints, sponsors accountable for agents, and tokens that distinguish an agent acting for itself from one acting for a user. This is an existing enterprise identity model, not merely a shared API key. [Identity overview](https://learn.microsoft.com/en-us/entra/agent-id/agent-identities).

**Limit and assessment.** Identity administration and token issuance depend on the Entra tenant and blueprint model. Organizations already using Entra may value that integration; organizations seeking independently controlled identifiers may face a different requirement. A sponsor records accountability, but is not itself approval for every action.

**Our position.** Entra offers delivered identity administration. Our proposed alternative combines independently verifiable identity with recoverable execution across independently administered services. We have not benchmarked governance coverage, revocation latency, or deployment cost. Availability and licensing vary across the broader platform. [Platform scope](https://learn.microsoft.com/en-us/entra/agent-id/what-is-agent-id-platform).

## Google Cloud Agent Identity

**Strength.** Google documents per-agent SPIFFE identities, attestation, certificate-bound access tokens, delegated user access, and integration with its credential manager and gateway. This directly challenges claims that cloud agents lack distinct cryptographic identities. [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview).

**Limit and assessment.** The documented identity derives from the hosting resource. Replacing the agent creates a new principal; deleting it does not automatically remove its old IAM bindings. That is a concrete lifecycle tradeoff, not a general claim that cloud identity is insecure. Protection also depends on the configured runtime and destination path.

**Our position.** A stable identity controlled independently of a hosting resource is a direction worth testing. Moving an identifier does not automatically move a customer's permissions or trust. We must demonstrate both continuity and deliberate reauthorization when hosting changes.

## AWS AgentCore Identity and Policy

**Strength.** AgentCore Identity manages workload identities and credentials. Policy evaluates calls through AgentCore Gateway using Cedar and now documents Dogwood conditions over session history, such as prior approval, action counts, and budgets. AWS already addresses action-time and stateful authorization. [Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html), [Policy](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html).

**Limit and assessment.** Gateway enforcement covers requests routed through that boundary. Direct credentials or alternate network paths require their own controls. Session-aware authorization is also distinct from determining whether an external service committed an action before a connection failed. These integration obligations apply to us too.

**Our position.** This is one of the closest documented competitors to our planned execution controls. A credible comparison needs the same workflow, policy changes, concurrent calls, and injected failures on both stacks. The decision should include control of deployment, evidence portability, and operating cost as well as permission coverage.

## Auth0 for AI Agents

**Strength.** Auth0 describes token vaulting, fine-grained authorization, and human-in-the-loop approvals. Token exchange can preserve user context while obtaining access for a downstream service. These address real agent authorization needs today. [Agent offering](https://auth0.com/solutions/ship-ai-agents), [Token Vault](https://auth0.com/blog/auth0-token-vault-secure-token-exchange-for-ai-agents/).

**Limit and assessment.** Applications still have to bind approval to the action actually sent and reconcile uncertain outcomes. Using a hosted credential service introduces a service dependency, but may remove substantial operational work. Announced features and available features must be assessed separately; this review does not treat every roadmap item as shipped.

**Our position.** Auth0 plus an execution engine competes with the integrated Agency proposition. Our target is fewer application-specific gaps between a grant, a reviewed action, and its recovered outcome, while retaining owner control. Compare that benefit against integration and operating effort.

## AP2 and Agentic Commerce Protocol

**AP2 strength.** Google's AP2 authorization framework uses user-approved mandates, verifier checks, and receipts. It describes both user-credential and trusted-agent-provider models, with payment-specific mandate types. It explicitly leaves room for broader applications. [Agent Authorization Framework](https://ap2-protocol.org/ap2/agent_authorization/).

**ACP strength.** The Agentic Commerce Protocol developed by Stripe and OpenAI specifies checkout and delegated payment. Allowances constrain payment tokens by properties such as amount, merchant, and expiry. [Project](https://www.agenticcommerce.dev/), [architecture](https://www.agenticcommerce.dev/docs/concepts/architecture).

**Limit and assessment.** Their concrete workflows are commerce-focused. Mapping their authority records to arbitrary enterprise tools still needs domain rules and adapters. Signed intent does not itself complete settlement or resolve every uncertain outcome.

**Our position.** Both provide concrete transaction interfaces that set a useful comparison baseline. Compete on carrying precise approval and action binding into enterprise work beyond commerce. AP2, A2A, and ACP are separate protocols. Here ACP means Agentic Commerce Protocol.

## SPIFFE and SPIRE

**Strength.** SPIFFE supplies workload identity conventions and verifiable identity documents; SPIRE implements attestation and credential delivery. Federation allows trust across separately administered domains. This is an important alternative when the actual problem is identifying running software. [SPIFFE overview](https://spiffe.io/docs/latest/spiffe-about/overview/).

**Limit and assessment.** A verified workload still needs permission to act for a particular person on a particular resource. Operating attestation and federation also requires trust-domain decisions.

**Our position.** Evaluate workload identity separately from enduring identity and business delegation. KERI does not automatically attest which code is running, and SPIFFE does not automatically express a customer's task approval. They can be complementary.

## UCAN

**Strength.** UCAN separates delegation, invocation, and revocation, with cryptographically verifiable capabilities. Its specifications provide a direct alternative to inventing another delegation-token format. [UCAN specifications](https://github.com/ucan-wg/spec).

**Limit and assessment.** Verifiers need agreed capability meanings and an acceptable freshness rule for revocation information. Offline signature verification does not reveal a revocation that has not arrived. Capability validation also needs enforcement at the resource.

**Our position.** Compare delegation chains and withdrawal semantics directly. KERI's delegation of identifier control is a different fact from permission to send a message or spend a budget. An application must define that authority explicitly.

## Temporal

**Strength.** Temporal provides durable workflow history and replay. Its documentation also explains that activities can repeat and that external operations need idempotency: repeating the same logical request must not repeat its effect. [Task execution](https://docs.temporal.io/tasks), [idempotency and durable execution](https://temporal.io/blog/idempotency-and-durable-execution).

**Limit and assessment.** A workflow engine cannot infer whether an arbitrary external service committed a request after communication was lost. The integration needs an idempotency key, an outcome lookup, or an explicit unresolved state. Identity and permission checks must be connected to the workflow.

**Our position.** Recovery is an established category. Agency must be compared with identity plus policy plus Temporal, not with a script that forgets its state. The competitive test is how much custom integration remains to preserve approval and authority through recovery.

## Decentralized identifiers and verifiable credentials

**Strength.** W3C DID Core standardizes identifiers and verification relationships, while Verifiable Credentials define issuer claims that a verifier can check. OpenID4VP specifies credential presentation. These are direct alternatives at the identity and evidence layer. [DID Core](https://www.w3.org/TR/did/), [VC Data Model 2.0](https://www.w3.org/TR/2025/REC-vc-data-model-2.0-20250515/), [OpenID4VP](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html).

**Limit and assessment.** DID methods have different resolution and update rules; a common data model does not eliminate trust-policy, status, or interoperability decisions. A valid credential says what its issuer asserts, not whether the holder may perform every proposed action.

**Our position.** Compare KERI's verifiable key history and our supported credential flows against a specific DID method and credential profile. “Decentralized identity” is too broad to score as one implementation. Selective disclosure and wallet compatibility must be checked explicitly against our capability matrix.

## Other KERI implementations

**Strength.** KERIpy implements KERI in Python, KERIA provides agent services, and Signify supplies a client approach. They are direct comparisons for the identity foundation and services built on it, rather than a competing identity standard. [KERIpy](https://github.com/WebOfTrust/keripy), [KERIA](https://github.com/WebOfTrust/keria), [Signify](https://github.com/WebOfTrust/signify-ts).

**Limit and assessment.** The decision includes deployment topology, key custody, supported protocol profiles, client compatibility, and language integration. Sharing KERI does not mean two services expose the same APIs.

**Our position.** CESR/KERI offers a Rust core with network and storage outside the protocol logic. That suits applications that want to choose their own execution and persistence components. Our [capability matrix](https://github.com/devrandom-labs/cesr/blob/main/docs/capability-matrix.md) explicitly excludes universal Signify/KERIA agent compatibility from the selected profile. Compare equivalent protocol flows and deployment responsibilities before claiming substitution.

## Keycloak

**Strength.** Keycloak is a self-hosted alternative to managed identity services. Its current documentation distinguishes supported standard token exchange from legacy and preview flows; actor-aware delegation is documented as preview. [Token exchange](https://www.keycloak.org/securing-apps/token-exchange).

**Limit and assessment.** Running the identity service yourself transfers operational responsibility to your team. The reviewed standard exchange flow is realm-scoped, and access-token revocation chains are not available in the documented comparison. These are concrete integration constraints, not an inability to support agents.

**Our position.** Owner control is a meaningful goal, but self-hosting alone is not our distinction. Compete on identity continuity, evidence across organizations, and the amount of application work needed to preserve authority through execution.

## Cedar, OPA, and OpenFGA

**Cedar** gives explicit principal/action/resource/context decisions, with default deny and forbid-overrides-permit rules. Evaluation errors are reported and skipped rather than universally forcing denial, so applications must choose how to handle diagnostics. [Authorization rules](https://docs.cedarpolicy.com/auth/authorization.html).

**OPA** evaluates general policies and can publish decision logs containing inputs and policy metadata. Its flexibility requires disciplined input construction and appropriate handling of sensitive log content. [Overview](https://www.openpolicyagent.org/docs), [decision logs](https://www.openpolicyagent.org/docs/management-decision-logs).

**OpenFGA** models permissions through relationships, with conditions that can depend on supplied context. This fits organizational access well; the application still has to provide correct relationships and current context. [Conditional permissions](https://openfga.dev/blog/conditional-tuples-announcement).

**Our position.** These compete with a bespoke permission engine and are also potential components. Our intended value is preserving the grant, exact action, decision, and result together. A policy verdict needs an enforcement point; an execution engine needs a correct policy. Neither substitutes for the other.

## Agent frameworks

**LangGraph** supplies persistent execution state and supports interruption and continuation. Its configurable persistence is a serious baseline for long-running agents. Compare the selected checkpoint backend and execution boundaries, not an in-memory demonstration. [Persistence](https://docs.langchain.com/oss/python/langgraph/persistence).

**Google ADK** provides callbacks that can inspect or block tools and mechanisms for action confirmation. Its flexibility is valuable, but a callback controls only the paths that invoke it; credential and network isolation remain separate concerns. [Tool callbacks](https://github.com/google/adk-docs/blob/main/docs/callbacks/types-of-callbacks.md), [confirmation](https://adk.dev/tools-custom/confirmation/).

**Microsoft Agent Framework** includes workflow checkpointing and resumption with several storage choices. Checkpoint timing and storage durability affect what survives a failure; an in-memory checkpoint is not restart recovery. [Checkpoint documentation](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints).

**Our position.** Agency competes on dependable delegated work. Its execution engine should make approved actions and uncertain outcomes explicit even when the model loop is replaced. These frameworks compete directly with Agency's future agent runtime and may also become clients of its execution engine. Integration is a possibility, not a claimed capability.

## Restate and Dapr

**Restate** combines durable calls with stateful services and workflows. Its documentation explicitly recognizes that a side effect can repeat if failure occurs before the result becomes durable. [Service model](https://docs.restate.dev/concepts/services/), [execution semantics](https://restate.dev/blog/why-we-built-restate).

**Dapr** offers workflows, actors, service invocation, and state infrastructure. Its current overview also describes signed, verifiable workflow histories. That overlaps both our recovery and evidence goals; cryptographic records are not exclusive to our approach. [Workflow overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/), [platform overview](https://docs.dapr.io/overview/).

**Limit and assessment.** Both reduce distributed-systems work. Teams still need deployment, storage, destination permissions, and external-effect contracts suited to their application. A verified history establishes the recorded facts; it cannot establish facts that were never reliably observed.

**Our position.** Compare a composed Bombay/Mnesis application against these runtimes on the same failures, evidence requirements, and operational budget. The decision is whether explicit ownership and typed effects simplify the complete application enough to justify choosing our stack.

## Actor systems

**Akka** has typed event-sourced actors with persisted events and recovery. It is a direct comparison for Bombay plus Mnesis, including the separation between commands, effects, and state changes. [Persistence](https://doc.akka.io/libraries/akka-core/current/typed/persistence.html).

**Orleans** offers virtual actors and distributed application support in .NET. Its runtime model reduces explicit actor placement and lifecycle work. Compare that convenience with the control required by an application's lifecycle and failure rules. [Overview](https://learn.microsoft.com/en-us/dotnet/orleans/overview).

**Erlang/OTP** provides supervision trees with explicit child startup, restart, and shutdown policies. Restarting a process, however, does not establish whether its last external action succeeded. [Supervision principles](https://www.erlang.org/doc/system/sup_princ.html).

**Our position.** Behavior and Bombay compete as a Rust actor stack with a deterministic core and explicit typed actions. That is a concrete programming-model choice. Performance, developer effort, distributed operation, and recovery need workload-specific comparisons; language choice alone does not settle them.

## ERC-8004 and x402

**ERC-8004**, marked Draft in the reviewed specification, defines identity, reputation, and validation registries. It acknowledges that registration cannot guarantee advertised behavior and that reputation can be manipulated through fake participants. [Specification](https://eips.ethereum.org/EIPS/eip-8004).

**x402** enables services to request payment through HTTP interactions. It addresses economic access to a service; payment is a different fact from a person's authorization of the agent's task. [Protocol introduction](https://github.com/x402-foundation/x402/blob/main/docs/introduction.md).

**Our position.** These compete in cross-organization agent discovery, trust, and transactions. Our focus is an owner's authority over actions and their outcomes. A comparison must include chain and settlement assumptions where applicable, and must not treat public reputation as equivalent to delegated permission.

## Where /dev/random stands

Our current evidence is component-level: [CESR/KERI's supported profiles](https://github.com/devrandom-labs/cesr/blob/main/docs/capability-matrix.md), [Behavior's explicit actions](https://github.com/devrandom-labs/bombay-behavior#readme), [Bombay's actor execution](https://github.com/devrandom-labs/bombay#readme), and [Mnesis's event storage](https://github.com/devrandom-labs/mnesis#readme). Agency's integrated product remains in development.

The competitive proposition is **connecting an owner's verifiable identity and grant to an exact action, preserving the permission decision and outcome through interruption and recovery**. That offers a coherent product around the whole job, while the public libraries also compete as individual components. Demonstrating it with real destinations and independently administered parties is the next proof point.

Three limits remain fundamental:

- A cryptographic identifier proves control of keys under the protocol's rules. Connecting it to a real person or organization requires trusted evidence and onboarding.
- A disconnected verifier cannot immediately learn a remote permission change. A deployment must define evidence freshness, expiry, and whether to stop when it cannot refresh.
- A permission check cannot undo an action already committed. Checking and acting must have a defined ordering; effects outside one transaction need destination-specific recovery.

No independent security audit, comparative performance result, or conformance result is asserted by this document. Supporting OAuth, MCP, A2A, either PAP, or the commerce protocols remains a proposal until a tested implementation is linked.

## The head-to-head test that matters

Use the same support workflow on each applicable stack: read one customer's case, draft a response, obtain approval for that exact response, and send it. Identity-only and protocol-only candidates need an explicit execution component; compare the assembled systems and name those dependencies.

| Test | Required observation |
| --- | --- |
| Change the recipient or message after approval | The changed action is refused or requires a new approval. |
| Delegate to another agent | The child cannot expand the approved resources, actions, lifetime, or budget. |
| Withdraw permission while work is queued | Record when the change becomes effective and which actions crossed the enforcement boundary before it. |
| Disconnect the policy or identity service | Show the declared freshness rule and the actual allow, deny, or wait result. |
| Crash before sending, after sending, and before recording the reply | Recover the same logical action; do not infer failure from silence. |
| Retry or race two copies of an approved action | Demonstrate the destination's duplicate-prevention rule or retain an unresolved outcome. |
| Call a tool outside the controlled route | Show whether alternate credentials and network paths permit a bypass. |
| Rotate keys or move hosting | Preserve the intended identity history while requiring correct destination trust and permissions. |

Measure enforcement coverage, revocation delay, recovery outcomes, latency, operating effort, and total deployment cost. These are proposed acceptance tests, not completed results. Publish failures and limitations alongside passes.

## Review scope

This review pins MCP to 2026-07-28, A2A to 1.0.0, and Personal Agent Protocol to the site's Draft 0.1. OAuth 2.1 was revision 16 at review time. Principal Agent Protocol and product pages are dated snapshots of live documentation; they do not establish universal release availability or adoption. RFC links identify individual standards, not proof that every provider implements them. Recheck these sources before a purchasing decision or a new compatibility claim.
