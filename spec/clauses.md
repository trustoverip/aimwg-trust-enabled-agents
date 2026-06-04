

[//]: # (Pandoc Formatting Macros)

[//]: # (Main content {#sec:content})



## TEA Reference Framework

As shown in the Reference Framework diagram below [Figure 1](#tea-reference-framework), we define TEA Agents (or simply TEAs) as units of authorization and accountability that are identified by Verifiable Identifiers (VIDs) as defined in the TSP specification and further refined in this specification. TEA Agents communicate with other TEA Agents through TSP using VIDs.

<figure id="tea-reference-framework">
  <img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/TEA-Reference-Framework.png" alt="TEA Reference Framework" width="700">
  <figcaption>Figure 1: TEA Reference Framework</figcaption>
</figure>

&nbsp;

TEA Agents therefore MUST have modules to guard, maintain and use the VIDs and their associated secrets, such as keys. Practically, we may refer to these modules as Wallets and Vaults. In other words, TEA Agents MUST have wallets.

TEA Agents conceptually MAY be composed of a Controller, one or more AI models (e.g. LLM and other models), and some methods of implementing Agent-scoped memories: storage of long term information. This conceptual composition is useful in understanding and implementing the TEA, but it is not strictly required. As AI technologies evolve rapidly, the common composition of AI Agents may also change. The TEA method itself however is not dependent on a particular way of agent composition. For example, a TEA does not necessarily require either an LLM or a specific type of long term memory. That being said, this composition is useful to illustrate many challenges we are solving in the TEA method.

The Reference Framework diagram also captures other actors in an Agentic System which are important to define authorization and accountability. In the diagram, the "Human interfaces" box represents entities (such as an application or web browser) controlled by humans or human organizations interacting with the TEA as a "user", for example, prompting or delegating. The "Management and Control Services" box represents external control systems, for example, administrative or operational controls. The "Tools, Services, other systems" box represents any external computational services. These boxes are external entities that we may reference in defining schemes for authorization and accountability.

In a TEA framework, external entities, such as a web service or a user interfacing mobile app, MAY also be TSP-Enabled. In such scenarios, these entities MAY behave just like a TEA. It is an important characteristic of the TEA framework, the overall system can expand into a network based on the common TSP layer. 

The diagram also contains a box representing other AI Agents. These Agents MAY also be TEAs, or may not. When these are TEA Agents, this opens a flexible way of constructing more complex agentic systems by a group of networked TEA Agents, where the networking among them is based on TSP.

Combining these elements, we may construct a TSP based network where each node is a TSP-Enabled entity. For the purpose of this specification, all these TSP-Enabled nodes are TEAs. This is illustrated in the next diagram [Figure 2](#tea-network).

<figure id="tea-network">
  <img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/TEA-Network.png" alt="TEA Network" width="600">
  <figcaption>Figure 2: TEA Network</figcaption>
</figure>

&nbsp;

Finally, TEA Agents can be composite, as shown in the next diagram [Figure 3](#composite-tea) by a simple example. Composite TEAs are not limited to one way of composition. This diagram is only showing a simple example for illustration. When a TEA Agent is a composite Agent, there MUST be an entity (and therefore VID) representing the whole for the purpose and requirements of being a TEA Agent. This entity is overall who/what authorities are delegated to and accountability is assigned to.

<figure id="composite-tea">
  <img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/Composite-TEA.png" alt="Composite TEA" width="400">
  <figcaption>Figure 3: Composite TEA</figcaption>
</figure>

&nbsp;


## TEAs
This section defines what agents need to do to be comformant TEAs.

A TEA is a TSP endpoint. As a TSP endpoint, the TSP specification [1[#TSP]] requires the agent MUST be secured as a distinct domain of control so that authority of operations and accountability can be uniquely assigned or attributed to a particular TEA. In the TEA Reference Framework [Figure 1](#tea-reference-framework), the agent MUST contain a Controller (or TEA Controller) that is a conformant TSP endpoint. The agent MAY also include or interact with one or more AI models, and MAY include or interact with one or more persistent memory modules.

All context, memory access and tool use within this framework by the AI models MUST go through the Controller. 

Note that an AI model served remotely by another operator may have its own context, memory or tool use outside of this framework. For the purpose of this specification, that is transparent and is considered part of the model's behavior. If required, we will explicitly state if a model is embedded within the TEA's domain of control or outside of it. Regardless, the model's access to the context, memory, tools that reside within this TEA MUST go through the Controller.

The TEA Controller MUST contain a secured data store. We will refer this data store as its Wallet throughout this specification. Only the Controller has access to the Wallet. We defer the proper implementation of such wallets to [[ref:#security-and-trust-considerations]].

The TEA Controller MUST contain a TSP Gateway to send and receive TSP messages. This TEA can communicate with other TEAs or TSP endpoints with assured authenticity, message integrity, and confidentiality and potentially meta-data privacy through this TSP Gateway. All TSP messages going out or coming in MUST go through the TSP Gateway.

The TEA Controller MAY have other communication channels other than the TSP Gateway. All such communication channels MUST be rigorously secured in order to prevent threats from breaching the Controller. For further discussions, please refer to [[ref:#security-and-trust-considerations]].

The TEA Controller MUST have one or more public VIDs and MAY have additional private VIDs. It MUST designate at least one public VID as the Introduction VID (IVID) with which first time contacts can be made without prior trust relationships. It MUST also designate at least one public VID as the Authorization VID (AVID) that is used to assign authority and accountability. By default, the AVID and IVID are the same.

The TEA Controller MUST implement the mandatory delegation and accountability functions as defined in this specification.

### Verifiable Identifiers (VIDs)

The TEA MUST use Verifiable Identifiers that are suitable for durable continuous identification. In order to meet this requirement, the VIDs MUST support key rotation and pre-rotation and allow the history of key rotations to be verifiable. TSP is interoperable with multiple VID formats but for ease of implementation and better interoperability, we prefer to choose a small number of VID schemes initially. This set MAY be extended in future.

The TEA MUST support these VIDs:
- **did:webvh**: See https://identity.foundation/didwebvh/v1.0/ (TODO: ADD reference)

The TEA MUST support the pre-rotation feature of the **did:webvh**.

## TSP Gateway

The TSP Gateway MUST support both confidential and meta-data privacy functions that are optional in TSP. It MUST also support both NaCl and HPKE-base modes.

## TSP Message Serializations

The TSP protocol specifies serialization using CESR which covers the envelope and nested and routed envelopes. In addition, it also specifies a set of control messages. For the payload data of a TSP message, TEA can use either native CESR, or JSON, CBOR or MsgPack serializations. This is a very useful feature especially when we have an existing higher layer protocols that we may want to layer over TSP.

### TEA Signed Payload

A TEA may need messages carried by TSP with a sender signature tied to one of its VIDs, for example, the AVID. Even though all TSP messages are signed in the TSP level, that signature is not usable to a third party who is not the receiver. For authorization and accountability features, a TEA will need to present proofs to such third parties.

The TEA MUST implement the native signing scheme as follows:

TODO: ADD signature to payload
TODO: Should this be part of TSP spec or here?


## Transports

TSP is agnostic to transport layer choices. For TEA, we are also agnostic to transport layer options but it will be more convenient in integration with other protocols or systems if we choose the same common options.

The TEA MUST at least support these transport options:

- **Streamable HTTP (SHTTP)**: as defined in the MCP specification (TODO: ADD reference)
- **stdio**: as defined in the MCP specification (TODO: ADD reference)

## Layering Existing Protocols over TSP as Trust Tasks

TSP is designed to support higher layer protocols, called Trust Tasks, over TSP. Such trust tasks MAY be existing commonly used protocols ported over to TSP, e.g. MCP, or can be new protocols specified in one of the following sections, or in other specifications outside of this document.

Regardless of whether a Trust Task is an existing protocol ported to TSP or a new protocol defined in this specification, the following requirements apply. A Trust Task protocol MUST conduct all of its trust-establishing communication either directly over the TSP Gateway (the Trust Spanning Layer) or over another Trust Task protocol that itself runs over TSP. A Trust Task protocol MUST NOT re-implement the authenticity, message integrity, confidentiality, or metadata-privacy guarantees that TSP already provides; it relies on the TSP Gateway for them.

Where a Trust Task carries content that a third party — one that is not the TSP receiver — must be able to verify, it MUST carry that content as a TEA Signed Payload (see [TEA Signed Payload](#tea-signed-payload)) tied to the appropriate VID, typically the AVID.

## Authenticated Exchange Protocol
 
The Authenticated Exchange Protocol is a Trust Task protocol, defined in this specification, by which two TEAs negotiate a request and the conditions under which it will be fulfilled. It is presented as a *pattern*: a deliberately minimal and flexible flow that more specialized Trust Tasks MAY follow or adapt.
 
The two roles are the **Initiator** (the TEA that opens the exchange) and the **Responder**. Both MUST be TSP endpoints identified by VIDs; either MAY be a composite TEA represented by a single VID, as defined in the [TEA Reference Framework](#tea-reference-framework).
 
<figure id="authenticated-exchange">
<img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/tea-authenticated-exchange.png" alt="Authenticated Exchange Protocol over TSP" width="700">
<figcaption>Figure 4: Authenticated Exchange Protocol as a Trust Task over TSP</figcaption>
</figure>
&nbsp;
 
Because TSP at the Trust Spanning Layer has already mutually authenticated the two VIDs, the pattern does not perform party authentication; it concerns only the *content* of the negotiation. The pattern uses four message types:
 
- **Propose** — A party states a request together with the conditions or terms of its fulfilment. Either party MAY send a Propose. A Propose sent in response to a prior Propose is a *counter-proposal* that supersedes the terms under negotiation.
- **Accept** — A party signals agreement to the most recent Propose. An Accept is *provisional* until confirmed (see Requirement 6) and MUST reference the specific Propose it accepts.
- **Ack** — The offering party's confirmation. The Ack is the act that *binds* the agreement.
- **Withdraw** — An optional message declining a Propose, or withdrawing an offer or a provisional acceptance, before it has been bound by an Ack.
A minimal exchange is `Propose → Accept → Ack`. A flexible exchange is `Propose → Propose (counter) → … → Accept → Ack`, with zero or more counter-proposals in either direction.
 
The following requirements apply to any conforming instantiation of the pattern:
 
1. Every message MUST be sent over the TSP between the Initiator's VID and the Responder's VID.
2. Each message payload MUST be carried as an Authentic Chained Data Container (ACDC), serialized as permitted in [TSP Message Serializations](#tsp-message-serializations). Where a message must be verifiable by a third party it MUST be a TEA Signed Payload (see [TEA Signed Payload](#tea-signed-payload)) tied to the sender's AVID. ACDC is the common interoperability primitive of the pattern regardless of the parties' particular pattern of negotiation — their "pattern of speech."
3. An Accept MUST reference the specific Propose it accepts by a verifiable identifier (for example, the SAID of that Propose's container).
4. A party MUST treat only the most recent superseding Propose as the live offer; earlier proposals in the same exchange are no longer acceptable once superseded.
5. **Bounding invariant.** Any message that leaves its sender exposed pending the counterparty's next action MUST carry a validity bound. A Propose MUST carry a `validUntil` bounding the time by which it may be accepted; an Accept MUST carry a `validUntil` bounding the time by which the binding Ack must be effective. An offer or acceptance MUST NOT be open-ended.
6. **Binding by affirmation.** An Accept is provisional. The agreement binds only when the offering party's Ack is effective within the Accept's `validUntil`. If no Ack is effective within that window, the acceptance lapses and nothing is bound.
7. Because the binding confirm necessarily follows acceptance, an Accept's `validUntil` will normally fall later than the accepted Propose's `validUntil`. An implementation MUST NOT set an Accept's `validUntil` such that no Ack could be effective within it.
8. A Withdraw never binds or un-binds an agreement; it is effective only in the absence of a valid Ack. The offering party MAY Withdraw its live Propose at any time before it emits a binding Ack. The accepting party MAY Withdraw its provisional Accept, but that Withdraw is effective only if no valid Ack exists within the Accept's validUntil; if a Withdraw and a valid Ack cross in flight, the Ack prevails and the Withdraw is void. The accepting party's exposure to this outcome is bounded by the validUntil it set on its Accept.
9. Timestamps used to evaluate `validUntil` MUST be the signer's own, carried in the message container, and evaluated within a defined clock-skew tolerance. The offering party's signed Ack timestamp is authoritative for the time of binding. Binding is determined solely by the existence of a valid Ack within the Accept's validUntil; it does not require comparing the Ack's timestamp against any Withdraw, so no inter-party timestamp comparison is performed.

> **Editor's note (binding model):** Requirements 5–9 adopt a hybrid of explicit validity bounds and affirmative (confirm-binds) binding. The alternatives — validity bounds alone, or affirmative binding alone — are discussed in [Design Rationale and Comparison](#design-rationale-and-comparison). The working group should confirm the hybrid before these are finalized.
 
Other Trust Tasks MAY follow alternative patterns or port existing protocols over TSP (see [Layering Existing Protocols over TSP as Trust Tasks](#layering-existing-protocols-over-tsp-as-trust-tasks) and [MCP over TSP](#mcp-over-tsp)); in every case the requirement is that they run over TSP and, where third-party verifiability is needed, carry their content as TEA Signed Payloads.

## MCP over TSP

TODO

## Delegation of Authorization and Duty

A TEA confers authority on another TEA by *delegation*. A delegation conveys two things together: an **authorization** — what the Delegate may do — and a **duty** — the obligations and conditions the Delegate accepts in exercising it, and for which it is accountable. Both are carried in a single authorization ACDC issued by the Delegator to the Delegate.
 
Consistent with the [TEA Reference Framework](#tea-reference-framework), authority is delegated to, and accountability assigned to, the entity (and VID) representing the Delegate. A delegation is issued under, and verified against, the Delegator's Authorization VID (AVID).
 
Because an authorization is itself verifiable information, a delegation can be delivered in band: the [Authenticated Exchange Protocol](#authenticated-exchange-protocol) settles the scope and the duties, and the binding act (the Ack) coincides with issuance of the authorization ACDC to the Delegate.
 
An authorization ACDC uses the three ACDC sections for distinct purposes:
 
- the **attribute section** names the Delegate (the Issuee) and the granted scope — the resource and ability conferred;
- the **rule section** carries the conditions as Ricardian clauses: both negative caveats that restrict the authority and affirmative duties the Delegate must fulfil;
- the **edge section** chains, via the I2I operator, to the ACDC that establishes the Delegator's own authority.
Requirements:
 
1. A delegated authority MUST be expressed as an authorization ACDC issued by the Delegator (as Issuer) to the Delegate (as Issuee), under the Delegator's AVID.
2. The authorization ACDC MUST either (a) include an edge referencing the ACDC that establishes the Delegator's own authority, using the I2I operator (or the DI2I operator where the Delegator's identifier is itself delegated), so that the Issuer of the delegation is constrained to be the Issuee of the authority being delegated; or (b) be a root issuance, in which the Issuer is the authority that controls the resource and against which the relying party roots trust, and which therefore carries no incoming authority edge.  Case (a) is the structural expression that a party may only delegate authority it holds; case (b) is the origin of that authority. A verifier MUST accept a root issuance only when it roots trust in that Issuer for the resource in question.
3. A delegated authority MUST carry a `validUntil` validity bound and MUST be revocable through its credential status registry.
4. Verification of a delegated authority MUST confirm, at the time of exercise, that every ACDC on the relevant chain is non-revoked and within its validity window.
### Attenuated Re-delegation
 
Re-delegation MUST be supported, MUST be general and composable — chains of arbitrary depth, and authority composable from more than one source — and MUST be strictly attenuating: authority can only narrow as it is re-delegated. This is achieved with a capability model whose narrowing is intrinsic rather than merely checked.
 
An authorization is modeled as a **capability object**: a *scope* (a resource and an ability) drawn from a defined resource/ability model, and a set of *caveats and duties* drawn from a defined vocabulary, carried in the attribute and rule sections respectively. Capability objects MUST be defined as elements of a structure equipped with a partial order ≤ ("is at most as permissive as") and a meet operation ∩ (intersection).
 
1. A re-delegation MUST be expressed as a *restriction* relative to its source: the re-delegating authorization ACDC carries only the additional caveats, duties, and narrowed scope introduced at that step, not an independently restated capability.
2. The *effective authority* of any node in a delegation chain is the meet (∩) of the source authority or authorities reached through its edges with the restriction expressed at that node. Because the meet is monotone (a ∩ b ≤ a), the effective authority of any node is necessarily no more permissive than each of its sources. Strict attenuation is therefore a property of the construction: provenance is supplied by the I2I edge (Requirement 2 above) and monotonic narrowing by the restriction-only encoding.
3. An authority MAY be composed from multiple source authorities using ACDC m-ary edge-group operators. Under an `AND` edge-group, the effective authority requires all referenced sources and is the meet across them; under an `OR` edge-group, any one valid source suffices; edge-groups MAY be nested to express arbitrary boolean combinations. The validity of a composed authority MUST be evaluated, at the time of exercise, over the live status (revocation and validity window) of each referenced source: under `AND`, revocation or expiry of any required source invalidates the composed authority; under `OR`, the authority remains valid while at least one referenced source is valid.

### Closed Capability Core and Open Policy Extension
 
1. This specification defines a **closed capability core**: a bounded caveat/duty vocabulary and a resource/ability model whose partial order and meet are total, deterministic, and decidable. A conforming verifier MUST compute the effective authority of a delegation chain by reduction — taking the meet along the chain — using only the closed-core semantics. This reduction requires no general policy engine and yields the same result for every conforming verifier.
2. An implementation MAY extend the rule section with an **open policy expression** for conditions not expressible in the closed core. Open caveats MUST be evaluated by the designated policy model, and MUST only further restrict the effective authority; they MUST NOT broaden the authority computed from the closed core.
3. **Soundness property.** For any delegation chain expressed solely with the closed capability core, no node's effective authority can exceed the authority of its root or roots. This property is intended to be stated and proven as part of [Security and Trust Considerations](#security-and-trust-considerations).
> **Editor's note (open items):** The concrete closed-core caveat/duty vocabulary and resource/ability lattice are not yet fixed; they should be chosen to keep the meet operation tractable. The semantics for merging caveats and duties across an `AND` composition of multiple sources must be specified or explicitly deferred to the open policy model. Existing capability and chained-authorization patterns in the KERI/ACDC community should be reviewed before fixing an encoding.
 
## Accountability

Authorization and accountability are distinct functions served by the same structure. A chain of ACDCs, read from root to leaf, expresses the delegation and provenance of authority; read from leaf to root, the same chain is the accountability trace. A TEA is therefore a single unit of authorization *and* accountability because both derive from one cryptographic structure.
 
Accountability is retrospective and evidentiary: it answers who agreed to or did what, and whether it can be proven after the fact. The ACDC chain provides accountability directly — each container is signed, chained, and non-repudiable — and, when carried as a TEA Signed Payload tied to the AVID, it can be presented to and verified by a third party that did not observe the original interaction.
 
Requirements:
 
1. An authorization or agreement that must be accountable to a third party MUST be recorded as a TEA Signed Payload (see [TEA Signed Payload](#tea-signed-payload)) tied to the responsible party's AVID.
2. The accountability chain of any authority a TEA holds or exercises MUST terminate in a principal — a natural person or organization — that can bear accountability. A TEA MUST NOT be the terminal responsible party for an authority.
3. An authorization MUST NOT be inferred solely from a record that an agreement occurred; conferral of authority requires an authorization ACDC as defined in [Delegation of Authorization and Duty](#delegation-of-authorization-and-duty).

## Worked Example: Delegated Service Access

*This section is informative. It illustrates the preceding clauses with the simplest end-to-end scenario: a principal who authorizes an agent to use a service on the principal's behalf.*

### Actors

- **BookingService (S)** — the Service API and Relying Party. As the owner of the resource being accessed, S is the **root of authority** over that resource and is also the party that verifies access to it.
- **Alice** — the **Principal**: the account holder at S and the accountable party. Alice holds `AVID_Alice`.
- **Agent** — Alice's TEA and the **Delegate**, holding `AVID_Agent`.

Because the resource owner is the natural root of authority, the chain of authority runs **S → Alice → Agent**, and every link is a uniform I2I delegation. S therefore verifies a chain that roots in its own issuance, needing no external trust anchor — which mirrors how account-based services actually work.

### The authority chain

**Hop 1 — `Cred_S→Alice` (root issuance).** S authorizes Alice as the holder of her account. This is the root of the chain: S owns the resource, so this issuance has no incoming authority edge.

```
Cred_S→Alice
  i:  AVID_S                         # Issuer  = the service (root of authority)
  a:
    i:  AVID_Alice                   # Issuee  = Alice
    scope:
      resource: bookingservice:account/alice
      ability:  [ create-booking, cancel-booking, view ]
  r:  { account terms ... }
  ri: <S's status registry>
  (no authority edge — S is the root of authority over the resource)
```

**Hop 2 — `Cred_Alice→Agent` (I2I re-delegation, attenuated).** Alice re-delegates a narrowed slice to her Agent. The grant is expressed as a *restriction* relative to the parent: a smaller ability set, plus caveats and a duty.

```
Cred_Alice→Agent
  i:  AVID_Alice                     # Issuer  = Alice
  a:
    i:  AVID_Agent                   # Issuee  = the Agent
    scope:
      resource: bookingservice:account/alice
      ability:  [ create-booking ]   # attenuated: subset of Alice's abilities
  r:
    caveats: { maxAmount: 500 USD, category: [ flights ] }
    duties:  { report: { to: AVID_Alice, on: each-booking } }
  e:
    auth:
      n: <SAID of Cred_S→Alice>      # far node = the parent authority
      o: I2I                         # Issuer(this) MUST be Issuee(parent): Alice = Alice  ✓
  validUntil: <now + 7 days>
  ri: <Alice's status registry>
```

### Effective authority (the meet)

The Agent's effective authority is the meet (∩) along the chain:

```
  Alice's authority   : { create-booking, cancel-booking, view } on alice's account
  ∩ Alice→Agent delta : ability ⊆ { create-booking }; amount ≤ $500; category = flights; within 7 days
  = Agent's authority : create-booking on alice's account, ≤ $500, flights only, until T+7d
```

This is strictly ≤ Alice's authority, which is ≤ S's grant — the narrowing is intrinsic because each hop carries only a restriction. A request to book a $420 flight falls inside this set and is permitted; a $900 booking, a hotel, or a request after day 7 would not.

### Runtime

<figure id="delegated-access-example">

<img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/tea-delegated-access-example.png" alt="Delegated service access: authority chain S to Alice to Agent" width="760">

<figcaption>Figure 5: Delegated service access — authority chain S → Alice → Agent</figcaption>

</figure>

&nbsp;

1. **Delegation.** Over their TSP channel, Alice and the Agent run the [Authenticated Exchange Protocol](#authenticated-exchange-protocol) to settle the terms; Alice's binding Ack issues `Cred_Alice→Agent` to the Agent in band.
2. **Access.** The Agent opens an exchange with S, proposing a concrete booking (a $420 flight) and presenting the chain `{ Cred_Alice→Agent ▸ Cred_S→Alice }` as a TEA Signed Payload (see [TEA Signed Payload](#tea-signed-payload)) tied to `AVID_Agent`.
3. **Verification.** S confirms the chain roots in its own issuance (`AVID_S`); that the I2I edge holds (Alice is the Issuee of the authority she re-delegated); that the leaf Issuee is the TSP counterparty presenting it; that the meet of the chain covers the requested operation; and that no ACDC on the chain is revoked or past its `validUntil`. S then Accepts/Acks and creates the booking.
4. **Duty.** The Agent discharges its duty by reporting the booking to Alice as a signed payload.

### What the example demonstrates

- **The service is both the root and the verifier.** S validates a chain rooted in its own grant, so no external anchor is needed — the base "root issuance" case lands naturally on the resource owner, and the normative text needs no special "root principal" carve-out beyond "the chain roots in the authority that controls the resource."
- **Re-delegation is a uniform I2I hop**, and attenuation is intrinsic: the Agent cannot exceed what Alice holds, which cannot exceed what S granted.
- **One chain, two readings.** Read S → Agent, it is the delegation of authority; read Agent → S, it is the accountability trace, terminating in Alice — a principal, never the Agent.

## Design Rationale and Comparison
 
*This section is non-normative. It situates the design choices above against commonly used schemes for the benefit of reviewers and implementers; it imposes no requirements.*
 
### Negotiation and binding
 
The Authenticated Exchange's binding model is, in effect, a peer-to-peer two-phase commit: the offering party's Ack is the commit decision. Unlike classical two-phase commit it needs no coordinator and does not block on a failed coordinator, because the `validUntil` bounds turn an unresponsive counterparty into a clean lapse rather than an indefinite hang — the same hardening production systems add to two-phase commit through presumptive-abort timeouts.

Race resolution and the inconsistency window. The asymmetry between Withdraw and Ack is the same asymmetry two-phase commit draws between a participant's vote and the coordinator's decision. An Accept is a vote-commit: once cast, the accepting party honors it until its validUntil lapses, and cannot unilaterally abort a decision the offering party may still make within that window. The offering party's Ack is the commit decision, and it is supreme — a Withdraw is effective only in the absence of a valid Ack, so a Withdraw that races a binding Ack simply loses. This is what makes the resolution deterministic without a coordinator and without comparing the two parties' self-signed timestamps against each other: binding turns solely on whether a valid Ack exists within the window, a question with one answer that an adversary cannot tilt by backdating. The price is that the accepting party cannot be certain a late Withdraw will land; it may lose to an Ack already in flight. That uncertainty is exactly the residual bounded inconsistency window noted below — it is bounded by the validUntil the accepting party itself chose, and no coordinator-free commit can eliminate it.
 
The closest market analogue is request-for-quote trading with "last look," in which a maker may reject a deal within a window after a taker hits a time-bounded quote. Last look is widely criticized because it is one-sided: it grants the maker a free option at the taker's expense. The symmetric `validUntil` on both Propose and Accept answers that critique — it bounds the offeror's confirm window the way it bounds the acceptor's offer window — so the design can be characterized as affirmative binding without the last-look asymmetry.
 
Hashed-timelock contracts (HTLCs) for atomic swaps are a closer fit on the symmetry of timeouts but solve a different problem: HTLCs provide atomic *exchange of value*, whereas the Authenticated Exchange provides *agreement on terms*. The Ack binds what the parties agreed; it does not guarantee performance. Where atomic execution of an agreed obligation is required, that is a separate Trust Task layered above the negotiation. Token-expiry schemes (OAuth/JWT `exp`/`nbf`) informed the `validUntil` mechanism but are not negotiations; GNAP is the closest negotiation-of-authority analogue but is authorization-server-mediated and centralized, whereas this design is peer-to-peer over TSP with the outcome captured in a portable ACDC.
 
| Scheme | How offers are bounded | What binds | Durable verifiable record? | Notable weakness |
|---|---|---|---|---|
| Two-phase commit | Coordinator timeout (often absent → blocking) | Coordinator commit | No (ephemeral logs) | Needs a trusted coordinator; blocks on its failure |
| RFQ + last look | Quote firm-for-N-seconds | Maker confirm after taker hits | Venue logs, not portable | Asymmetric free option for the maker |
| HTLC / atomic swap | On-chain timelocks | Preimage before timeout | Yes (on-ledger) | Needs a ledger and the asset; value exchange only |
| OAuth2 / JWT | `exp` / `nbf` in token | One-shot issuance | Token verifiable, not a negotiation record | Clock-dependent; not a negotiation |
| GNAP | Protocol-level | Grant issuance | Server-side | Authorization-server-mediated; centralized |
| IPEX | Not expiry-first | grant/admit | Yes (ACDC) | Disclosure only; expiry not first-class; draft incomplete |
| Authenticated Exchange | Symmetric `validUntil` on Propose and Accept | Affirmative Ack (confirm-binds) | Yes (ACDC-chained, portable) | Extra round-trip; clock-skew residue; binds terms, not performance |
 
The distinctive position of the Authenticated Exchange is that it is the only one of these combining decentralized peer operation (no coordinator, certificate authority, venue, or ledger), symmetric exposure bounding, and a portable non-repudiable record verifiable after the fact. The honest costs are an extra round-trip, a residual bounded inconsistency window that no timeout-based commit can fully eliminate — the window in which an accepting party's Withdraw may lose to a racing Ack, bounded by its own validUntil — and a remaining clock dependence that affirmative binding reduces but does not remove.
 
### Delegation and attenuation
 
The capability model draws on established authorization-capability designs. SPKI/SDSI is the formal precedent for computing effective authority as the *intersection* of authorizations along a delegation chain (tag intersection / certificate-chain reduction); the closed-core "meet along the chain" rule is the same idea over ACDC edges. Macaroons achieve strict attenuation structurally by making caveats append-only — a restriction can be added but not removed — which is the model for the restriction-only re-delegation encoding. UCAN provides decentralized, DID-native capability chains with an attenuation-checked subsumption order, and ZCAP-LD is the linked-data cousin in which caveats accumulate down the chain.
 
The closest ACDC-native precedent is the GLEIF vLEI credential chain (a role credential chained to a Legal Entity credential chained to a Qualified vLEI Issuer credential), itself a delegation-of-authority chain expressed entirely through ACDC edges with the I2I operator. The principal way this specification goes beyond IPEX — the nearest ACDC-native exchange protocol — is that IPEX provides no validity bounds or attenuation semantics at all (its security considerations were never completed), so the bounding and strict-attenuation apparatus here is additive rather than a restatement.
 
The rationale for favouring a closed capability core in this specification is that it is the part whose security can be proven: a capability structure with a defined meet supports a soundness theorem — no chain of delegations can yield authority exceeding its root — which is a materially stronger guarantee than re-checking attenuation with a general policy engine on every exercise. The open policy extension remains available for conditions the closed core cannot express, on the understanding that those conditions trade the structural guarantee for an evaluation dependency.
 
 ## Security and Trust Considerations

TODO

