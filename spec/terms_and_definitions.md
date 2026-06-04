
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

~ A TSP-Enabled Agent, or TEA, is an agent that is a TSP endpoint [[1](#TSP)]. As defined in [[1](#TSP)], a TSP endpoint has Verifiable Identifiers (VID), communicates with other TEAs through TSP. A TEA may also interact with other external systems that are not TEAs. A TEA is a unit that can be delegated with authority to take actions and be accounted for for effects of these actions. For convenience, we also allow the use of TEA Agent as equivalent to just TEA. This specification defines what a TEA MUST, SHOULD, or MAY do. 

[[def: Verifiable Identifier, VID]]

~ A Verifiable Identifier is a class of identifiers defined in the TSP specification [[1](#TSP)]. The TEA uses a sub-class of VIDs that have stronger cryptographic verifiability for both control and provenance history. Such proof of continuous control of VID's key material is necessary to enable long term durable relationships and reputation for TEAs. This specification defines detailed requirements to the VIDs used for TEAs.

[[def: Wallet, Vault, Secure Data Store]]

~ As a TSP endpoint, a TEA needs a method to store securely keys and other secrets. In this specification, we call such secure data store as a wallet, or a vault, or secure data store, interchangably.

[[def: Delegation of Authority,  Delegation of Authority and Duty, Delegation]]

~ A TEA may be given by another party, e.g. an application that can interact with a human user or an administrative system or another party, the authorization to take certain scoped actions or access certain scoped resources on their behalf or for their benefits within a limited time horizon. The process that such authorization is given is called Delegation of Authority. In this specification, we define ways such delegation can be performed through TSP based messages. When an authorization is delegated to TEAs, such delegated authority can be accompanied or attached with explcitly expressed Duties also expressed in TSP based messages that are defined in this specification. 

[[def: Accountablity]]

~ A TEA may be required to observe some rules or duties that are not automatically enforced or even explicitly stated. For example, computer systems often do not enforce by the system implementation Terms of Use agreements. Such terms can later be accounted after violation occured. Accountability is a process that can be used to investigate and discover whether certain such rules or duties were indeed observed by the TEA during a given time period. This process is usually performed after the fact.

[[def: Audit]]

~ Audit is a process of performing accountability over the history of a period of time.





