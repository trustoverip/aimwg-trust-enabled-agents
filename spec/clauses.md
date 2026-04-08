

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

TEA Agents conceptually MAY be composed of a Controller, one or more AI models (e.g. LLM and other models), and some methods of implementing Agent-scoped memories: storage of long term information. This conceptial composition is useful in understanding and implementing the TEA, but it is not strictly required. As AI technologies evolve rapidly, the common composition of AI Agents may also change. The TEA method itself however is not dependent on a particular way of agent composition. For example, a TEA does not necessarily require either an LLM or a specific type of long term memory. That being said, this composition is useful to illustrate many challenges we are solving in the TEA method.

The Reference Framework diagram also captures other actors in an Agentic System which are important to define authorization and accountability. In the diagram, the "Human interfaces" box represents entities (such as an application or web browser) controlled by humans or human organizations interacting with the TEA as a "user", for example, prompting or delegating. The "Management and Control Services" box represents external control systems, for example, administrative or operational controls. The "Tools, Services, other systems" box represents any external computational services. These boxes are external entities that we may reference in defining schemes for authorization and accountability.

In a TEA framework, external entities, such as a web service or a user interfacing mobile app, MAY also be TSP-Enabled. In such scenarios, these entities MAY behave just like a TEA. It is an important charactaristic of the TEA framework, the over system can expand into a network based on the common TSP layer. 

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

The TEA Controller MAY have other communication channels other than the TSP Gateway. All such communication channels MUST be rigourously secured in order to prevent threats from breaching the Controller. For further discussions, please refer to [[ref:#security-and-trust-considerations]].

The TEA Controller MUST have one or more public VIDs and MAY have additional private VIDs. It MUST designate at least one public VID as the Introduction VID (IVID) with which first time contacts can be made without prior trust relationships. It MUST also designate at least one public VID as the Authorization VID (AVID) that is used for assign authority and accountability. By default, the AVID and IVID are the same.

The TEA Controller MUST implement the mandatory delegation and accountability functions as defined in this specification.

### Verifiable Identifiers (VIDs)

The TEA MUST use Verifiable Identifiers that are suitable for durable continuous identification. In order to meet this requirement, the VIDs MUST support key rotation and pre-rotation and allow the history of key rotations to be verifiable. TSP is interoperable with multiple VID formats but for ease of implementation and better interoperability, we prefer to choose a small number of VID schemes initially. This set MAY be extended in future.

The TEA MUST support these VIDs:
- **did:webvh**: See https://identity.foundation/didwebvh/v1.0/ (TODO: ADD reference)

The TEA MUST support the pre-rotation feature of the **did:webvh**.

## TSP Gateway

The TSP Gateway MUST support both confidential and meta-data privacy functions that are optional in TSP. It MUST also support both NaCl and HPKE-base modes.

## TSP Message Serializations

The TSP protocol specifies serialization using CESR which covers the envelope and nested and routed envelopes. In addition, it also specifies a set of control messages. For the payload data of a TSP message, TEA can use either native CESR, or JSON, CBOR or MsgPak serializations. This is a very useful feature especially when we have an existing higher layer protocols that we may want to layer over TSP.

### TEA Signed Payload

A TEA may need messages carried by TSP with a sender signature tied to one of its VIDs, for example, the AVID. Even though all TSP messages are signed in the TSP level, that signature is not usable to a third party who is not the receiver. For authorization and accountability features, a TEA will need to present proofs to such third parties.

The TEA MUST implement the native signing scheme as follows:

TODO: ADD signature to payload
TODO: Should this be part of TSP spec or here?


## Transports

TSP is agonostic to transport layer choices. For TEA, we are also agonostic to transport layer options but it will be more convenient in integration with other protocols or systems if we choose the same common options.

The TEA MUST at least support these transport options:

- **Streamable HTTP (SHTTP)**: as defined in the MCP specification (TODO: ADD reference)
- **stdio**: as defined in the MCP specification (TODO: ADD reference)

## Layering Existing Protocols over TSP as Trust Tasks

TSP is designed to support higher layer protocols, called Trust Tasks, over TSP. Such trust tasks MAY be existing commonly used protocols ported over to TSP, e.g. MCP, or can be new protocols specified in one of the following sections, or in other specifications outside of this document.

## MCP over TSP

## Delegation of Authorization and Duty

## Accountability

## Security and Trust Considerations


