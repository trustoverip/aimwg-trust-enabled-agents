
[//]: # (Pandoc Formatting Macros)

[//]: # (\mainmatter)

[//]: # (\doctitle)

## Scope

This specification defines Trust-Enabled Agents (TEAs): agents that are endpoints of the Trust Spanning Protocol (TSP) and that hold, delegate, exercise, and account for authority using verifiable, cryptographically bound data.

It specifies:

- the requirements an agent must meet to be a TEA;
- the authority graph over Verifiable Identifiers (VIDs) in which delegation, invocation, revocation, and accountability are defined;
- the exchanges by which two TEAs establish, use, and settle a trust relationship — delegation of authority with its limitations and obligations, invocation of that authority, and the evidence these produce;
- how TEAs integrate with AI agent communication protocols such as MCP and A2A.

It does not specify how VIDs are created or how TSP relationships are formed, which are defined in the TSP specification and the applicable VID methods, nor does it define the concrete vocabularies of resources, abilities, limitations, and obligations, which are supplied by namespaces or applications outside this specification.

This specification is intended for AI agents but does not depend on any particular AI technology; a TEA need not contain an AI model.
