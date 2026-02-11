
[//]: # (Pandoc Formatting Macros)

[//]: # (::: introtitle)

[//]: # (Introduction)

[//]: # (:::)

## Introduction

### The Rise of AI Agent Protocols

The rapid adoption of AI agents is driving a new generation of communication protocols
designed for agent-to-tool and agent-to-agent interactions. Prominent among these are
the Model Context Protocol (MCP), which connects AI assistants with external tools
and data sources, and the Agent-to-Agent Protocol (A2A), which enables
interoperability between autonomous agents built on different frameworks.

These protocols standardize how AI agents discover capabilities, invoke tools,
exchange messages, and coordinate tasks. MCP defines schemas and transports
(JSON-RPC over stdio and Streamable HTTP) that let models interoperate with tools
and data sources. A2A specifies inter-agent messaging over HTTPS/SSE/JSON-RPC
(with gRPC added in v0.3) to coordinate workflows across applications and
providers. As deployments move from isolated AI assistants to multi-agent systems
operating across organizational boundaries, the demand for protocols that address
security and trust continues to grow.

### The Security Gap: Retrofitting Web-Era Trust Models

Both MCP and A2A deliberately inherit the web-services security framework: HTTPS/TLS
for transport protection and OAuth 2.0/OIDC for authentication and authorization,
often modeled via OpenAPI `securitySchemes`. MCP adopted OAuth 2.1 with PKCE and
Dynamic Client Registration. A2A supports OAuth 2.0, OIDC, API keys, and mutual TLS,
declared via agent cards.

This pragmatic reliance on the web stack accelerates adoption. OAuth and OIDC are
mature, widely deployed, and well-understood. However, as agents shift from
experimental prototypes to autonomous, long-lived, cross-domain principals, this
security foundation reveals structural gaps. Each gap reflects assumptions in the
web stack that do not hold for autonomous AI agents.

The gaps fall into three categories:

### Gap 1: Agent Identity

The web-services security framework does not provide a native identity model for
AI agents. This manifests in four related problems.

**Durability.** The longer an autonomous agent operates, the more complex and
valuable the problems it can solve. Long-duration agents accumulate context,
build trust relationships, and develop reputation — all of which depend on stable
identity. Stable identity is also a prerequisite for accountability: auditing
agent actions, attributing decisions, and enforcing policy require an identifier
that persists across sessions and interactions.

The web stack does not provide this. TLS certificates authenticate *domains*;
OAuth 2.0/OIDC tokens identify users or service accounts within an Identity
Provider's (IdP's)
namespace. An agent's identity is
therefore tied to its current domain or IdP account — not to the agent as a
persistent principal. If the identifier changes (due to redeployment, provider
migration, or infrastructure changes), allowlists break, provenance chains are
disrupted, and accumulated credentials are lost. Today, most agents are deployed
ephemerally — not because enterprises prefer it, but because the control and
identity mechanisms for long-lived autonomous agents do not yet exist. Durable
identity is a prerequisite for the autonomous, long-horizon agents that
agent protocol specifications and academic research envision.

**Centralization.** OAuth and OIDC depend on centralized Identity Providers.
Identity federation (OpenID Federation, SAML) enables cross-domain trust, but
requires bilateral administrative agreements that do not scale to open agent
ecosystems, and still binds each agent's identity to its organization's IdP.
IdP outages or compromises remain single points of failure. Agents operating as
autonomous peers need to establish bilateral trust directly; the hierarchical
client-server model of OAuth/OIDC does not accommodate peer-to-peer trust
establishment.

**Heterogeneity.** The web stack uses multiple incompatible identifier systems
(domain names in X.509, {issuer, subject} pairs in OAuth, vendor-specific service
accounts). No current agent protocol defines a native, persistent identifier
format for agents. Cross-vendor collaborations require brittle bilateral mappings. W3C
Verifiable Credentials 2.0 defines machine-verifiable attestations bound to
persistent identifiers, but these cannot be used effectively when the underlying
identifiers are ephemeral and change across deployments.

**Key management.** In the web-services model, cryptographic keys are managed by
the infrastructure — TLS keys belong to the domain operator, OAuth signing keys
belong to the IdP. The agent does not control its own cryptographic material and
cannot independently rotate keys while maintaining identity continuity. If the
infrastructure operator is compromised, the agent has no independent cryptographic
basis to re-establish trust.

The identity substrate and the trust model built on top of it are distinct
problems. One might argue that using decentralized identifiers (DIDs) as the
subject within existing OAuth flows would address these issues. However,
substituting a DID for the subject claim does not change OAuth's underlying
trust model: tokens are still issued by centralized IdPs, scopes remain static,
and the agent still does not control its own cryptographic keys.

### Gap 2: Authentication and Delegated Authorization

OAuth 2.0 delegation lets a client (here, an agent) act on behalf of a resource
owner. Current agent protocols rely on OAuth-style flows or OpenAPI-style auth
for delegated authorization. When applied to autonomous AI agents, this model
exhibits several well-documented problems.

- *Confused deputy*: In agentic systems, a privileged agent may be manipulated
  into using its credentials on behalf of an unauthorized party. With bearer
  tokens, the agent cannot cryptographically verify *who* is making a request or
  whether the request is authorized — the token works regardless of who triggered
  it. The OWASP MCP Top 10 identifies confused deputy as a primary attack vector.

- *Prompt injection*: LLM-based agents are susceptible to prompt injection, where
  malicious content in data (tool results, user inputs, retrieved documents) is
  crafted to be interpreted as instructions. This is fundamentally a model-level
  vulnerability, but the damage it causes is amplified by the lack of
  authenticated provenance in current protocols. When all data entering an
  agent's context is unauthenticated, the agent and its runtime have no basis
  for applying differentiated trust policies. Authenticated provenance does
  not eliminate prompt injection, but it provides the infrastructure on which
  effective defenses can be built.

- *Token leakage and replay*: OAuth access tokens are bearer tokens — anyone
  holding one can use it until expiry. DPoP (RFC 9449) mitigates this by
  binding tokens to a key pair via proof-of-possession, but the binding is
  between token and key, not between token and a verifiable agent identity.
  A DPoP-bound token proves the presenter holds the right key; it does not
  prove who the presenter is, what delegation chain authorized the request,
  or what task context it belongs to.

- *Static, coarse-grained scoping*: OAuth scopes are predefined string tokens that
  cannot express the dynamic, contextual permissions AI agents require. An agent's
  necessary permissions may change from one micro-action to the next, yet scopes
  are fixed at token issuance time.

- *No verifiable delegation chains*: Agentic systems involve multi-hop
  delegation — user to orchestrator to specialized agents to tools. OAuth's
  native model supports a single layer of delegation; Token Exchange (RFC 8693)
  enables re-delegation across service boundaries, but each hop trusts the
  previous authorization server rather than verifying a cryptographic chain.
  Downstream parties cannot independently audit the full delegation path.

- *Autonomy and lifecycle mismatch*: OAuth's security model assumes interactive
  human consent and persistent authorization relationships. Autonomous agents
  operate at machine speed, may run for hours, days, or months, and spawn
  sub-agents dynamically. OAuth's consent-driven model does not scale with
  the frequency, duration, and system size of agentic workflows — leading to
  either overly broad long-lived tokens or impractical per-agent approval flows.

- *Scale of autonomous coordination*: When agents number in the hundreds of
  thousands or millions — as demonstrated by recent agent-only platforms where
  over a million autonomous agents interact without human participation —
  trust decisions occur at a frequency and volume that human-mediated
  authorization cannot support. The security model must handle trust
  establishment, verification, and revocation at machine scale, not
  human scale.

The OAuth community is actively extending the framework — Rich Authorization
Requests (RFC 9396), DPoP (RFC 9449), Token Exchange (RFC 8693), CIBA — and
these are meaningful improvements. However, they address individual symptoms
within the existing architectural model. The common root cause remains: the
trust model depends on external centralized infrastructure rather than on the
cryptographic relationship between the communicating endpoints.

This specification defines a protocol-native approach to authentication and
delegation that addresses these structural issues, detailed in subsequent
sections.

### Gap 3: Data Authenticity and Provenance

**The problem.** TLS authenticates session endpoints; OAuth authenticates the
client to the server. Neither provides data-level authenticity: individual
messages, tool results, or data artifacts do not carry verifiable origin or
integrity proofs once outside the session (RFC 8446 explicitly notes TLS
authenticates endpoints, not application data). Trust is bound to ephemeral
sessions, not to the data itself. When an agent retrieves data and passes it to
another agent, downstream recipients cannot verify its authenticity without
re-fetching from the original source. Multi-hop workflows cannot build durable
provenance chains. For example, when an orchestrator agent delegates a task to
a specialist agent that invokes a tool, the orchestrator receives the
tool result through the specialist but has no cryptographic proof that the
result originated from the tool and was not modified in transit.

For LLM-based agents, this gap is amplified: data and instructions occupy the
same input stream. Skills, plugins, tool descriptions, and retrieved content
are not merely data — they influence agent behavior. A malicious skill
downloaded from a repository, a poisoned tool description, or compromised
retrieved content can alter what the agent does. Without verified provenance,
agents cannot distinguish trusted components from untrusted ones. The supply
chain for agent capabilities — skills, extensions, integrations — is a data
authenticity problem.

Agents need to produce, relay, and consume data that is self-authenticating: the
authenticity proof travels with the data, independent of session, transport, or
intermediary. This applies to messages, credentials, attestations, task results,
and any artifact whose origin matters.

**Existing approaches.** JOSE (JWS/JWT) provides standardized signing primitives
with key identification (`kid`, `jwk`, `x5c`) and issuer/subject claims. However,
JOSE defines how to sign a payload and identify a key — not how agent protocols
should bind signatures to agent identifiers, how receiving agents resolve and
verify those identifiers, or how signatures chain across multiple hops to build
provenance. The key identification mechanisms inherit whatever limitations the
underlying identifier system has (the same issues described in Gap 1). Without a
protocol-level convention, each application re-implements signing with its own
choices, and signed payloads from one agent framework cannot be verified by
another.

**What is needed.** A protocol-level mechanism that binds data authenticity to the
agent identity substrate: every message is signed using the sender's cryptographic
identifier, provenance is verifiable at each hop, and the mechanism is consistent
across agent protocols. More broadly, when the identity substrate changes (as
argued in Gap 1), the authentication, authorization, and data authenticity
mechanisms built on top of it benefit from being native to that substrate rather
than adapted from frameworks designed for a different identity model.

#### Metadata privacy

Even with TLS, network metadata — IP addresses, traffic
timing, packet sizes, and communication patterns — remains exposed (RFC 7624).
In multi-agent systems where frequent, structured exchanges are the norm, this
metadata can reveal workflow structures, business relationships, and operational
details. Deployments handling sensitive workflows may require metadata-minimizing
routing mechanisms beyond what TLS provides.

### Evidence from the Field

These gaps are not merely theoretical. MCP, as the more widely deployed protocol,
has attracted the most scrutiny. The OWASP MCP Top 10 identifies confused
deputy attacks — where agents with elevated privileges execute actions on behalf
of unauthorized callers — as a primary attack vector, a direct consequence of
bearer-token delegation without verified sender identity. Academic research found
5.5% of MCP servers exhibiting tool poisoning attacks through false tool
descriptions. Scans of nearly 2,000 internet-exposed MCP servers found that all
verified servers lacked any form of authentication.

Beyond deployment observations, industry practitioners, security researchers,
standards bodies, and academic publications have independently concluded that the
web-services security framework does not satisfy the trust requirements of AI agent
communication. Published analyses from the ISACA, the OpenID Foundation, the Cloud
Security Alliance, the Decentralized Identity Foundation, and researchers at
ICML 2025 all reach this conclusion. An ICML 2025 position paper argues that
authenticated delegation is critical and that current protocols cannot provide it.

### The Need for a New Trust Layer

The gaps identified above indicate that securing AI agent communication requires
more than adapting web-era authentication for agent protocols. A trust layer
designed for the characteristics of autonomous agents would need to provide
durable agent identity anchored in controller-managed cryptographic keys;
data-level authenticity and provenance
that travels with the data rather than the session; and cryptographically
verifiable, auditable delegation chains with independent revocation at every hop.

The Trust Spanning Protocol (TSP), developed by the Trust over IP Project, is a
message-oriented trust layer designed to address these requirements. TSP provides
durable identity and authenticity properties across heterogeneous systems,
anchored in long-term, controller-managed cryptographic identifiers. As long as
endpoints use identifiers based on public key cryptography with a verifiable
trust root, TSP ensures their messages are authentic.

TSP is designed to enable, not replace, agent protocols. It provides the
trust layer that protocols such as MCP, A2A, and their successors currently
lack by supplying:

- **Agent identity**: Durable, controller-managed identifiers portable across
  infrastructures, supporting multiple identifier types without requiring a
  shared trust authority. Key rotation preserves identity continuity, and
  stable identifiers serve as anchors for trust signals — formal credentials,
  peer attestations, and authenticated word of mouth that accumulate over time.

- **Data authenticity and provenance**: Message-level signatures bound to agent
  identifiers, making data artifacts verifiable across hops and over time —
  authenticity travels with the data, not the session.

- **Secure delegation**: TSP's authenticated messaging and verifiable identifiers
  provide the foundation for cryptographically verifiable delegation chains, where
  each hop can be independently audited and revoked. This specification defines
  delegation profiles built on these primitives.

By layering agent protocols over TSP, their existing developer ergonomics and
interoperability are preserved while the trust substrate is strengthened. Tool
discovery, invocation, and inter-agent coordination gain durable authenticity
and verifiable trust properties that the web stack alone does not provide.

This specification defines how TSP integrates with AI agent communication protocols
to address the security gaps described above. It specifies TSP-enabled profiles for
agent identity, authentication, authorization, delegation, and secure messaging —
defining a path from today's web-stack-dependent agent protocols to a trust
architecture designed for autonomous AI agents.