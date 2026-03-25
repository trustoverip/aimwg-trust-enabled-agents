

[//]: # (Pandoc Formatting Macros)

[//]: # (Main content {#sec:content})

## Executive Summary
CoLorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mi ipsum faucibus vitae aliquet nec ullamcorper sit amet. Scelerisque fermentum dui faucibus in ornare quam viverra orci. Maecenas ultricies mi eget mauris pharetra. Tempor nec feugiat nisl pretium fusce id. In ante metus dictum at tempor commodo ullamcorper a. Nulla at volutpat diam ut venenatis tellus in. 

Quis hendrerit dolor magna eget est lorem ipsum dolor. Cursus metus aliquam eleifend mi in. Volutpat commodo sed egestas egestas fringilla phasellus. Viverra adipiscing at in tellus integer feugiat scelerisque varius. Arcu bibendum at varius vel pharetra. Dictum at tempor commodo ullamcorper. Eu consequat ac felis donec et odio pellentesque diam volutpat. Pretium quam vulputate dignissim suspendisse in est. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. Dolor morbi non arcu risus quis varius.

Maecenas volutpat blandit aliquam etiam erat velit. Quis imperdiet massa tincidunt nunc pulvinar. Placerat vestibulum lectus mauris ultrices eros in cursus turpis massa. Sodales ut etiam sit amet. Orci nulla pellentesque dignissim enim sit amet venenatis. Fusce ut placerat orci nulla pellentesque dignissim enim sit. Sollicitudin ac orci phasellus egestas tellus rutrum tellus. Enim eu turpis egestas pretium aenean pharetra magna ac placerat. Et malesuada fames ac turpis egestas. Integer quis auctor elit sed vulputate. Massa tempor nec feugiat nisl pretium.

------------------------------------

## TEA Agent Reference Framework

As shown in the Reference Framework diagram below, we define TEA Agents as units of authorization and accountability that are identified by Verifiable Identifiers (VID) as defined in the TSP specification and further refined in this specification.

TEA Agents therefore MUST have methods to guard, maintain and use the VIDs and their associated secrets, such as keys. Practically, we may refer to these methods as Wallets and Vaults. In other words, TEA Agents MUST have wallets.

TEA Agents conceptually MAY be composed of a Controller, one or more AI models (e.g. LLM and other models), and some methods of implementing Agent-scoped memories: storage of long term information. This conceptial composition is useful in understanding and implementing the TEA, but it is not strictly required. As AI technologies evolve rapidly, the common composition of AI Agents may also change. The TEA method itself however is not dependent on a particular way of agent composition. For example, a TEA does not necessarily require either an LLM or a specific type of long term memory. That being said, this composition is useful to illustrate many challenges we are solving in the TEA method.

The Reference Framework diagram also captures other actors in an Agentic System which are important to define authorization and accountability. In the diagram, the "Human interfaces" box represents entities (such as an application or web browser) controlled by humans or human organizations interacting with the TEA as a "user", for example, prompting or delegating. The "Management and Control Services" box represents external control systems, for example, administrative or operational controls. The "Tools, Services, other systems" box represents any external computational services. These boxes are external entities that we may reference in defining schemes for authorization and accountability.

In a TEA framework, external entities, such as a web service or a user interfacing mobile app, MAY also be TSP-Enabled. In such scenarios, these entities MAY behave just like a TEA. It is an important charactaristic of the TEA framework, the over system can expand into a network based on the common TSP layer. 

The diagram also contains a box representing other AI Agents. These Agents MAY also be TEAs, or may not. When these are TEA Agents, this opens a flexible way of constructing more complex agentic systems by a group of networked TEA Agents, where the networking among them is based on TSP.

Combining these elements, we may construct a TSP based network where each node is a TSP-Enabled entity. For the purpose of this specification, all these TSP-Enabled nodes are TEAs. This is illustrated in the next diagram.

Finally, TEA Agents can be composite, as shown in the next diagram by a simple example. Composite TEAs are not limited to one way of composition. This diagram is only showing a simple example for illustration. When a TEA Agent is a composite Agent, there MUST be an entity (and therefore VID) representing the whole for the purpose and requirements of being a TEA Agent. This entity is overall who/what authorities are delegated to and accountability is assigned to.


### Heading 2

CoLorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mi ipsum faucibus vitae aliquet nec ullamcorper sit amet. Scelerisque fermentum dui faucibus in ornare quam viverra orci. Maecenas ultricies mi eget mauris pharetra. Tempor nec feugiat nisl pretium fusce id. In ante metus dictum at tempor commodo ullamcorper a. Nulla at volutpat diam ut venenatis tellus in. 

Quis hendrerit dolor magna eget est lorem ipsum dolor. Cursus metus aliquam eleifend mi in. Volutpat commodo sed egestas egestas fringilla phasellus. Viverra adipiscing at in tellus integer feugiat scelerisque varius. Arcu bibendum at varius vel pharetra. Dictum at tempor commodo ullamcorper. Eu consequat ac felis donec et odio pellentesque diam volutpat. Pretium quam vulputate dignissim suspendisse in est. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. Dolor morbi non arcu risus quis varius.


### Heading 2

This section describes key concepts used in the content of the document.  If the concept requires a footnote, one should be inserted as follows:  Any defined term should be hyperlinked to its glossary definition (or at least bolded).

## Heading 1

Quis hendrerit dolor magna eget est lorem ipsum dolor. Cursus metus aliquam eleifend mi in. Volutpat commodo sed egestas egestas fringilla phasellus. Viverra adipiscing at in tellus integer feugiat scelerisque varius. Arcu bibendum at varius vel pharetra. Dictum at tempor commodo ullamcorper. Eu consequat ac felis donec et odio pellentesque diam volutpat. Pretium quam vulputate dignissim suspendisse in est. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. Dolor morbi non arcu risus quis varius.

### Heading 2

Eu consequat ac felis donec et odio pellentesque diam volutpat. Pretium quam vulputate dignissim suspendisse in est. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. Dolor morbi non arcu risus quis varius.


#### Heading 3

CoLorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mi ipsum faucibus vitae aliquet nec ullamcorper sit amet. Scelerisque fermentum dui faucibus in ornare quam viverra orci. Maecenas ultricies mi eget mauris pharetra. Tempor nec feugiat nisl pretium fusce id. In ante metus dictum at tempor commodo ullamcorper a. Nulla at volutpat diam ut venenatis tellus in.

##### Heading 4

Sodales ut etiam sit amet. Orci nulla pellentesque dignissim enim sit amet venenatis. Fusce ut placerat orci nulla pellentesque dignissim enim sit. Sollicitudin ac orci phasellus egestas tellus rutrum tellus. Enim eu turpis egestas pretium aenean pharetra magna ac placerat. Et malesuada fames ac turpis egestas. Integer quis auctor elit sed vulputate. Massa tempor nec feugiat nisl pretium.

## Figures, Bullets and Numbered Lists

### Figure

Arcu bibendum at varius vel pharetra. Dictum at tempor commodo ullamcorper. Eu consequat ac felis donec et odio pellentesque diam volutpat. Pretium quam vulputate dignissim suspendisse in est. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. Dolor morbi non arcu risus quis varius


[image]: image.png "Image Title" 
![Alt text][image] 
A reference to the [image](#image).

### Figure 1. Mi ipsum faucibus vitae


### Lists

Eu consequat ac felis donec et odio pellentesque diam volutpat:

1. Establish Magna
    1. Dictum at tempor commodo
    1. Volutpat commodo sed,
        1. Cursus metus aliquam eleifend.


- Identify Bibendum,
   - Analyze Donec,
      - Treat Fringilla,

### Quote

The following is a quote from a referenced source:

> This is a quote from an author in a paper that provides invaluable insight into the subject of this ToIP document. Be sure to include a reference to the source.

### Conclusion

CoLorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mi ipsum faucibus vitae aliquet nec ullamcorper sit amet. Scelerisque fermentum dui faucibus in ornare quam viverra orci. Maecenas ultricies mi eget mauris pharetra. Tempor nec feugiat nisl pretium fusce id. In ante metus dictum at tempor commodo ullamcorper a. Nulla at volutpat diam ut venenatis tellus in.

- This section seems incorrectly indented: https://github.com/trustoverip/specification-template/issues/1
