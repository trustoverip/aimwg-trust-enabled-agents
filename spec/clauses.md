

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

### TSP Gateway

The TSP Gateway MUST support both confidential and meta-data privacy functions that are optional in TSP. It MUST also support both NaCl and HPKE-base modes.

### TSP Message Serializations

The TSP protocol specifies serialization using CESR which covers the envelope and nested and routed envelopes. In addition, it also specifies a set of control messages. For the payload data of a TSP message, TEA can use either native CESR, or JSON, CBOR or MsgPack serializations. This is a very useful feature especially when we have an existing higher layer protocols that we may want to layer over TSP.

### TSP Signed Payload

A TEA often needs a payload carried by TSP to bear a sender signature tied to one of its VIDs — typically the AVID — that a **third party** can verify. Although TSP authenticates messages at the Trust Spanning Layer, that authentication assures only the *receiver* and is not transferable: a third party who did not receive the message cannot use it as proof of authorship. Authorization and accountability both require presenting such proofs to parties who were not the receiver, so the TEA defines a transferable, non-repudiable signed payload.

The scheme is deliberately general — "**sign the SAID under the VID**" — and is independent of the payload's serialization and of whether the payload is an ACDC. A **TSP Signed Payload** consists of:

1. a **payload**, in any serialization permitted by [TSP Message Serializations](#tsp-message-serializations) (native CESR, JSON, CBOR, or MsgPack);
2. a **SAID** — the self-addressing digest over that payload (which an ACDC already carries in its `d` field; for any other payload it is computed the same way);
3. one or more **signatures over that SAID**, produced under the key state of the signing TEA's VID and carried as CESR attachments.

Requirements:

1. The signature(s) MUST be produced under the signing VID's key state and MUST be verifiable by any party that can resolve that VID (e.g. via the **did:webvh** key history), independently of any TSP message-level signature.
2. The signing VID MUST be identified so a verifier knows whose key state to resolve. For authorization and accountability content this VID MUST be the signer's AVID.
3. A verifier MUST: (a) recompute the SAID over the payload and confirm it matches; (b) resolve the signing VID's key state; and (c) verify the signature(s) against that key state.
4. Where the payload's validity depends on time (for example a `validUntil`, or a key rotation in the signer's VID history), the signature MUST be evaluated against the signing VID's key state as of the signer's own timestamp, within a defined clock-skew tolerance, consistent with the timestamp rules of the [Authenticated Exchange Protocol](#authenticated-exchange-protocol).
5. The TSP Signed Payload is **in addition to**, not a substitute for, TSP message-level signing; the presence of one does not satisfy the requirement for the other.

ACDC adds essentially nothing to this scheme. Where the payload is an ACDC, the TSP Signed Payload signature is simply the ACDC's issuer signature over its SAID, produced under the issuer's AVID — no additional signature is required. Verification proceeds as above, with one addition: for an issued credential the verifier MUST also check its validity and, where it carries a revocation registry (`rd`), its non-revocation, as required by [Delegation Exchange](#delegation-exchange). Thus an authorization ACDC issued under the AVID, presented with its signature attachments, *is* a TSP Signed Payload.

> **Note (placement).** This scheme reuses primitives already present in TSP and the KERI/ACDC stack (self-addressing identifiers and VID-keyed signatures); it is specified here as a TEA profile binding them to the AVID and to third-party verifiability. It could be upstreamed into the TSP specification unchanged.


### Transports

TSP is agnostic to transport layer choices. For TEA, we are also agnostic to transport layer options but it will be more convenient in integration with other protocols or systems if we choose the same common options.

The TEA MUST at least support these transport options:

- **Streamable HTTP (SHTTP)**: as defined in the MCP specification (TODO: ADD reference)
- **stdio**: as defined in the MCP specification (TODO: ADD reference)

### Layering Protocols over TSP as Trust Tasks

TSP is designed to support higher layer protocols, called Trust Tasks, over TSP. Such trust tasks MAY be existing commonly used protocols ported over to TSP, e.g. MCP, or can be new protocols specified in one of the following sections, or in other specifications outside of this document.

Regardless of whether a Trust Task is an existing protocol ported to TSP or a new protocol defined in this specification, the following requirements apply. A Trust Task protocol MUST conduct all of its trust-establishing communication either directly over the TSP Gateway (the Trust Spanning Layer) or over another Trust Task protocol that itself runs over TSP. A Trust Task protocol MUST NOT re-implement the authenticity, message integrity, confidentiality, or metadata-privacy guarantees that TSP already provides; it relies on the TSP Gateway for them.

Where a Trust Task carries content that a third party — one that is not the TSP receiver — must be able to verify, it MUST carry that content as a TSP Signed Payload (see [TSP Signed Payload](#tsp-signed-payload)) tied to the appropriate VID, typically the AVID.

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
2. Each message payload MUST be carried as an Authentic Chained Data Container (ACDC), serialized as permitted in [TSP Message Serializations](#tsp-message-serializations). Where a message must be verifiable by a third party it MUST be a TSP Signed Payload (see [TSP Signed Payload](#tsp-signed-payload)) tied to the sender's AVID. ACDC is the common interoperability primitive of the pattern regardless of the parties' particular pattern of negotiation — their "pattern of speech."
3. An Accept MUST reference the specific Propose it accepts by a verifiable identifier (for example, the SAID of that Propose's container).
4. A party MUST treat only the most recent superseding Propose as the live offer; earlier proposals in the same exchange are no longer acceptable once superseded.
5. **Bounding invariant.** Any message that leaves its sender exposed pending the counterparty's next action MUST carry a validity bound. A Propose MUST carry a `validUntil` bounding the time by which it may be accepted; an Accept MUST carry a `validUntil` bounding the time by which the binding Ack must be effective. An offer or acceptance MUST NOT be open-ended.
6. **Binding by affirmation.** An Accept is provisional. The agreement binds only when the offering party's Ack is effective within the Accept's `validUntil`. If no Ack is effective within that window, the acceptance lapses and nothing is bound.
7. Because the binding confirm necessarily follows acceptance, an Accept's `validUntil` will normally fall later than the accepted Propose's `validUntil`. An implementation MUST NOT set an Accept's `validUntil` such that no Ack could be effective within it.
8. A Withdraw never binds or un-binds an agreement; it is effective only in the absence of a valid Ack. The offering party MAY Withdraw its live Propose at any time before it emits a binding Ack. The accepting party MAY Withdraw its provisional Accept, but that Withdraw is effective only if no valid Ack exists within the Accept's `validUntil`; if a Withdraw and a valid Ack cross in flight, the Ack prevails and the Withdraw is void. The accepting party's exposure to this outcome is bounded by the validUntil it set on its Accept.
9. Timestamps used to evaluate `validUntil` MUST be the signer's own, carried in the message container, and evaluated within a defined clock-skew tolerance. The offering party's signed Ack timestamp is authoritative for the time of binding. Binding is determined solely by the existence of a valid Ack within the Accept's `validUntil`; it does not require comparing the Ack's timestamp against any Withdraw, so no inter-party timestamp comparison is performed.

> **Editor's note (binding model):** Requirements 5–9 adopt a hybrid of explicit validity bounds and affirmative (confirm-binds) binding. The alternatives — validity bounds alone, or affirmative binding alone — are discussed in [Design Rationale and Comparison](#design-rationale-and-comparison). The working group should confirm the hybrid before these are finalized.
 
Other Trust Tasks MAY follow alternative patterns or port existing protocols over TSP (see [Layering Existing Protocols over TSP as Trust Tasks](#layering-existing-protocols-over-tsp-as-trust-tasks) and [MCP over TSP](#mcp-over-tsp)); in every case the requirement is that they run over TSP and, where third-party verifiability is needed, carry their content as TSP Signed Payloads.

## Delegation Exchange

A TEA confers authority on another TEA by *delegation*. A delegation conveys things of two different kinds: an **authority** — what the Delegate may do — and **post-gate policy** — the obligations the Delegate accepts and the assumptions the authority rests on, settled after the act rather than at the gate. All are carried in a single authorization ACDC issued by the Delegator to the Delegate.

Consistent with the [TEA Reference Framework](#tea-reference-framework), authority is delegated to, and accountability assigned to, the entity (and VID) representing the Delegate. A delegation is issued under, and verified against, the Delegator's Authorization VID (AVID).

Because an authorization is itself verifiable information, a delegation can be delivered in band: the [Authenticated Exchange Protocol](#authenticated-exchange-protocol) settles the authority and the accompanying policy, and the binding act (the Ack) coincides with issuance of the authorization ACDC to the Delegate.

### The intelligent agent assumption

This specification assumes that TEA agents are capable. The protocol provides a **common language and a communication channel** for expressing and exchanging authority and obligations; it is **not** a reasoning engine and does not, by itself, evaluate the wisdom, the consistency, or the satisfiability of what the parties agree to. A TEA is assumed to evaluate, on its own and outside the scope of the protocol, the terms it accepts — including whether the obligations imposed on it are mutually consistent and whether it is willing and able to bear them — just as a real-world party evaluates a contract before signing it.

The protocol therefore mandates only two things about delegated content: that authority **attenuates** and that obligations **accumulate** as delegation proceeds, and that both remain **verifiable after the fact**. Conflict resolution, penalties for non-performance, and the decision to accept in the first place are the responsibility of the parties and of the [Accountability](#accountability) layer.

### Kinds of stated policy

A delegation states policy of three kinds, separated by two questions: is it a **fact the authority rests on** or a **duty someone owes**; and is it **checkable at the gate** or **only knowable later**?

| | checkable at the gate | only knowable later |
|---|---|---|
| **fact the authority rests on** | **limitation** | **assumption** |
| **duty someone owes** | — | **obligation** |

The fourth cell is structurally empty: a duty required *before* acting is a precondition, and a precondition is a limitation.

An **authority** is modeled as a **capability**: a triple

```
capability = ( resource, ability, limitation )
```

where the **resource** is *which object*, the **ability** is *which operation*, and the **limitation** is *under what limits* the operation may be exercised. All three are part of the permission boundary and all three narrow in the same direction (toward less). The authorization decision — "is a given request within authority?" — is computed **only** from the capability, and composes by meet (∩) down a delegation chain.

**Obligations** and **assumptions** together form the **post-gate policy**. They ride alongside the capability, do not enter the authorization decision, accumulate by union (∪) down the chain, and are settled after the act.

- An **obligation** is a duty the Delegate owes — to report, notify, settle, or watch. If it is unmet, the obligor is answerable: a **breach**.
- An **assumption** is a fact the authority rests on that the Delegate can neither control nor observe. Nobody is obliged to make it true. If it proves false the act is undermined but no one is at fault — it goes to **validity**, not breach. Assumptions are the same device as a contract's "whereas" clauses, and like those they work only because they are disclosed up front and signed.

Obligations are the Delegate's exposure; assumptions are its cover. The line between them is therefore the risk allocation itself, written down and signed: stating something as an assumption retains that risk with the Delegator; stating it as an obligation places it on the Delegate.

An assumption belongs only if it **conditions the authority**. If it does not bear on the grant, it does not belong in the credential.

> **The time test.** A fact decidable at the moment of exercise that *gates* the action is a **limitation**. A duty that may come due *later* is an **obligation**. A fact the authority rests on that can only be known later, and that the Delegate can neither control nor observe, is an **assumption**. "Consent must already exist before booking" is a limitation; "report each booking afterward" is an obligation; "the principal had capacity to grant this" is an assumption.

> **Prefer an obligation where the Delegate can observe.** If the Delegate *can* watch for a fact, do not state it as an assumption — state the duty to watch for it and act, which is an obligation. What remains an assumption is only what the Delegate can neither control nor observe.

### ACDC encoding

An authorization ACDC uses the three ACDC sections for distinct purposes:

- the **attribute section** (`a`) carries the **capability**: the Issuee (the Delegate), the granted resource and ability, and the limitations;
- the **rule section** (`r`) carries the **post-gate policy**: the obligations the Delegate owes and the assumptions the authority rests on, each SAID-committed and, where useful, carrying structured parameters alongside its rendering;
- the **edge section** (`e`) chains, via the I2I operator, to the ACDC that establishes the Delegator's own authority.

```
a:                                   # capability — the "may I?"
  i: <Issuee VID>
  resource: <selector>               # omitted ⇒ inherit from parent
  ability:  [ … ]                    # omitted ⇒ inherit from parent
  limitations:                       # omitted keys ⇒ no limit on that axis
    <limitation-type>: <bound>
    validUntil: <ts>                 # expiry as a bounded-scalar limitation (meet = min); absent ⇒ no expiry
r:                                   # post-gate policy
  obligations:                       # duties the Delegate owes
    - t:  <obligation-type>          # registry term
      to: <beneficiary VID>
      on: <trigger>                  # the antecedent (see Obligations)
      by_when: <deadline>
      d:  <SAID of this clause>
      l:  "<rendering>"
  assumptions:                       # facts the authority rests on
    - d:  <SAID of this clause>
      l:  "<rendering>"
e:
  auth: { n: <parent SAID>, o: I2I } # or DI2I; absent at a root issuance
rd: <registry SAID> # optional — present ⇒ revocable (indirect mode); absent ⇒ expiry-only
```

Requirements:

1. A delegated authority MUST be expressed as an authorization ACDC issued by the Delegator (as Issuer) to the Delegate (as Issuee) — a Targeted ACDC — under the Delegator's AVID. An Untargeted ACDC, having no Issuee, cannot carry a delegation.
2. The authorization ACDC MUST either (a) include an edge referencing the ACDC that establishes the Delegator's own authority, using the I2I operator (or the DI2I operator where the Delegator's identifier is itself delegated), so that the Issuer of the delegation is constrained to be the Issuee of the authority being delegated; or (b) be a root issuance, which carries no incoming authority edge. Case (a) is the structural expression that a party may only delegate authority it holds; case (b) is the origin of authority over the resource. Whether a given root is trusted is a relying-party decision and is out of scope of this specification.
3. A non-root delegated authority MUST bound its validity with a `validUntil` time limitation — a bounded-scalar limitation whose meet is the earlier bound, so validity can only shorten as authority is re-delegated. It MAY additionally be made revocable by issuing it in indirect mode with a revocation registry (see Freshness); revocation adds best-effort early termination whose effectiveness is deployment-specific, and is never a substitute for the validUntil bound. A root issuance MAY be open-ended, expressed as the absence of the limitation (⊤ = no expiry).
4. Verification of a delegated authority MUST confirm, at the time of exercise, that every ACDC on the relevant chain is within its validity window and, for any ACDC issued with a revocation registry, not revoked in that registry's current state.

### Attenuated Re-delegation

Re-delegation MUST be supported, MUST be general and composable — chains of arbitrary depth, and authority composable from more than one source — and MUST be strictly attenuating: authority can only narrow as it is re-delegated. This is achieved with a capability model whose narrowing is intrinsic rather than merely checked.

#### The capability is a product of three lattices

Each of the three capability axes is a **bounded meet-semilattice**: a set of possible values equipped with a partial order ≤ ("is at most as permissive as"), a meet ∩ (greatest lower bound), and a top element ⊤ (the most-permissive value — no narrowing on that axis). A product of bounded meet-semilattices is itself a bounded meet-semilattice whose meet is taken component-wise; this is what makes the capability meet **total, deterministic, order-independent, and decidable**.

All three axes narrow in the same direction:

| axis | value | ≤ means | meet ∩ | top ⊤ | narrow by |
|---|---|---|---|---|---|
| **resource** | a selector over a resource namespace | "is a sub-resource of" | most-specific common selector (⊥ if disjoint) | the whole granted namespace | specializing the selector |
| **ability** | a subset of an ability vocabulary | ⊆ | set intersection | the full vocabulary granted | removing abilities |
| **limitation** | a map: limitation-type → bound | "at least as tight on every axis" | per-type meet | empty map (no limits) | adding or tightening a limit |

`C₁ ≤ C₂` holds iff resource, ability, and limitation are each ≤. Because ⊤ is the identity for meet (`x ∩ ⊤ = x`), an axis left unspecified at a re-delegation sits at ⊤ and is inherited unchanged from the parent. This is the formal reason the **restriction-only encoding** below is sound: omission means *inherit*; presence means *narrow*.

#### Restriction-only encoding and effective authority

1. A re-delegation MUST be expressed as a *restriction* relative to its source: the re-delegating authorization ACDC carries only the narrowed resource/ability and the additional limitations and obligations introduced at that step, not an independently restated capability.
2. The **effective capability** of any node is the meet (∩) of the source capability or capabilities reached through its edges with the restriction expressed at that node. Because the meet is monotone (`a ∩ b ≤ a`), the effective capability of any node is necessarily no more permissive than each of its sources. Strict attenuation is therefore a property of the construction: provenance is supplied by the I2I edge and monotonic narrowing by the restriction-only encoding.
3. The **effective obligation set** of any node is the union (∪) of the obligations along its chain. Obligations only accumulate; a re-delegation MAY add an obligation but a node can never hold fewer obligations than its chain imposes.

#### Composition from multiple sources

An authority MAY be composed from multiple source authorities using ACDC m-ary edge-group operators, which MAY be nested to express arbitrary boolean combinations.

- Under an **`AND`** edge-group, the effective capability is the meet across all referenced sources (then met with the node's restriction), and the effective obligation set is the union across all sources. At the time of exercise, revocation or expiry of any required source invalidates the composed authority.
- Under an **`OR`** edge-group, any one valid source suffices. `OR` is **not** a join of capabilities (a join would broaden authority and break attenuation); it is a per-request disjunction: a request is within authority iff it falls within `effective-capability(sₖ) ∩ restriction` for *some* currently-valid source `sₖ`. Each branch is reduced and attenuated independently. At the time of exercise, the composed authority remains valid while at least one referenced source is valid.

### Resources, Abilities, and Limitations

The capability axes draw their concrete values from **namespaces**. Both owner-local and community-wide namespaces are supported and coexist; each is published and SAID-addressed. **Trust in a namespace comes from the self-addressing identifier, not from any authority that serves it** — the identifier self-certifies the content it names, so whatever source supplies a definition, the SAID determines whether it is the one the issuer meant.

How many sources serve a given namespace is therefore an **availability** question, not a trust one. A single registry is the N=1 case and is admissible; replicating for availability and censorship-resistance is an independent choice. An operator may adopt content commitment without changing its serving topology, and may distribute later. A sole source cannot substitute a definition undetected, but it can withhold one — so resolution failure costs the ability to decide, never the correctness of a decision actually made (see [Key management and freshness](#key-management-and-freshness)).

**Resources.** This specification does not mandate a universal resource-naming scheme; a resource is identified using the resource owner's own convention (typically an API path or URL). It does, however, require that whatever scheme is used be a bounded meet-semilattice — i.e. that it carry a **decidable "is-sub-resource-of" order, a meet, and a top element**. This requirement is a security property, not a convenience: without a decidable order a verifier cannot distinguish a genuine attenuation (a sub-resource) from a lateral escalation (a *different* resource), and restriction-only re-delegation would be unsafe. A URL path hierarchy satisfies this directly — `A ≤ B` iff `A`'s path extends `B`'s by further segments; the meet of two paths is the more specific when one is a prefix of the other and ⊥ (disjoint, no shared authority) otherwise — and is the RECOMMENDED default. Owners MAY use richer schemes (query selectors, attribute filters) provided they supply the order, meet, and ⊤.

**Abilities.** The set of abilities for a resource type is an ability vocabulary defined by the resource owner or by a community namespace, published and SAID-addressed. The ability axis is the powerset of that vocabulary under ⊆, with meet = intersection and ⊤ = the full granted set.

**Limitations.** A limitation is a typed bound. Each **limitation-type** selects one of the **value-lattice kinds defined by this specification** — it does not supply its own meet. This is what keeps the closed core *closed*: the meet follows from the kind alone, so every conforming verifier computes the same result with no namespace-supplied logic to execute. This specification defines the following value-lattice kinds:

- **bounded scalar** (e.g. `maxAmount`): meet = `min`, ⊤ = ∞;
- **membership set** (e.g. `category ⊆ {flights, hotels, …}`): meet = ∩, ⊤ = the full set;
- **interval** (e.g. an allowed time window): meet = interval intersection, ⊤ = (−∞, +∞).

An absent limitation-type sits at ⊤ on that axis (no constraint). The meet of two limitation maps is the per-type meet, treating absent keys as ⊤. Limitation-types are declared in a namespace (community-wide or owner-local), each declaring its identifier, value domain, the value-lattice **kind** it uses (from those defined above), its ⊤ element, and a rendering template.

**Expression languages and profiles.** The closed capability core is the **pinned evaluation semantics**: it fixes what a limitation *means* and how limitations compose. It does not fix how one is *written*. An implementation MAY express limitations in an existing policy language, provided that language is used through a **named syntactic profile** — a published fragment of its grammar together with the mapping from expressions in that fragment into the capability core. A conforming verifier MUST reject an expression outside the declared profile. A profile is published and SAID-addressed like any other namespace content, so the profile itself carries the pinned semantics for that language. Languages used through conforming profiles are therefore interchangeable *syntaxes over one semantics*: they interoperate, rather than merely each being well-defined on its own.

> **Editor's note (scope of vocabularies):** This specification fixes the **value-lattice kinds** (the meet machinery) but deliberately does **not** enumerate the concrete resource schemes, ability vocabularies, limitation-types, or obligation-types. These are *content*, supplied by namespaces at whatever altitude fits — owner-local, bilateral, community, or global standard — all coexisting and SAID-addressed. Keeping the protocol content-free is what makes it general-purpose. Nor does it enumerate expression languages: it fixes the value-lattice kinds and the profile mechanism, and leaves the choice of syntax open. A small **baseline namespace** of near-universal types (e.g. a time-window limitation, generic `report`/`notify` obligations) MAY be published separately as a companion document for out-of-the-box interoperability; it is not part of the core protocol. Existing capability and chained-authorization patterns in the KERI/ACDC community should be reviewed before fixing any such baseline.

### Obligations

An obligation is a duty the Delegate owes, which may come due in the future (see the time test above). Obligations form a set ordered by ⊇ (more obligations = less permissive), with meet = union and ⊤ = the empty set; they accumulate down a chain and are enforced by the accountability layer, never by the authorization decision.

#### Anatomy of an obligation

| field | meaning | default |
|---|---|---|
| **type** (`t`) | the kind of obligation, from a registry obligation-type (`report`, `notify`, `pay`, `delete-after-use`, `log`, …) | — |
| **obligor** | who owes it | the Issuee (the Delegate) |
| **beneficiary** (`to`) | to whom it is owed | — |
| **trigger** (`on`) | the antecedent that makes it due | — |
| **deadline** (`by_when` / `within`) | by when, after the trigger | — |
| **params** | type-specific content (for `report`: what to report) | — |
| **discharge** | what counts as fulfilment (see below) | a signed payload |
| **rendering** (`l`) | the human-readable rendering, SAID-committed | from the type's template |

The **obligation-type** lives in the registry, declaring its parameter schema, trigger semantics, what constitutes a valid discharge, and a rendering template; the **obligation instance** lives in the rule section `r`, binding that type to concrete parameters.

#### Conditional obligations

Obligations are rarely absolute; they are conditionals of the form

```
WHEN <antecedent>  THEN  <obligation>  BY <deadline relative to the antecedent>
```

The `trigger` field *is* the antecedent. The protocol does not evaluate whether the antecedent holds; it makes both the antecedent and the discharge into **verifiable artifacts that link by SAID**, so that compliance becomes a matching check over the evidence trail rather than a deontic evaluation:

- the **antecedent artifact** (e.g. the booking record, itself a TSP Signed Payload) proves the obligation is now due;
- the **discharge artifact** (e.g. the report) proves it was met, and MUST reference both the obligation clause's SAID and the triggering artifact's SAID.

A trigger is one of the following kinds:

```
on:
  exercise: <ability>     # fires when this authority is exercised
  clause:   <SAID>        # fires when another clause/obligation is discharged (obligation-to-obligation dependency)
  event:    <event-type>  # fires on an externally-attested event (referenced by evidence)
  time:     <schedule>    # periodic or absolute
  standing                # continuously in force
```

`exercise:` captures the "gated to having done it" case directly: an obligation gated on exercise creates **no** duty if the authority is never used (an unexercised authority discharges its exercise-gated obligations vacuously). `clause:` lets obligations depend on one another, forming a dependency graph linked by SAID; the protocol records this structure but does not reason over it. Event/exercise/clause-triggered obligations verify by event-matching; a `standing` obligation has no discrete antecedent and is verified by **periodic attestation** on a `time:` schedule.

#### Discharge and non-performance

Discharge is a TSP Signed Payload, tied to the obligor's AVID, that references the obligation clause's SAID and (for event/exercise/clause triggers) the triggering artifact's SAID. Non-performance never retroactively invalidates authority that was already exercised; it leaves a verifiable gap in the accountability trace and, where the delegation is revocable, MAY trigger revocation for future exercises. Enforcement is by evidence and revocation, never by recomputation of the capability meet.

#### Consistency is the accepting party's responsibility

There is a fundamental asymmetry between limitations and obligations:

- **Limitations always compose into something satisfiable.** The meet only tightens; the worst case is ⊥ (no permission), which is still a valid state.
- **Obligations compose by union, and a union can be unsatisfiable.** Two obligations may directly contradict ("delete within 24h" vs. "retain for 30 days"), or they may **deadlock** (each gated on the other via `clause:`). A deadlock is simply another form of an unsatisfiable obligation set.

The protocol does not attempt to prevent either. Detecting a contradiction or a cycle requires semantic judgment and, in general, knowledge of the whole obligation graph — which a party may not have, since a `clause:` trigger can reference a clause in another chain. Mandating such a check would therefore be a requirement parties are sometimes structurally unable to satisfy. Accordingly:

1. Union MUST always compose structurally; an unsatisfiable obligation set is **not** an authorization error.
2. The only structural invariant on a `clause:` trigger is that the reference be a well-formed SAID; the protocol does not require that it resolve, that the graph be acyclic, or that the set be satisfiable.
3. A party MAY screen the obligations it can see before accepting a delegation, and SHOULD do so, but completeness is not required.
4. An unsatisfiable obligation set surfaces as an unavoidable breach in the accountability layer, borne by whoever accepted it — exactly as a party who signs a self-contradictory contract is the one in default.

In short: the protocol ensures obligations are *explicit, accumulating, and verifiable*; it does not ensure they are *jointly satisfiable*, and parties accept obligation sets at their own responsibility.

### The Closed Capability Core and Interpreted Policy

Stated policy is either **computed** — evaluated mechanically against the closed capability core — or **interpreted** — read and judged by a capable party. This is an attribute of each item, not a separate layer: a limitation may be computed (`amount ≤ 500 USD`) or interpreted ("only vendors we have a relationship with"), and so may an obligation ("report within 24h" or "reported adequately").

**Interpreted policy is expected to be the principal avenue through which parties express what they actually mean; the computed core is the security and safety floor beneath it.** Most real-world authority — "consistent with my stated travel preferences," "nothing that would embarrass the firm" — is the stuff of ordinary, interpretable, legally enforceable speech, not of a formal lattice. The core exists not to express all policy but to make the parts that bear on security and safety *mechanically provable*.

1. This specification defines a **closed capability core**: the resource/ability/limitation model above, whose partial order and meet are total, deterministic, and decidable against the published namespaces. A conforming verifier MUST compute the effective capability of a delegation chain by reduction — taking the meet along the chain (and the union of obligations along the chain) — using only the closed-core semantics. This reduction requires no general policy engine and yields the same result for every conforming verifier. This is the security/safety floor.
2. An implementation MAY — and in practice typically will — carry **interpreted policy**: an interpreted limitation among the limitations, or an interpreted obligation or assumption in the rule section. It is carried in band, signed under the issuer's AVID and committed by SAID. Its content MAY be prose, other text, or any medium a capable party can interpret; the container commits to arbitrary bytes and carries a content type.

**Why interpretation cannot escalate.** An interpreted limitation is still a limitation, and limitations only narrow. No reading of one — however mistaken, or however adversarial the content that prompted it — can grant authority the computed core did not; the worst a misreading can do is fail to narrow. Safety here follows from what a limitation *is*, not from evaluation order and not from the language it is written in. The same holds for an interpreted obligation or assumption, neither of which enters the authorization decision at all.

#### The nature of interpreted policy: attributable speech

Interpreted policy carried this way is **attributable speech**, and its attribution is structural: it is signed under the issuer's AVID and committed by SAID, so authorship is known and the text is tamper-evident and non-repudiable — exactly the properties that make a written contract clause enforceable. It may be less formally exact than a closed-core bound, but it is interpretable in the way agreements are interpreted in daily life, and it binds to the [Accountability](#accountability) layer on the same footing, with the single accepted cost of possible interpretation gaps.

That cost lands as a *dispute, not a vulnerability*. Because an interpreted limitation can only narrow, an interpretation difference can never cause an escalation — only a disagreement about whether to deny. Resolution has a clean two-time split:

- **At exercise**, the party making the access decision interprets the prose reasonably and decides; deny-only keeps this safe regardless of how it reads.
- **At accountability**, the signed prose is the evidence, and any dispute over its meaning resolves in the accountability layer — ultimately by humans, under a reasonable-interpretation standard, as contract ambiguity has always been resolved.

The binding act is **acceptance**: when a Delegate Acks a delegation carrying prose, it accepts liability to act consistently with that prose *as a reasonable party would read it*, not under an adversarial reading. The intelligent agent assumption supplies the capacity for reasonable interpretation; acceptance supplies the liability.

An interpreted limitation and an interpreted obligation are therefore the **same kind of content** — signed, committed, read by a capable party, enforced through accountability — differing only in *when* they are read: a limitation is read at exercise and gates the act; an obligation is read when it comes due. No separate machinery is needed for interpreted policy; it is the attribute and rule sections carrying content that happens to be judged rather than computed.

Where evaluation is discretionary (prose / prompt) rather than deterministic, the evaluating party SHOULD emit a **signed decision record** tied to its AVID — what policy it evaluated, against what request, and its conclusion — so that a judgment that cannot be *reproduced* can still be *attributed*.

**Guideline — formalize the floor, prose the rest.** Computed and interpreted policy are not either/or. A delegator SHOULD place anything bearing on security or safety, or anything requiring deterministic agreement between independent verifiers, in the closed core, and use prose only for what genuinely requires judgment. This confines interpretation variance to exactly the judgmental residue — where human-style interpretation is wanted anyway — while keeping the safety-critical part mechanically provable.

> **Non-normative note (expression languages).** Established analyzable policy languages — **Cedar**, **Biscuit**, OPA/Rego, Datalog and others — are reasonable candidates for expressing computed limitations, used through a conforming profile as described under [Resources, Abilities, and Limitations](#resources-abilities-and-limitations). This specification commits to none of them, and the names here are illustrative rather than a recommendation. Note what a profile does and does not supply: such a language contributes *syntax and tooling*, never semantics. A general policy engine evaluates a flat, typically centrally-administered policy set and re-checks attenuation on each exercise; the closed core instead makes attenuation *intrinsic* to a chained delegation and supports a soundness result. Using a language through a profile therefore lets an implementation keep its existing parsers, editors and test tooling while the meet remains the specification's.

3. **Soundness property.** For any delegation chain, the closed capability core guarantees that no node's effective capability can exceed the capability of its root or roots, and no node's effective obligation set can be smaller than the union imposed along its chain — *regardless of any interpreted policy carried alongside it*, since an interpreted limitation can only narrow further. This property is intended to be stated and proven as part of [Security and Trust Considerations](#security-and-trust-considerations).
4. **Expressiveness.** Soundness bounds what a chain may express relative to its root; it is not a claim about what can be said. Separately, this specification is **complete relative to the supplied vocabulary**: any policy decidable at the gate can be expressed as a capability, because the vocabulary it ranges over is namespace-supplied and unrestricted by this specification. Anything unexpressible is unexpressed by an author's choice, never excluded by the framework.

## Invocation Exchange 

Authority is created in a Delegation Exchange and used in an Invocation Exchange. Both are instances of the Authenticated Exchange Protocol. The Invocation Exchange is the entire authorization lifecycle between an agent's - or the holder's - VID and the service's VID: it opens with presentation and verification, and continues with notices, denials, and disputes for as long as the authority is in use.

### Presentation and Verification

Before exercising delegated authority against a service, the holder MUST present the governing capability chain to the service and the service MUST verify it. Presentation is the opening phase of an Invocation Exchange.

The presentation MUST carry the chain as a TSP Signed Payload tied to the holder's AVID.

The service MUST verify: chain well-formedness and I2I linkage; rooting in issuance it accepts as authoritative for the resource; leaf Issuee VID equal to the TSP sender VID; validity window of every link, and non-revocation of every link that carries a revocation registry; and a non-empty effective capability for the stated purpose. Verification failures are governed by fail-closed (Key management and freshness).

Upon successful verification the authorization is bound to the holder's VID. Thereafter the TSP-authenticated sender VID is the only per-request authorization material. The scope and lifetime of the binding are those of the presented capability itself; no separate session object is created.

For each exercise the service MUST ensure, at time of exercise, that the operation is within the effective capability and that the chain remains valid (Delegation Exchange, Requirement 4). How the service refreshes its view is an implementation choice within the freshness rules.

The holder SHOULD screen its own requests against the capability it holds. Both parties hold the same signed contract; divergent evaluations are disputes, not protocol errors.

## Continuing Exchange

Subsequent authorization events between the same parties are messages of the same exchange:

 - denial — a specific exercise was refused. Conditions: no-binding, exceeds-capability, stale, declined.
 - notice — an unsolicited state change, e.g. revocation or expiry of a bound chain.
 - re-presentation — a fresh chain presented per (1)–(4), replacing a stale binding.
 - dispute — a party contests a prior denial or exercise.

Correlation is by SAID. A denial MUST reference the refused exercise by the SAID of that message; a notice MUST reference the chain SAID; a dispute MUST reference the SAID of the denial or exercise contested. Any payload carried over TSP is SAID-addressable whether or not it is signed.

Denials with condition exceeds-capability or declined, and all disputes, MUST be TSP Signed Payloads tied to the sender's AVID. Other denials and notices MAY be.

A denial MUST carry its condition and its SAID reference. It MAY carry a what-would-suffice hint, including the roots of issuance the service accepts; the issuer of acceptable authority is not presumed to be the service. Disclosure beyond the required slots is service policy.

Error-driven bootstrap is not the design intent of the Invocation Exchange. The normal flow as defined in this section SHOULD be followed. However, a no-binding denial is a correct response to an agent that did not present its capability ahead of time.

A declined denial is not required to carry a reason: discretion above a passing verification needs no justification at the protocol level. It is, however, signed (Req. 9) and therefore attributable and disputable. Whether declining breaches the service's own duties is a matter of the obligations in the delegation that created the holder's authority, and is adjudicated through Accountability. A party that requires a duty to serve MUST express it as an obligation in the governing Delegation Exchange.

## Accountability

Authorization and accountability are distinct functions served by the same structure. A chain of ACDCs, read from root to leaf, expresses the delegation and provenance of authority; read from leaf to root, the same chain is the accountability trace. A TEA is therefore a single unit of authorization *and* accountability because both derive from one cryptographic structure.
 
Accountability is retrospective and evidentiary: it answers who agreed to or did what, and whether it can be proven after the fact. The ACDC chain provides accountability directly — each container is signed, chained, and non-repudiable — and, when carried as a TSP Signed Payload tied to the AVID, it can be presented to and verified by a third party that did not observe the original interaction.
 
Requirements:
 
1. An authorization or agreement that must be accountable to a third party MUST be recorded as a TSP Signed Payload (see [TSP Signed Payload](#tsp-signed-payload)) tied to the responsible party's AVID.
2. The accountability chain of any authority a TEA holds or exercises MUST terminate in a principal — a natural person or organization — that can bear accountability. A TEA MUST NOT be the terminal responsible party for an authority.
3. An authorization MUST NOT be inferred solely from a record that an agreement occurred; conferral of authority requires an authorization ACDC as defined in [Delegation Exchange](#delegation-exchange).

## Integration with AI Agent Protocols

TEA integrates with agent protocols such as MCP and A2A at the interfaces those protocols already define, leaving their messages unchanged.

TEA is a family of exchanges between different party pairs. An agent uses the Delegation Exchange to obtain one or more capabilities from one or more parties who have relevant authorities. This exchange is similar in its role to a party interacting with an Authorization Service (AS) via OAuth, but has significantly different trust framework in TEA. An agent uses the Invocation Exchange to access a service by presenting the capability or capabilities that are previously delegated to it. This echange is similar in its role to a party accessing a web service via HTTPS with an anthorization token, but again has significantly different trust framework in TEA.

The host protocol and the Invocation Exchange are sibling channels over the same TSP relationship: host-protocol payloads and TEA payloads are multiplexed over one authenticated relationship between the same two VIDs. Neither protocol carries the other. All authorization traffic — presentation, denials, notices, disputes — is Invocation Exchange traffic; host-protocol messages carry no authorization content in either direction, and are referenceable from the Invocation Exchange by SAID.

Issuer and verifier are roles, not deployment prescriptions: the issuer of a root capability and the service that verifies chains rooted in it may be one entity or separate. The issuer participates through its signatures in the chain, never through presence in the exchange.

### Integration with MCP
TODO

### Integration with A2A
TODO

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
    resource: bookingservice:account/alice
    ability:  [ create-booking, cancel-booking, view ]
  r:  { account terms ... }
  rd: <S's registry SAID>
  (no authority edge — S is the root of authority over the resource)
```

**Hop 2 — `Cred_Alice→Agent` (I2I re-delegation, attenuated).** Alice re-delegates a narrowed slice to her Agent. The grant is expressed as a *restriction* relative to the parent: a smaller ability set, tighter limitations, and an obligation conditional on exercise.

```
Cred_Alice→Agent
  i:  AVID_Alice                     # Issuer  = Alice
  a:                                 # capability
    i:  AVID_Agent                   # Issuee  = the Agent
    resource: bookingservice:account/alice
    ability:  [ create-booking ]     # attenuated: subset of Alice's abilities
    limitations:
      maxAmount: 500 USD             # bounded scalar  (meet = min)
      category:  [ flights ]         # membership set  (meet = ∩)
      validUntil: <now + 7 days>     # bounded scalar  (meet = min): earliest expiry wins
  r:                                 # post-gate policy
    obligations:
      - t:  report
        to: AVID_Alice
        on: { exercise: create-booking }   # antecedent: a booking was made
        within: 24h
        what: { booking-id, amount, vendor }
        d:  <SAID of this clause>
        l:  "The Agent shall report each booking to Alice within 24 hours."
  e:
    auth:
      n: <SAID of Cred_S→Alice>      # far node = the parent authority
      o: I2I                         # Issuer(this) MUST be Issuee(parent): Alice = Alice  ✓
  rd: <Alice's registry SAID>
```

### Effective capability (the meet)

The Agent's effective capability is the meet (∩) along the chain:

```
  Alice's capability    : { create-booking, cancel-booking, view } on alice's account
  ∩ Alice→Agent delta   : ability ⊆ { create-booking }; amount ≤ $500; category = flights; within 7 days
  = Agent's capability   : create-booking on alice's account, ≤ $500, flights only, until T+7d
```

This is strictly ≤ Alice's capability, which is ≤ S's grant — the narrowing is intrinsic because each hop carries only a restriction. A request to book a $420 flight falls inside this set and is permitted; a $900 booking, a hotel, or a request after day 7 would not.

The effective obligations are the union along the chain: `{ report each booking to Alice within 24h }`. Making the booking triggers the `report` obligation, which the Agent discharges by a signed payload referencing the obligation's SAID and the booking record; had the Agent never booked, the obligation would discharge vacuously.

### Runtime

<figure id="delegated-access-example">

<img src="https://raw.githubusercontent.com/trustoverip/aimwg-tsp-enabled-ai-agent-protocols/main/assets/tea-delegated-access-example.png" alt="Delegated service access: authority chain S to Alice to Agent" width="760">

<figcaption>Figure 5: Delegated service access — authority chain S → Alice → Agent</figcaption>

</figure>

&nbsp;

1. **Delegation.** Over their TSP channel, Alice and the Agent run the [Authenticated Exchange Protocol](#authenticated-exchange-protocol) to settle the terms; Alice's binding Ack issues `Cred_Alice→Agent` to the Agent in band.
2. **Access.** The Agent opens an exchange with S, proposing a concrete booking (a $420 flight) and presenting the chain `{ Cred_Alice→Agent ▸ Cred_S→Alice }` as a TSP Signed Payload (see [TSP Signed Payload](#tsp-signed-payload)) tied to `AVID_Agent`.
3. **Verification.** S confirms the chain roots in its own issuance (`AVID_S`); that the I2I edge holds (Alice is the Issuee of the authority she re-delegated); that the leaf Issuee is the TSP counterparty presenting it; that the meet of the chain covers the requested operation; and that no ACDC on the chain is revoked or past its `validUntil`. S then Accepts/Acks and creates the booking.
4. **Obligation.** The Agent discharges its obligation by reporting the booking to Alice as a signed payload.

### What the example demonstrates

- **The service is both the root and the relying party.** S validates a chain rooted in its own grant, so no external anchor is needed — the base "root issuance" case lands naturally on the resource owner, and the normative text needs no special "root principal" carve-out beyond "the chain roots in the authority that controls the resource."
- **Re-delegation is a uniform I2I hop**, and attenuation is intrinsic: the Agent cannot exceed what Alice holds, which cannot exceed what S granted.
- **One chain, two readings.** Read S → Agent, it is the delegation of authority; read Agent → S, it is the accountability trace, terminating in Alice — a principal, never the Agent.

## Design Rationale and Comparison
 
*This section is non-normative. It situates the design choices above against commonly used schemes for the benefit of reviewers and implementers; it imposes no requirements.*
 
### Negotiation and binding
 
The Authenticated Exchange's binding model is, in effect, a peer-to-peer two-phase commit: the offering party's Ack is the commit decision. Unlike classical two-phase commit it needs no coordinator and does not block on a failed coordinator, because the `validUntil` bounds turn an unresponsive counterparty into a clean lapse rather than an indefinite hang — the same hardening production systems add to two-phase commit through presumptive-abort timeouts.

Race resolution and the inconsistency window. The asymmetry between Withdraw and Ack is the same asymmetry two-phase commit draws between a participant's vote and the coordinator's decision. An Accept is a vote-commit: once cast, the accepting party honors it until its `validUntil` lapses, and cannot unilaterally abort a decision the offering party may still make within that window. The offering party's Ack is the commit decision, and it is supreme — a Withdraw is effective only in the absence of a valid Ack, so a Withdraw that races a binding Ack simply loses. This is what makes the resolution deterministic without a coordinator and without comparing the two parties' self-signed timestamps against each other: binding turns solely on whether a valid Ack exists within the window, a question with one answer that an adversary cannot tilt by backdating. The price is that the accepting party cannot be certain a late Withdraw will land; it may lose to an Ack already in flight. That uncertainty is exactly the residual bounded inconsistency window noted below — it is bounded by the `validUntil` the accepting party itself chose, and no coordinator-free commit can eliminate it.
 
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
 
The distinctive position of the Authenticated Exchange is that it is the only one of these combining decentralized peer operation (no coordinator, certificate authority, venue, or ledger), symmetric exposure bounding, and a portable non-repudiable record verifiable after the fact. The honest costs are an extra round-trip, a residual bounded inconsistency window that no timeout-based commit can fully eliminate — the window in which an accepting party's Withdraw may lose to a racing Ack, bounded by its own `validUntil` — and a remaining clock dependence that affirmative binding reduces but does not remove.
 
### Delegation and attenuation
 
The capability model draws on established authorization-capability designs. *(What this specification calls a* **limitation** *is the device the capability-security literature calls a* **caveat***; this subsection uses each tradition's own term.)* SPKI/SDSI is the formal precedent for computing effective authority as the *intersection* of authorizations along a delegation chain (tag intersection / certificate-chain reduction); the closed-core "meet along the chain" rule is the same idea over ACDC edges. Macaroons achieve strict attenuation structurally by making caveats append-only — a restriction can be added but not removed — which is the model for the restriction-only re-delegation encoding. UCAN provides decentralized, DID-native capability chains with an attenuation-checked subsumption order, and ZCAP-LD is the linked-data cousin in which caveats accumulate down the chain.

The closest ACDC-native precedent is the GLEIF vLEI credential chain (a role credential chained to a Legal Entity credential chained to a Qualified vLEI Issuer credential), itself a delegation-of-authority chain expressed entirely through ACDC edges with the I2I operator. The principal way this specification goes beyond IPEX — the nearest ACDC-native exchange protocol — is that IPEX provides no validity bounds or attenuation semantics at all (its security considerations were never completed), so the bounding and strict-attenuation apparatus here is additive rather than a restatement.
 
The rationale for favouring a closed capability core in this specification is that it is the part whose security can be proven: a capability structure with a defined meet supports a soundness theorem — no chain of delegations can yield authority exceeding its root — which is a materially stronger guarantee than re-checking attenuation with a general policy engine on every exercise. Interpreted policy remains available for what the closed core cannot express, on the understanding that it trades the structural guarantee for a judgment.

## Security and Trust Considerations


### Security model and assumptions

This section analyzes the security of the delegation, obligation, and exchange mechanisms defined above. It rests on a small number of explicit assumptions and is written against a stated set of adversaries.

**Assumptions.**

- **Intelligent agents.** TEA agents are capable and evaluate what they accept; the protocol supplies a common language and channel, not a reasoning engine (see [The intelligent agent assumption](#the-intelligent-agent-assumption)). The protocol does not protect a node from its own bad acceptance decisions.
- **Controller integrity is the trust boundary.** The guarantees below assume the Controller and its Wallet are intact; their compromise is full TEA compromise and is treated as the boundary condition, not a case the inner mechanisms defend against.
- **VID resolution and key state are verifiable.** Verifiers can resolve counterparties' VIDs and their key/rotation history (e.g. via **did:webvh**) and reach credential status registries — with the explicit fail-closed rule when they cannot (see [Key management and freshness](#key-management-and-freshness)).
- **Trust roots are chosen by the relying party.** Soundness bounds authority relative to a root; which roots to trust is out of scope (sound ≠ trusted).

**Adversaries considered.** A manipulated or fully compromised AI *model* within a TEA; a malicious *counterparty* TEA (issuing abusive obligations, presenting forged or stolen chains, racing Withdraws against Acks); a *network adversary* (observing, blocking, replaying); *key compromise* of a VID; and a *thief* in possession of credential bytes. Compromise of a TEA's own Controller/Wallet is out of scope as noted above.

The guarantees are organized as: the structural core (soundness and model containment), the binding of authority to identity (holder binding), the freshness and key-management discipline, the residual risks of the open-policy/obligation/exchange mechanisms, privacy considerations, and a consolidated list of residual limitations.

### Soundness of the delegation model

The closed capability core provides two guarantees that together are the security foundation of delegation: **authority never escalates** as it is delegated, and **obligations never disappear**. Stated plainly:

- No TEA, anywhere in a delegation chain, can hold more authority than the root that originated it granted.
- No TEA can hold fewer obligations than the chain leading to it imposed.

Both are properties of *construction*, not of after-the-fact checking, and that distinction is what makes them strong.

**Why authority cannot escalate.** A re-delegation never restates a capability from scratch; it carries only a *restriction* — a narrowing applied to the authority it inherited. A verifier computes a node's effective capability as the *meet* (the intersection) of the parent's authority with that restriction. Because a meet can only be as permissive as each of its inputs, every hop is necessarily at most as permissive as the one above it; following the chain up to its root, no node can exceed the root's grant. The narrowing is therefore intrinsic: it happens because of how effective authority is *computed*, not because someone audits each step.

**Robustness to a dishonest delta.** A useful consequence is that the encoding cannot be gamed by overstating a restriction. Suppose a re-delegating TEA writes a restriction naming more than it holds — claiming, say, every ability rather than the subset it was granted. It gains nothing: the meet with its parent discards anything the parent did not have, so the overstated claim collapses back to the parent's authority. Escalation by writing a generous delta is simply not expressible. The dual holds for obligations — omitting an inherited obligation from a delta cannot remove it, because the effective obligation set is the union along the chain.

**Why the chain itself is trustworthy.** Algebra alone bounds authority only if the chain is genuine: a TEA must not be able to graft its delegation onto authority it was never given. This is enforced by the I2I edge — a delegation is valid only when its issuer is the party to whom the parent authority was issued. So each hop provably descends from authority the issuer actually held, and the "meet with the parent" is a meet with the issuer's *own* received authority, never someone else's. Provenance (I2I) and narrowing (the meet) together give the guarantee.

**Composition from several sources (AND).** When an authority draws on more than one source under an AND-group, its effective authority is the meet across *all* of them. It is therefore at most as permissive as each source, and the no-escalation property holds against every one of its roots simultaneously; its obligations are the union across all sources.

**Composition under alternatives (OR).** Where any one of several sources suffices, there is no single combined authority; each alternative is evaluated on its own. A request is permitted if it lies within the authority of *some* currently-valid source. Because each alternative is independently a sound chain bounded by its own root, no alternative can escalate — and therefore neither can their disjunction: whichever source is invoked, the exercise is bounded by that source's root, and the obligations owed are those accumulated along that source's chain.

**Interpretation cannot breach the floor.** An interpreted limitation is still a limitation, and limitations only narrow. It removes requests from the authorized set; it never adds any. So the ceiling established by the computed core holds regardless of what is written alongside it: no expressive policy, and no manipulation of one, can grant authority the chain did not.

**What soundness does and does not promise.** The guarantee is precise, and so are its limits:

- *Sound is not the same as trusted.* Soundness says a node's authority never exceeds its root's; it does not say the root is one you should believe. Deciding which roots to trust is the relying party's responsibility and is out of scope.
- *It is conditional on verification.* The guarantee holds only for well-formed chains — every link's I2I edge checked, every signature valid, nothing revoked, everything within its validity window at the time of exercise. A verifier that omits these checks is owed nothing.
- *It bounds authority, not behavior.* Soundness limits what a credential can express. Limiting what an agent can *do* within that envelope — even when its reasoning is manipulated — is the separate property of model containment (see B.2).
- *For obligations, it bounds accumulation, not fulfilment.* The chain determines which obligations are owed; whether they are discharged is a matter for the accountability layer.
- *It bounds what can be exceeded, not what can be said.* Soundness is not a completeness claim. Separately, the model is **complete relative to the supplied vocabulary**: anything decidable at the gate can be expressed as a capability, since the vocabulary is namespace-supplied and unrestricted here. What cannot be expressed is what an author chose not to say.

These guarantees are stated over the capability lattice rather than over any syntax, so they transfer unchanged to any expression language admitted through a conforming profile.

A formal statement of both theorems, with proofs, is given in [Appendix: Soundness](#appendix-soundness-formal-statement-and-proofs).

### Model containment

**A manipulated, jailbroken, or fully compromised AI model cannot act beyond its delegated, attenuated capability. The blast radius of a duped model is exactly its bounded capability envelope — no more.** This is the central security property of the TEA architecture for AI agents, and it is what permits expressive, even untrusted, intelligence to operate safely inside a TEA.

The property follows directly from the architecture. The model never touches the outside world directly: all of its context, memory access, and tool use go through the Controller, and the Controller checks every exercise of authority against the effective capability computed for the chain (the ceiling established by [Soundness](#soundness-of-the-delegation-model)). Authority is therefore enforced *outside* the model, at the Controller, against a cryptographically bounded capability. Whatever the model "decides" — under a clever jailbreak, a poisoned tool result, a confused reasoning chain, or outright adversarial control — it cannot cause an action the Controller would not have permitted on the basis of the credential alone.

This is a deliberate inversion of where trust is placed: **the design does not ask you to trust the model's reasoning; it bounds the model's authority.** The model's intelligence is harnessed for *what to do within the bounds*, never for *whether something is allowed*. The two core guarantees compose precisely along this line:

- **Soundness** bounds what a *credential* can express: a node's authority never exceeds its root's grant.
- **Containment** bounds what an *agent* can do: its behavior never exceeds the credential the Controller holds.

Together they form an unbroken chain — root grant → (soundness) effective capability → (containment) actual behavior — so an agent's real-world reach is provably no larger than what some accountable principal deliberately conferred.

Attenuation is what makes this guarantee *tight* rather than merely finite. Because capabilities are delegated least-privilege — narrowed at every hop to exactly what the task needs — the bounded envelope under full model compromise is *small*, not just non-infinite. The value of attenuation is realized precisely in the worst case: a thoroughly compromised agent that holds only a narrow capability can do only narrow harm. And whatever it does within those bounds is still non-repudiably recorded and still accrues its obligations (see [Accountability](#accountability)), so even contained misbehavior is attributable after the fact.

The honest premise is that **the trust boundary is the Controller, not the model.** Containment holds while the Controller is intact; a compromise of the Controller itself (and thus the Wallet and TSP Gateway) is a different and higher bar, which is why this specification requires the Controller to be a secured, distinct domain of control with sole access to the Wallet and rigorously secured non-TSP channels (see [TEAs](#teas)). Containment converts the problem of trusting an unbounded, foolable intelligence into the much smaller problem of protecting a well-defined enforcement boundary.

#### Identity authentication contains a subclass of prompt injection

Containment bounds the *consequences* of prompt injection regardless of how the injection arrives. Identity authentication additionally **prevents and exposes an important subclass** of it: injection delivered agent-to-agent over the authenticated TSP channel.

Because every TSP message is mutually authenticated to a sender VID, content that arrives over TSP is never anonymous — it is attributed to a specific, authenticated counterparty with which a relationship exists. An attacker who wishes to inject instructions through this channel must therefore:

1. **first defeat the authentication system** — hold a valid VID and establish a relationship — rather than injecting anonymously; this alone raises the bar substantially and removes the cheap, untraceable injection vector; and
2. **leave non-repudiable evidence** — the injection attempt is signed and attributable to the attacker's VID, so it is detectable and the attacker is accountable, enabling response (revoking the relationship, blocklisting the VID, pursuing accountability through the [Accountability](#accountability) layer).

In other words, identity authentication converts *anonymous, deniable* injection into *authenticated, non-repudiable* injection — a fundamentally weaker position for an attacker, who can no longer hit and run.

This protection covers the agent-to-agent subclass; injection carried in *unauthenticated* content the agent processes (a fetched web page, a document, a tool result from a non-TEA source) is not prevented at the identity layer. Such content remains **contained** by model containment (it cannot escalate authority) and, once acted upon over TSP, **attributable**. Moving more of an agent's inputs onto authenticated TSP channels — including TSP-enabling external services where practical — correspondingly shrinks the anonymous-injection surface.

The layers are complementary: identity authentication raises the bar and provides observability for the agent-to-agent subclass; model containment is the backstop that bounds *all* injection, authenticated or not; and accountability is the record that makes contained misbehavior attributable. None of these claims to *solve* prompt injection — rather, they bound its blast radius, raise its cost, and remove its anonymity.

### Holder binding: capabilities are not bearer tokens

**A capability is bound to the VID of its Issuee. Possession of the credential confers nothing — only the controller of the named VID can exercise it.** This is the property that distinguishes a delegated capability from a bearer token, whose danger is that whoever holds the bytes holds the power.

**The binding loop is closed by TSP.** A delegation names its Issuee VID in the attribute section (`a.i`); the authority is conferred on *that VID*, not on whoever possesses the container. To exercise it, the holder presents the chain over a TSP channel — and TSP mutually authenticates both endpoints by VID. So the relying party already knows, *independently of the credential*, that its counterparty is a particular VID. Verification then closes the loop with a single check: **leaf Issuee VID = TSP-authenticated counterparty VID.** When they match, the presenter has proven control of the VID the authority was issued to. No separate holder-binding or key-binding proof is required — where systems like SD-JWT (key binding) or OAuth (DPoP) must add a dedicated proof-of-possession step, TSP supplies the holder proof inline as a property of the channel.

**Consequence — a stolen credential is useless.** An attacker can copy the ACDC bytes; they are signed but not secret. But the attacker cannot authenticate as the Issuee VID over TSP without that VID's keys, so presenting a copied chain fails the leaf-Issuee-equals-counterparty check and yields nothing. The defining failure mode of bearer tokens — theft of the token is theft of the authority — is absent by construction.

**The whole chain is identity-bound, end to end.** There is no bearer step anywhere: the root issues to a VID; every re-delegation's I2I edge ties `Issuer = Issuee(parent)`, so each hop is controlled by the correct VID; and the leaf is bound to the presenter by the TSP loop above. From root issuance to final exercise, authority only ever moves between authenticated VIDs.

**Confused-deputy resistance.** Because authority is tied to a specific VID and exercised by that VID, an attacker cannot induce an intermediary to wield its authority on the attacker's behalf. For the attacker to gain anything, the intermediary would have to *delegate* to the attacker's VID — producing a credential that is itself bound and attributable to the attacker. There is no ambient authority to confuse.

**The replay loop is also closed by TSP.** TSP provides per-message authenticity, integrity, confidentiality, and ordering. A passive observer cannot capture and replay a presentation: it cannot read the confidential channel, cannot authenticate as the holder, and cannot forge the VID. Cross-service replay fails for an additional reason — the chain roots in a specific resource owner and will not verify against a different one. So holder binding and TSP channel security together close the replay loops that bearer-token systems must patch with nonces or proof-of-possession tokens.

> **Note (no loops in the authority graph).** "Loops" in the other sense — cyclic delegation — cannot arise either. Authority edges reference their parent by SAID (a content hash), and nothing can reference a container that does not yet exist, so the delegation graph is acyclic by construction. (This is distinct from obligation `clause:` triggers, which may reference clauses in other chains and were therefore handled separately under [Obligations](#obligations).)

**Honest scope.** Holder binding ties exercise to the legitimate keyholder; it does not defend against a *compromised* holder — one whose VID keys are stolen, or whose Controller is breached. That case collapses to the Controller trust boundary of [Model containment](#model-containment) and to key management below; key rotation and pre-rotation limit the damage of key compromise.

### Key management and freshness

All authority in this system rests on two things staying true at the moment of exercise: that a signature genuinely comes from the claimed VID, and that the credentials on the chain are still live. Two failure surfaces follow — **key compromise** and **stale status** — and the specification addresses each, with a single overriding rule when verification cannot complete: **fail closed.**

#### Key rotation and pre-rotation

The required VID features (key rotation and pre-rotation with a verifiable history; see [Verifiable Identifiers](#verifiable-identifiers-vids)) are not conveniences — they are the defense against key compromise.

- **Rotation** lets a VID change its keys without changing its identity. This is what makes identification *durable*: long-lived delegations remain valid across rotations because identity is the VID, not the key.
- **Pre-rotation** commits the *next* key (as a digest) before it is ever used. Consequently, **compromise of the current signing key does not permit identity takeover**: an attacker holding the current key cannot rotate the VID to a key of their own choosing, because the next key is already committed to one only the legitimate controller holds. Pre-rotation is therefore also the *recovery path* — the controller rotates to the pre-committed key, retiring the compromised one.
- **A verifiable rotation history** (the key event log) makes the whole key-state evolution auditable and lets duplicity — a secretly forked key state — be detected.

#### Verifying against the key state at signing time

Because keys rotate, a signature MUST be evaluated against the key state that was current *when it was signed*, not when it is checked. The signing position in key-state history is fixed either by anchoring the credential in the signer's key event log or, failing that, by the signer's own timestamp evaluated within a defined clock-skew tolerance — consistent with the timestamp rules of the [Authenticated Exchange Protocol](#authenticated-exchange-protocol). This two-sided discipline prevents both hazards: a key compromised *today* does not retroactively invalidate signatures made legitimately *before* it, and an attacker cannot backdate a forgery to a key state that has since been rotated away.

#### Freshness: revocation and validity windows

A credential carries two independent expiry mechanisms, and both MUST be checked **at the time of exercise**, for **every** ACDC on the chain (per [Delegation](#delegation-of-authorization-and-obligation) Req 4):

- a **`validUntil`** time limitation, fixed at issuance — a static ceiling on lifetime; and
- **revocation** — an optional, dynamic signal that the authority was withdrawn early (key compromise, ended relationship, completed task). Revocation is not an event but a state: the issuer sets the transaction state of the ACDC's registry (`rd`) to revoked, anchored in its key-state log, and a verifier reads that current state..

Both exist because they fail differently: `validUntil` bounds exposure even when revocation infrastructure is unreachable, while revocation handles termination that cannot wait for expiry. Verification is fresh on each exercise, not once at receipt.

::: Note 
Note (what revocation entails — informational). Revocation is optional. When adopted, a delegation is issued in indirect mode: it carries a revocation registry (`rd`) — a verifiable, controller-committed, append-only record of the credential's state (issued / revoked), anchored in the issuer's key-state log. To revoke, the issuer records a state change in that log; a verifier reads the current state at the time of exercise. Both sides take on registry work: the issuer publishes and maintains the registry, and a verifier reads current state from a cache it keeps and consults that, not the issuer — so revocation checking is neither a forced phone-home nor a disclosure of usage to the issuer. State propagates out of band and is eventually consistent: a revocation takes effect only after a defined grace window, so its latency is the synchronization interval plus that window. Which log serves as the anchoring substrate is VID-specific — a KEL for KERI AIDs (the TEL registrar/observer pattern), a DID Log for did:webvh (watchers and webhooks) — but the contract is the same. A delegation that adopts no registry is issued in direct mode: it has a single state (issued) and relies entirely on its `validUntil` limitation for termination.
:::

#### Fail closed

**If a verifier cannot confirm non-revocation or the relevant key state, it MUST deny. Unverifiable is treated as unauthorized — never allowed by default.** This rule matters because the attacker's natural move is not to break the freshness check but to *block* it: deny-of-service a revocation registry or the VID resolver so that a revoked credential appears usable. Fail-closed turns that attack into a denial of access rather than a grant of it.

The cost is availability: a registry or resolver outage produces denials. That is the correct trade for an authorization system — deny under uncertainty — but it can be tuned without abandoning the floor. A verifier MAY accept cached status within a bounded staleness window (a defined tolerance), trading a bounded revocation-latency risk for availability; and keeping `validUntil` windows short bounds how much any deployment must lean on revocation at all. Beyond the tolerated staleness, the rule is unconditional: deny.

#### The Controller and Wallet trust boundary

The Wallet holds the VID secrets, and only the Controller may access it. It follows that **a compromise of the Controller (and hence the Wallet) is a compromise of the entire TEA**: the attacker can then sign as the TEA, exercise its capabilities, and issue delegations. This is the boundary that [Model containment](#model-containment) and [Holder binding](#holder-binding-capabilities-are-not-bearer-tokens) both assume intact — containment converts "trust an unbounded intelligence" into "protect this boundary," and this subsection is where that boundary is defended. The specification's requirements that the Wallet be secured, accessible only to the Controller, and that all non-TSP channels be rigorously secured (see [TEAs](#teas)) are precisely these protections; implementations SHOULD hold key material in an HSM or secure enclave.

Even under key compromise, recovery and accountability remain: the controller rotates to its pre-committed key and revokes affected credentials (rotate-and-revoke), which bounds forward damage; and anything the attacker did while holding the key was done under the legitimate VID and is therefore non-repudiably attributable to the TEA through the [Accountability](#accountability) layer — a recorded harm, not a deniable one.

### Interpreted policy and obligation risks

The risks of the interpreted-policy and obligation mechanisms are mostly established where those mechanisms are defined; they are gathered here for the threat reader.

- **Interpretation cannot escalate.** An interpreted limitation is still a limitation and can only narrow (see [The Closed Capability Core and Interpreted Policy](#the-closed-capability-core-and-interpreted-policy)). Prompt injection into carried prose is therefore bounded to a *denial of restriction* — at worst the policy fails to add its intended limit — and can never grant authority beyond the computed ceiling.
- **Discretionary evaluation is non-deterministic.** Interpretation is not reproducible: different parties, or the same party on different occasions, may reach different conclusions. This is acceptable precisely because an interpreted limitation can only narrow — divergence yields inconsistent *denials*, never inconsistent grants above the ceiling — and it is made accountable by the signed decision record (attributable though not reproducible).
- **Interpretation gaps are disputes, not vulnerabilities.** A difference in reading a prose clause resolves as a dispute in the accountability layer under a reasonable-interpretation standard, not as a security hole; the binding act is acceptance.
- **Unsatisfiable obligation sets.** The protocol guarantees obligations are explicit, accumulating, and verifiable — not that they are jointly satisfiable. Contradictions and dependency cycles are the same class of defect and are the accepting party's responsibility; an unsatisfiable set surfaces as an unavoidable breach in accountability, not an authorization error (see [Obligations](#obligations)). The only structural invariant on a `clause:` trigger is that the reference be a well-formed SAID.
- **Obligation non-performance** never retroactively alters authority already exercised; it leaves a verifiable gap in the accountability trace and MAY trigger revocation for future exercises.
- **Abusive or malicious obligations** are not prevented by the protocol. The intelligent agent assumption places the burden on the delegate to evaluate obligations before accepting; acceptance is the assumption of liability. The protocol does not protect a node from its own acceptance decisions.

### Authenticated Exchange security

- **No party authentication is performed or needed.** TSP has already mutually authenticated the two VIDs; the exchange concerns only the *content* of the negotiation (see [Authenticated Exchange Protocol](#authenticated-exchange-protocol)).
- **Race resolution is deterministic and coordinator-free.** Binding turns solely on the existence of a valid Ack within the Accept's `validUntil`; a Withdraw is effective only in the absence of such an Ack, so a Withdraw racing a binding Ack loses (Ack-supremacy). No inter-party timestamp comparison is performed, so an adversary cannot tilt the outcome by backdating.
- **Bounded inconsistency window.** The residual window in which an accepting party's Withdraw may lose to a racing Ack is inherent to any coordinator-free commit; it is bounded by the `validUntil` the accepting party itself set, and cannot be eliminated, only bounded.
- **Clock dependence.** `validUntil` is evaluated against signers' own timestamps within a defined skew tolerance. Affirmative binding reduces but does not remove clock dependence. A party misstating its clock is constrained by the tolerance and is non-repudiably attributable; out-of-tolerance timestamps MUST be rejected.
- **Exposure is always bounded.** Every offer and acceptance carries a `validUntil`, so no party is left open-ended; an unresponsive counterparty produces a clean lapse rather than an indefinite hang. This also limits denial-of-service: a counterparty cannot pin a party in an exposed state indefinitely.
- **Freshness.** Each Accept references the specific Propose it accepts by SAID, and all messages travel over TSP (authenticated, integrity-protected, ordered), so stale or replayed exchange messages are rejected (see also the replay loop under [Holder binding](#holder-binding-capabilities-are-not-bearer-tokens)).

### Privacy and disclosure

- **A TSP Signed Payload is intentionally not receiver-private.** Its purpose is transferable, third-party-verifiable proof, so presenting authorization or accountability content necessarily discloses authorship and content to the verifying third party. This is a deliberate trade against the metadata-privacy that TSP otherwise provides for transport.
- **Disclose the minimum.** A presenter SHOULD present only the portion of the delegation chain required for the decision at hand, avoiding unnecessary exposure of the wider delegation graph.
- **Selective and graduated disclosure.** ACDC's selective-disclosure facilities SHOULD be used to reveal only the attributes, limitations, and obligations a relying party needs, and to reference other nodes by commitment where full content is not required — limiting exposure of sensitive business terms in limitations and obligations to the intended relying party.
- **Correlation and VID role.** Reuse of a single AVID across many relying parties enables correlation of a TEA's activities — an inherent cost of accountability, since the AVID is *meant* to be linkable for that purpose. Where unlinkability matters, a TEA SHOULD use distinct or private VIDs (the specification permits additional private VIDs) for interactions that do not require AVID-tied accountability, trading linkability against privacy per VID role (IVID / AVID / private VIDs).
- **Confidentiality of content in transit.** Presenting over a confidential TSP channel to the specific relying party limits exposure of chain content to the intended party; that party necessarily learns what it must to decide.

### Residual risks and limitations

Collected here, honestly, are the residual risks this design bounds but does not eliminate:

1. **Trust-root choice is external** — soundness guarantees a node never exceeds its root, not that the root is legitimate (sound ≠ trusted).
2. **Bounded inconsistency window** in the Authenticated Exchange — inherent to coordinator-free commit; bounded by `validUntil`.
3. **Residual clock dependence** — reduced by affirmative binding and skew tolerance, not removed.
4. **Containment depends on Controller/Wallet integrity** — the trust boundary; its compromise is full TEA compromise.
5. **Discretionary open-policy evaluation is not reproducible** — attributable only; interpretation gaps become disputes.
6. **Obligation satisfiability is not guaranteed** — the accepting party's responsibility; defects surface in accountability.
7. **Fail-closed trades availability for safety** — status/resolver outages produce denials.
8. **Injection via unauthenticated content is not prevented at the identity layer** — only contained and attributable.
9. **AVID correlation/linkability** — the privacy cost of accountability; mitigated by VID role separation.
10. **A compromised holder within the rotation/revocation window** — bounded by pre-rotation recovery and rotate-and-revoke, with pre-recovery actions remaining attributable harm.

## Appendix: Soundness — formal statement and proofs

### A.1 Setup

Let the capability lattice be `L = R × A × M` (resource × ability × limitation), the product of three bounded meet-semilattices. `L` inherits component-wise a partial order `≤` (reflexive, transitive), a meet `∩` that is a greatest lower bound (`x ∩ y ≤ x` and `x ∩ y ≤ y` for all `x, y`), and a top `⊤` that is the identity for meet (`x ∩ ⊤ = x`).

A delegation graph is a set of authorization-ACDC nodes. Each node `n` carries a local restriction `r(n) ∈ L` (omitted axes = `⊤`) and an obligation delta `d(n)` (a set; omitted = `∅`). A node is a **root** if it has no authority edge.

Effective capability `E(n)` and effective obligation set `D(n)` for the single-parent and AND cases:

```
root ρ:         E(ρ) = r(ρ)                 D(ρ) = d(ρ)
single parent:  E(n) = E(p) ∩ r(n)          D(n) = D(p) ∪ d(n)
AND {p₁..pₖ}:   E(n) = (⋂ᵢ E(pᵢ)) ∩ r(n)   D(n) = (⋃ᵢ D(pᵢ)) ∪ d(n)
```

A graph is **well-formed** if every edge `n → p` satisfies the provenance constraint `Issuer(n) = Issuee(p)` (I2I, or the DI2I variant), and every node on the chain has a valid signature, is non-revoked, and is within its validity window at the time of exercise.

### A.2 Lemma (one-step attenuation / accumulation)

For any non-root node `n` and each parent `p` required to reach `n`'s authority: `E(n) ≤ E(p)` and `D(n) ⊇ D(p)`.

*Proof.* Single parent: `E(n) = E(p) ∩ r(n) ≤ E(p)` since meet is a lower bound; `D(n) = D(p) ∪ d(n) ⊇ D(p)` since union is an upper bound. AND-group: `E(n) ≤ ⋂ᵢ E(pᵢ) ≤ E(pⱼ)` for every `j`, and dually `D(n) ⊇ ⋃ᵢ D(pᵢ) ⊇ D(pⱼ)`. ∎

### A.3 Theorem 1 (Capability Soundness — no escalation)

In a well-formed graph, for every node `n` and every root `ρ` reachable from `n` along required (single/AND) edges, `E(n) ≤ E(ρ) = r(ρ)`.

*Proof.* Induction on the path length from `n` to `ρ`. Base (`n = ρ`): `E(n) = E(ρ)` by reflexivity. Step: by the Lemma `E(n) ≤ E(p)` for the parent `p` on the path, and by the induction hypothesis `E(p) ≤ E(ρ)`; transitivity gives `E(n) ≤ E(ρ)`. Since `E(ρ) = r(ρ)` is exactly the grant the resource owner made at the root, no chain can manufacture authority the owner did not confer. ∎

### A.4 Theorem 2 (Obligation Accumulation — no shedding)

In a well-formed graph, for every node `n` and every root `ρ` on a required path, `D(n) ⊇ D(ρ)`.

*Proof.* Dual to Theorem 1, with `⊇` for `≤` and `∪` for `∩`; union is an upper bound and `⊇` is reflexive and transitive. ∎

### A.5 Corollary (robustness to a dishonest delta)

A node gains nothing by overstating its restriction or omitting inherited obligations. If `r(n)` names more than the parent holds, `E(n) = E(p) ∩ r(n) ≤ E(p)` regardless (the meet discards the excess); if `d(n)` omits a parent obligation, `D(n) = D(p) ∪ d(n) ⊇ D(p)` regardless. Attenuation is thus intrinsic: `E` and `D` are *computed* by meet and union, never read from a node's own claim.

### A.6 Extension (OR-composition)

Under an OR-group `{p₁..pₖ}`, a node has no single effective capability; each alternative is evaluated independently:

```
branch j:  E(n)|ⱼ = E(pⱼ) ∩ r(n)        D(n)|ⱼ = D(pⱼ) ∪ d(n)
```

A request `q` is authorized at `n` iff there exists a currently-valid branch `j` with `q ≤ E(n)|ⱼ`; the obligations owed for that exercise are `D(n)|ⱼ`.

*Claim.* Every admissible exercise is bounded by the root of its branch, and no escalation is introduced by the disjunction.

*Proof.* Fix the branch `j` invoked. The sub-chain through `pⱼ` is itself well-formed, so by Theorem 1 `E(n)|ⱼ = E(pⱼ) ∩ r(n) ≤ E(pⱼ) ≤ E(ρⱼ)` for the root `ρⱼ` of that branch; hence any `q ≤ E(n)|ⱼ` satisfies `q ≤ E(ρⱼ)`. As each disjunct is individually bounded, their disjunction admits no `q` exceeding every root — every admitted `q` is within the root of the branch that admitted it. Dually, by Theorem 2 `D(n)|ⱼ ⊇ D(ρⱼ)`. ∎

### A.7 Interpreted policy preserves soundness

An interpreted limitation `O(n)` is a narrowing filter evaluated at exercise. The authorized set is `{ q : q ≤ E(n) ∧ O(n)(q) } ⊆ { q : q ≤ E(n) }`. Since `O(n)` only removes requests, the upper bound of Theorem 1 holds *a fortiori*; no interpreted policy can broaden authority beyond the computed result.

### A.8 Role of the structural premises

- **I2I provenance** makes well-formedness cryptographically real. The algebra of A.2–A.4 bounds authority only relative to the *declared* parents; `Issuer(n) = Issuee(p)` is what guarantees a node can only build on authority issued to it, so the declared parent is the real one. Without it the theorems are vacuous.
- **Restriction-only encoding** forces `E(n)` to be *defined* as `E(p) ∩ r(n)`. Because the meet is the only route to a node's capability, monotonicity (and hence A.5) is unavoidable rather than a property to be audited.

### A.9 Conformance over expression languages

The theorems above are stated over the capability lattice `L`, never over a syntax. A named syntactic profile supplies a mapping `⟦·⟧` from expressions in its fragment into `L`. Because `E(n)` and `D(n)` are defined by meet and union in `L`, and the proofs use only the lattice properties, the guarantees transfer unchanged to any language admitted through a conforming profile.

Two conditions are required of a profile, and they are what *conforming* means:

- **Totality** — every expression in the fragment denotes some element of `L`.
- **Determinism** — `⟦e⟧` is a fixed element of `L`, identical for every verifier. In particular a denotation MUST NOT depend on verifier-local state; otherwise the meet is not well-defined and conforming verifiers cease to agree.

Monotonicity of the language is **not** required. `E(n) = E(p) ∩ ⟦r(n)⟧ ≤ E(p)` holds whatever `⟦r(n)⟧` denotes, so an expression attempting to broaden is discarded by the meet (A.5). A profile must guarantee that a denotation exists and is fixed, not that it narrows.

Soundness is therefore a **conformance result**: any language admitted through a conforming profile inherits Theorems 1 and 2 without further proof.

### A.10 Relative completeness

Soundness and completeness are separate claims, and the theorems above establish only the first.

*Claim.* For any policy decidable at the gate — decidable from the credential chain and the request alone, identically for every verifier — there is a capability expressing it.

*Argument.* A decidable gate policy `P` partitions requests into permitted and not; write `S_P` for the permitted set. The author names the terms `P` ranges over in a namespace and writes `P` in a conforming profile; a verifier then evaluates it, and nothing is left over. A counterexample could arise only if this specification restricted the available vocabulary. It does not: vocabulary is namespace-supplied and deliberately out of scope. ∎

*Consequence.* The model is **sound, and complete relative to the supplied vocabulary** — relative completeness in the familiar sense, as a program logic is complete relative to the expressiveness of its assertion language. Here the qualifier costs nothing, since the thing completeness is relative to is out of scope by design: anything unexpressible is unexpressed by an author's choice, never excluded by the framework.
