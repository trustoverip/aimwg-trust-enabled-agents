
[//]: # (Pandoc Formatting Macros)

[//]: # (# This is an annex {#sec:annexA .normative})

[//]: # (With some text)

[//]: # (# This is another annex {#sec:annexB .informative})

[//]: # (With some more text)

## Annex A (informative): Design Rationale and Comparison
 
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

## Annex B (informative): Relative completeness

Soundness and completeness are separate claims, and the theorems of the Soundness appendix establish only the first.

*Claim.* For any policy decidable at the gate — decidable from the Capability chain and the Exercise alone, identically for every verifier — there is a capability expressing it.

*Argument.* A decidable gate policy `P` partitions Exercises into permitted and not; write `S_P` for the permitted set. The author names the terms `P` ranges over in a namespace and writes `P` in a conforming profile; a verifier then evaluates it, and nothing is left over. A counterexample could arise only if this specification restricted the available vocabulary. It does not: vocabulary is namespace-supplied and deliberately out of scope. ∎

*Consequence.* The model is **sound, and complete relative to the supplied vocabulary** — relative completeness in the familiar sense, as a program logic is complete relative to the expressiveness of its assertion language. Here the qualifier costs nothing, since the thing completeness is relative to is out of scope by design: anything unexpressible is unexpressed by an author's choice, never excluded by the framework.
