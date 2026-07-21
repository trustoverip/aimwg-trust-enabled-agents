
[//]: # (Pandoc Formatting Macros)

[//]: # (Portable Document Format)

[//]: # (blank)

[//]: # (: file format defined by ISO 32000-2)

## Terms and Definitions

For the purposes of this document, the following terms and definitions apply.

ISO and IEC maintain terminological databases for use in standardization at the following addresses:

 - ISO Online browsing platform: available at <https://www.iso.org/obp>
 - IEC Electropedia: available at <http://www.electropedia.org/>

[[def: Agent, AI Agent]]

~ An agent is an anatonomous process over a period of time. An agent may be powered by various AI technologies which introduce capabilities such as use of natural languages, image and visual signal generation and understanding, use of programming and other online tools, reasoning, and so on. In this specification, we assume AI technologies may be used by agents, but they do not have to. We also assume AI agents may have persistent memory of data that they come to possess or have access to. Unless we make an explicit distinction in certain context, we use the terms agent and AI agent interchangably. Figure 1 provides a conceptual framework to define an agent's main functional components and their interconnections among the components and with external systems.

[[def: Trust Spanning Protocol, TSP]]

~ The Trust Spanning Protocol (TSP) [[1](#TSP)] is a foundational trust protocol that ensures endpoints (the TSP speakers), as identified by Verifiable Identifiers (VIDs), can have strong assurance of endpoint authenticity, message integrity, confidentiality and meta-data privacy. The TSP specification is a normative reference of this specification. Terms that are defined in [[1](#TSP)] and used in this specification by references are not redefined but may be further constrained by this specification.

[[def: TSP-Enabled Agent, TEA, TEA Agent]]

~ A TSP-Enabled Agent, or TEA, is an agent that is a TSP endpoint [[1](#TSP)]. As defined in [[1](#TSP)], a TSP endpoint has Verifiable Identifiers (VID), communicates with other TEAs through TSP. A TEA may also interact with other external systems that are not TEAs. A TEA is a unit that can be delegated with authority to take actions and be accounted for for effects of these actions. For convenience, we also allow the use of TEA Agent as equivalent to just TEA. This specification defines what a TEA MUST, SHOULD, or MAY do. A TEA contains a TEA Controller, maintains a Wallet, and designates at least one Introduction VID and at least one Authorization VID. A TEA MAY be composite; where it is, a single entity and VID represents the whole for the purposes of delegation and accountability.

[[def: TEA Controller, Controller]]

~ The TEA Controller is the component within a TEA that constitutes the agent's domain of control and is itself a conformant TSP endpoint. Where this specification refers to "the Controller" in the context of an agent's architecture, it means the TEA Controller. The TEA Controller contains the Wallet, to which it has sole access; contains the TSP Gateway, through which all inbound and outbound TSP messages pass; mediates all of the AI model's context, memory access, and tool use; holds the agent's VIDs; and implements the mandatory delegation and accountability functions. The TEA Controller is the trust boundary of the agent: authority is enforced at the Controller rather than inside the AI model, which is what bounds a manipulated or compromised model to the capability actually delegated to it. This architectural sense is distinct from the identifier-control sense of "controller" used for the entity that holds a VID's private keys; for a TEA, the controlling entity is ultimately a Principal, while operational use of the keys is performed by the TEA Controller on that Principal's behalf.

[[def: TSP Gateway]]

~ The TSP Gateway is the component within the TEA Controller that sends and receives TSP messages. All TSP messages going out or coming in MUST pass through the TSP Gateway, which provides assured authenticity, message integrity, confidentiality, and potentially meta-data privacy. A TEA Controller MAY have communication channels other than the TSP Gateway, but all such channels must be rigorously secured.

[[def: Verifiable Identifier, VID]]

~ A Verifiable Identifier is a class of identifiers defined in the TSP specification [[1](#TSP)]. The TEA uses a sub-class of VIDs that have stronger cryptographic verifiability for both control and provenance history. Such proof of continuous control of VID's key material is necessary to enable long term durable relationships and reputation for TEAs. This specification defines detailed requirements to the VIDs used for TEAs.

[[def: Authorization VID, AVID]]

~ An Authorization VID is a public VID that a TEA designates for assigning authority and accountability. Delegations are issued under, and verified against, the Delegator's AVID, and authorization or accountability content is signed under the AVID when it must be verifiable by a third party. A TEA MUST designate at least one AVID.

[[def: Introduction VID, IVID]]

~ An Introduction VID is a public VID that a TEA designates for first contact, with which parties having no prior trust relationship can reach it. A TEA MUST designate at least one IVID. By default, the AVID and the IVID are the same VID.

[[def: Wallet, Vault, Secure Data Store]]

~ As a TSP endpoint, a TEA needs a method to store securely keys and other secrets. In this specification, we call such secure data store as a wallet, or a vault, or secure data store, interchangably. Only the TEA Controller has access to the Wallet. The Wallet performs cryptographic operations without exposing private key material to the layers above it, and SHOULD hold key material in a hardware security module or secure enclave where one is available.

[[def: Principal]]

~ A Principal is a natural person or organization that can bear accountability. The accountability chain of any authority a TEA holds or exercises MUST terminate in a Principal; a TEA MUST NOT be the terminal responsible party for an authority. The Principal for an agent is typically its operator — the organization or individual responsible for the agent — not the infrastructure provider, not an Identity Provider, and not a platform vendor.

[[def: Delegation of Authority,  Delegation of Authorization and Obligation, Delegation]]

~ A TEA may be given by another party, e.g. an application that can interact with a human user or an administrative system or another party, the authorization to take certain scoped actions or access certain scoped resources on their behalf or for their benefits within a limited time horizon. The process that such authorization is given is called Delegation of Authority. In this specification, we define ways such delegation can be performed through TSP based messages. When an authorization is delegated to TEAs, such delegated authority can be accompanied or attached with explcitly expressed Obligations also expressed in TSP based messages that are defined in this specification. A delegation conveys a Capability together with any post-gate policy — the Obligations the Delegate accepts and the Assumptions the authority rests on — carried in a single authorization ACDC issued by the Delegator to the Delegate under the Delegator's AVID.

[[def: Capability]]

~ A capability is an authority, modelled as the triple (resource, ability, limitation): which object, which operation, and under what limits the operation may be exercised. All three narrow in the same direction, and the authorization decision — whether a given request is within authority — is computed only from the capability. A capability is carried in the attribute section of an authorization ACDC.

[[def: Limitation]]

~ A limitation is a typed bound restricting the conditions under which an otherwise-granted ability may be exercised, for example a maximum amount or a permitted category. A limitation is part of the capability and is decidable at the moment of exercise. It is the device that the capability-security literature calls a caveat.

[[def: Obligation]]

~ An obligation is a duty the Delegate owes, which may come due in the future and is therefore not decidable at the moment of exercise. Obligations are carried in the rule section of an authorization ACDC, accumulate down a delegation chain, and are enforced through accountability; they do not enter the authorization decision. An obligation is expressed as a conditional: a trigger (the antecedent) makes it due, and discharge is evidenced by a TEA Signed Payload referencing the obligation clause and the triggering artifact. If an obligation is unmet, the obligor is answerable — a breach.

[[def: Assumption]]

~ An assumption is a fact the authority rests on that the Delegate can neither control nor observe. Nobody is obliged to make it true, so it is not an obligation; if it proves false the act is undermined but no party is at fault — it goes to validity rather than breach. Assumptions are the same device as a contract's "whereas" clauses, and like those they serve as a defence only because they are disclosed up front and signed. An assumption belongs in a credential only if it conditions the authority. Where the Delegate can observe a fact, state the duty to watch for it and act, which is an obligation, rather than an assumption.

[[def: Post-gate policy]]

~ Post-gate policy is the stated policy settled after the act rather than at the gate: the Obligations a Delegate owes and the Assumptions the authority rests on. It rides alongside the capability, does not enter the authorization decision, and accumulates by union down a delegation chain. Obligations are the Delegate's exposure and assumptions are its cover, so the line between them is the risk allocation, written down and signed.

[[def: Effective Capability, Effective Obligation Set]]

~ The effective capability and effective obligation set are the authority and obligations actually held by a node in a delegation chain. The effective capability is the meet (intersection) of the capabilities along the chain, so it can only narrow as authority is re-delegated; the effective obligation set is the union of the obligations along the chain, so it can only grow. Together these give the soundness properties that no node's authority exceeds that of its root, and no node can shed an obligation imposed upon it. The model is sound, and complete relative to the supplied vocabulary: anything decidable at the gate can be expressed as a capability, since the vocabulary is namespace-supplied and unrestricted by this specification.

[[def: Closed Capability Core]]

~ The closed capability core is the decidable resource/ability/limitation model whose meet every conforming verifier computes identically without a general policy engine. It is the security and safety floor of the delegation model and establishes a provable ceiling on authority. It is also the pinned evaluation semantics: it fixes what a limitation means and how limitations compose, but not how one is written.

[[def: Interpreted Policy]]

~ Stated policy is either computed — evaluated mechanically against the closed capability core — or interpreted, meaning read and judged by a capable party. This is an attribute of an individual limitation, obligation, or assumption rather than a separate layer or kind. Interpreted policy is expected to be the principal avenue by which parties express what they actually mean, and its content may be prose, other text, or any medium a capable party can interpret. Interpretation cannot escalate authority: an interpreted limitation is still a limitation, and limitations only narrow, so the worst a misreading can do is fail to narrow.

[[def: Profile, Named Syntactic Profile]]

~ A profile is a published fragment of an existing policy language's grammar, together with the mapping from expressions in that fragment into the capability core. It allows an implementation to express computed limitations in a familiar language while the meaning and composition remain the specification's. A conforming verifier MUST reject an expression outside the declared profile. A profile must be total (every expression in the fragment denotes) and deterministic (the denotation is fixed and identical for every verifier); it need not be monotone, since the meet discards an expression that attempts to broaden. Languages used through conforming profiles are interchangeable syntaxes over one semantics.

[[def: Namespace]]

~ A namespace supplies the concrete values the capability axes draw on: resource schemes, ability vocabularies, limitation-types, and obligation-types. Owner-local and community-wide namespaces coexist, each published and SAID-addressed. Trust in a namespace comes from the self-addressing identifier, not from any authority that serves it, so how many sources serve one is an availability question rather than a trust one — a single registry is the N=1 case and is admissible. A sole source cannot substitute a definition undetected but can withhold one, so resolution failure costs the ability to decide, never the correctness of a decision actually made.

[[def: Intelligent Agent Assumption]]

~ This specification assumes TEA agents are capable: the protocol supplies a common language and a communication channel, not a reasoning engine, and does not itself evaluate the wisdom, consistency, or satisfiability of what parties agree to. A TEA evaluates the terms it accepts on its own. Discretion under this assumption cannot exceed a capability — no judgment reasons an agent into authority it does not hold — but is free over interpreted policy, obligations, and assumptions, so no separate judgment tier is defined.

[[def: Trust Task]]

~ A Trust Task is a higher layer protocol carried over TSP. A Trust Task MAY be an existing protocol ported to TSP, or a protocol newly defined in this or another specification. A Trust Task protocol MUST conduct all of its trust-establishing communication over the TSP Gateway, and MUST NOT re-implement the authenticity, message integrity, confidentiality, or metadata-privacy guarantees that TSP already provides. Where a Trust Task carries content that a third party must be able to verify, it MUST carry that content as a TEA Signed Payload.

[[def: Authenticated Exchange, Authenticated Exchange Protocol]]

~ The Authenticated Exchange Protocol is a Trust Task defined in this specification by which two TEAs negotiate a request and the conditions under which it will be fulfilled. It uses four message types — Propose, Accept, Ack, and Withdraw — with the Ack as the act that binds the agreement. Because TSP has already mutually authenticated the two VIDs, the pattern does not perform party authentication; it concerns only the content of the negotiation.

[[def: TEA Signed Payload]]

~ A TEA Signed Payload is a payload bound to a TEA's VID by a signature that any third party can verify, independently of TSP message-level signing. It consists of a payload in any permitted serialization, a SAID over that payload, and one or more signatures over that SAID produced under the signing VID's key state and carried as CESR attachments. For authorization and accountability content, the signing VID is the AVID. Where the payload is an ACDC, its issuer signature over its SAID serves this purpose directly. The TSP-level signature authenticates a message as sent, including its encrypted payload, and so assures the receiver; it is not usable by a third party that is not the receiver as proof of what was said. A TEA Signed Payload is in addition to, not a substitute for, TSP message-level signing.

[[def: Authentic Chained Data Container, ACDC]]

~ An Authentic Chained Data Container is the container used to carry authorizations, obligations, and assumptions. An authorization ACDC uses its three sections for distinct purposes: the attribute section carries the Capability, the rule section carries the post-gate policy — obligations and assumptions — and the edge section chains to the ACDC that establishes the issuer's own authority. An ACDC body is content-addressed by its SAID but is not itself signed; authenticity comes from the issuer's signature attached at issuance and anchored in the issuer's key state.

[[def: Self-Addressing Identifier, SAID]]

~ A Self-Addressing Identifier is a cryptographic digest computed over a container's own contents, making the container content-addressed and tamper-evident. A SAID establishes integrity, not authorship: it is a digest, not a signature.

[[def: I2I, DI2I]]

~ I2I and DI2I are ACDC edge operators that constrain how a container relates to the container its edge references. I2I ("Issuer-To-Issuee") requires that the Issuer of the current ACDC be the Issuee of the referenced one, which is the structural expression that a party may only delegate authority it holds. DI2I is the relaxation used where the issuing identifier is itself a delegated identifier. Together with the restriction-only encoding of re-delegations, the I2I constraint is what makes attenuation intrinsic rather than merely checked.

[[def: Accountability]]

~ A TEA may be required to observe some rules or obligations that are not automatically enforced or even explicitly stated. For example, computer systems often do not enforce by the system implementation Terms of Use agreements. Such terms can later be accounted after violation occured. Accountability is a process that can be used to investigate and discover whether certain such rules or duties were indeed observed by the TEA during a given time period. This process is usually performed after the fact. The accountability chain of any authority a TEA holds MUST terminate in a Principal.

[[def: Audit]]

~ Audit is a process of performing accountability over the history of a period of time.
