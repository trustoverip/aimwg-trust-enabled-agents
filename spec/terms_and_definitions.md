
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

~ An agent is an anatonomous process over a period of time. An agent may be powered by various AI technologies which introduce capabilities such as use of natural languages, image and visual signal generation and understanding, use of programming and other online tools, reasoning, and so on. In this specification, we assume AI technologies may be used by agents, but they do not have to. Unless we make an explicit distinction in certain context, we use the terms agent and AI agent interchangably. 

[[def: TSP-Enabled Agent, TEA, TEA Agent]]

~ A TSP-Enabled Agent, or TEA, is an agent that is a TSP endpoint, has Verifiable Identifiers (VID), communicates with other TEAs through TSP. A TEA may also interact with other external systems that are not TEAs. A TEA is a unit that can be delegated with authority to take actions and be accounted for for effects of these actions. For convenience, we also allow the use of TEA Agent as equivalent to just TEA.

[[def: Trust Spanning Protocol, TSP]]

~ The Trust Spanning Protocol (TSP) is a foundational trust protocol that ensures endpoints (the TSP speakers), as identified by Verifiable Identifiers (VIDs), can have strong assurance of endpoint authenticity, message integrity, confidentiality and meta-data privacy. 

[[def: Verifiable Identifier, VID]]

~ A Verifiable Identifier is a class of identifiers defined in the TSP specification. The TEA uses a sub-class of VIDs that have strong cryptographic verifiability for both control and provenance history. 

[[def: Wallet, Vault, Secure Data Store]]

~ As a TSP endpoint, a TEA needs a method to store securely keys and other secrets. In this specification, we call such secure data store as a wallet, or a vault, or secure data store.

[[def: Delegation of Authority]]

~ A TEA may be given by another party, e.g. an application that can interact with a human user for such authority or an administrative system, through some process that we call Delegation. In this specification, we define ways such delegation can be performed through TSP based messages.

[[def: Accountablity]]

~ A TEA may be required to observe some rules or duties that are not automatically enforced or even explicitly stated. For example, computer systems often do not enforce by the system implementation Terms of Use agreements. Such terms can later be accounted after violation occured. Implicit rules or imprecise rules are not explicitly stated or not machine readable. Accountability is a process that can investigate and discover whether certain such rules or duties were indeed observed by the TEA during a given time period. This process is usually performed after the fact.

[[def: Audit]]

~ Audit is a process of performing accountability over the history of a period of time.





