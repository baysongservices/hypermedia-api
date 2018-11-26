The goal of this specification is to define a standards based hypermedia API system defined completely decoupled from protocol and resource representation constraints. This will enable the creation of a generic hypermedia user agent which can be coupled to any valid arbitrary vocabulary at runtime to the minimal design time coupling surface. This is accomplished by separating the application domain vocabulary from the protocol binding with standard RFC 8288 Web Links This allows arbitrary hypermedia conveyance with maximum flexibility, testability, and usability while providing the inherent architectural properties desired from ReSTful architectural design.

Current hypermedia API implementations use custom media types such as Hypertext Application Language (HAL), Siren, or JSON API in order to create generic vocabularies and require clients to use out of band documentation to learn hypermedia control meaning. These approaches require the consumer to use a specific media type which breaks content negotiation in order to convey metadata about the resource state. These approaches also generally prescribe a "follow your nose" pattern of hypermedia vocabulary discovery which presents a number of issues for implementers who require the ability to test the extent and performance of their applications. This pattern presents a paradox to consumers, as the vocabulary can never be entirely known yet it's extent is required in order to ensure correct behavior and to create rich interactions with users. In order for hypermedia to present as a compelling and useful tool in the development of APIs, it's vocabulary boundaries must be known and enable systems to form assurances on the systems status and behavior. In short, if hypermedia is the engine for application state is a constraint for APIs built with a ReSTful style, then their vocabularies must be known.

This specification will demonstrate a pattern using existing standards and novel extensions within those standards to convey the metadata necessary for complete hypermedia interaction without prescribing protocol or media type for the interaction. This standard will create a few metadata media types to convey the control vocabularies as well a means to bind the vocabulary to any given protocol. While this specification aims to create a mechanism for hypermedia to be used to drive API interaction, a large consequence not to be overlooked is the above statements mean this system by it's very nature gracefully degrades without loss or penalty in the event of an unsophisticated user agent to current interaction designs. If legacy implementations required the conveyance of hypermedia controls embedded within a specific media type, this system could be made to losslessly support any existing content type. By separating the stateful metadata from the resource representation in addition to minimizing the coupling surface of the application, we also greatly increase the interoperability of the systems across protocol and legacy system boundaries.

In order to explain the methodology and reduce confusion for the sake of this document we will define a few terms:
Resource: A resource is a item available over the network using some protocol identified by a URI.
Property: A property is a named and likely typed data element of a resource which is not substantially significant in it's own context and does not have a URI.
Resource Relationship: A resource relationship is link between two resource contexts which describes the way they are related in a manner which contributes to a larger single context.
Affordance: An affordance is the potential for a resource to conduct a specified state change.

Generally speaking discussions of affordances will include the notion of implicit affordance, which will be excluded from this discussion based on the principle of it being bettert to be explicit than concise. This is important as machines do not have the capacity to accurately reason implied affordance, and it is just as important that automated user agents can accurately navigate the APIs we will create.

The overall philosophy of this approach is to leverage existing and adopted standards as much as possible to increase the size of the addressed population. Other efforts from the Semantic Web domain may be persuing the end goal solution for hypermedia APIs, and this work may simply be an intermediary step, therefor it is important to this effort the approach does not violate the existing principles of the Web in order to provide a transition pathway towards future solutions. While some new media types are created to facilitate the use of this approach, they are simply the first iteration and wholely confined to use as metadata layering on top of the protocol uniform interfaces. All additional novel elements to this approach are created completely as additional definitions within existing standards using the conventional means.

This solution breaks down the interaction of a hypermedia API to the required components with a strong bias to runtime coupling over design time coupling, as design time coupling is a fundamental barrier to evolutionary systems and automated interoperation. The following elements are required to support a rich hypermedia API: vocabulary, protocol binding, and goals. In contrast to contemporary systems, RDF and related technologies, which define an infinite data architecture this specification requires the definition of a complete bounded vocabulary as the sole design time binding element. The power of this system comes from the runtime coupling for the protocol binding and goals which allows robust expressive solutions without large design time documents or excessive unnecessary bytes on the wire.

This approach is designed to be useful for systems in the IoT space through large complex enterprise applications, therefor the server is intended to be explicit and authoritative in the state of the resources, with minimal message size and favor fast resolving of vocabulary and protocol binding over storing large static monolithic definition documents.

Existing Standards Used
As previously stated, this specification greatly leverages existing standards and protocol capabilities to discover and express hypermedia controls. As much of the web technologies are developed in the domain of HTTP there is influence of this protocol across much of the definition space. This specification ensures all relavent hypermedia discovery and expression is done via metadata which allows it to address the widest possible audience. If a particular protocol does not support an analogous metadata mechanism, a protocol specific media type SHOULD be created in order to convey and serialize this metadata in a way which most closely adheres to the desired split of resource representation and metadata.

Existing HTTP Headers
  * Link
  * Profile
  * Prefer
  * Preference-Applied

Existing Prefer Header Preferences
  * Representation

New Definitions

The following media types, header's, and preference values are defined in order to provide the metadata foundation to allow hypermedia to be serialized external to data to augment existing services with additional capabilities.

This specification defines the following Media Types:
  * application/hypervocabulary+json
  * application/hyperbinding+json
  * application/goal+json

The Hypervocabulary Media Type
The hypervocabulary media type exists to define and serialize the custom domain specific vocabularies to be used within this approach. While reuse is a strong fundamental principle within software systems, in this case it has the undesirable side effect of over generifying most hypermedia vocabularies to a point where rich interactions are difficult if not impossible to conduct dynamically. <link to md of hypervocabulary definition format>

The Hyperbinding Media Type
The hyperbinding media type exists to declare a protocol binding and define a pairing between general vocabulary definition elements and the underlying protocol to translate the generic vocabulary to protocol operations at run time. If necessary on the protocol, the binding will also declare the location and format within a specific protocol where the metadata can be found to drive this approach. <link to md of hyperbinding definition format>

The Goal Media Type
The Goal media type is used to declare goals within a domain which may be deseriable for many consumers to adopt without requiring additional design time maintenance coupling to the ever changing vocabulary. This format describes the state machine responsible for defining this goal. <link to md of goal definition format>

Link Relation Types

In order to support the implementation over HTTP and provide protocol agnostic and spanning capabilities for this system, this specification defines the following Link Relation Types:
  * Vocabulary
  * Protocol-Binding
  * Goal
  * Hypermedia-API

Vocabulary
In order to allow richer and deeper content negotiation across protocols, this document defines the Vocabulary link relation type for a server to express the vocabularies it supports. 

Protocol-Binding
In order to allow richer and deeper content negotiation across protocols, this document defines the Protocol-Binding link relation type for a server to express the protocol-bindings it supports.

Goal
In order to allow richer and deeper content negotiation across protocols, this document defines the Goal link relation type for a server to express the protocol-bindings it supports.

Hypermedia-API
In order to allow richer and deeper content negotiation across protocols, this document defines the Hypermedia-API link relation type for a server to link to a hypermedia API which conforms to this documents specification.


Prefer Header Preference Values

In order to support the implementation over HTTP, this specification defines the following HTTP Preference header values:
  * Vocabulary
  * Profile
  * Protocol-Binding
  * Hypermedia-API
  * Curie
  * Goal

Hypermedia-API
The hypermedia-API preference is used to signify the user would like this request to be treated as a hypermedia API request. You can use this field with or without a value. Applying this preference without a value indicates the requester would like this request to be treated as a hypermedia-api request. Additionally, setting the value to "options" will signify the requester would like the server to send links to supported vocabuarlies and protocol-bindings.

Curie
The curie preference is used to request the server use CURIEs within the hypermedia-api metadata for this response. If the user sends a value for the CURIE, the server MUST respect this value when creating hypermedia-api metadata for the next request. If the value provided for the CURIE is invalid, the server MUST respond by not applying the preference unless the `handling=lenient` preference is also requested, then the server MUST apply it's own CURIE and return the value in the preference-applied header.

Vocabulary
The vocabulary preference header is used to request a vocabulary be used for the processing of the current request and response. The preference value MUST be a valid option the server supports and the server MUST apply this preference unless it is unable to do so. If the service is unable to apply the requested valid value and the `handling=strict` value is applied the server MUST respond with an error response that describes the issue. If the `handling=lenient` value is applied, the server SHOULD respond with an error message and provide metadata to supported vocabularies.

Profile
The profile preference is used to request the use of the Profile header to convey the vocabulary information in lieu of the Vocabulary preference header. As there are many uses for the Profile header out of the scope of the hypermedia-api specification, the use of the Profile preference along with the Hypermedia-API preference signifies the profile is to be interpreted in the intentionally limited manner described within this document. When the Profile preference is used, the server SHOULD interpret the messaging and metadata as if it was requested in the preferences.


Protocol-Binding
The protocol-binding preference is used to request the transport level semantics the server should use to render hypermedia-api metadata and process incoming requests. In certain instances protocols may not have the required metadata container to render the metadata separate from the contents of the message, in this case the protocol-binding MUST reference a `application/protocol-binding+json` document which programmatically disambiguates the location of the contents. Clients will use this protocol binding both to runtime bind affordances to a resource as well as to convey to a server the additional semantics applies to the request the client is making in addition to any protocol uniform interface semantics.


Goal
The goal preference is used to indicate the client would like to discover available goals or recieve tailored metadata to achieve a goal within the context of the requested resource. When the client includes a value in the Goal preference it MUST be a valid value the server supports and reference a valid `application/goal+json` document.


Processing CURIEs in 8288 Web Links

In order to support the use of CURIEs within the link targets, link relation types, and the Profile header, this document will outline the process for requesting, resolving, and disambiguating CURIEs such that they are always resolve to the same URIs, and are not ambiguous.

When a consumer agent requests a resource within the hypermedia-api compliant service without a curie preference the server responds with a Preference-Applied Vocabulary with a valid value or Profile header containing a single URI for the profile it applied by default for the vocabulary. On subsequent interactions with the service, the client MAY request the server tailor the response metadata to use CURIEs by sending a curie name and URI pair in the first values of the Profile header in the form of <curie_name %20 URI> or sending the Preference-Applied Curie value with the applied value. If the client chooses to use a CURIE with a Profile header it MUST also apply the Curie preference to the request to ensure the server appropriately processes the non-URI value without error. This is important because in the case of a client request with a message the client MUST send a profile to describe the contents, and the client SHOULD use the CURIE to shorten the value. In all requests which the client intends to express a preference for or interact via CURIEs, the client MUST send the preference curie value and the server SHOULD respond with an empty Preference-Applied curie value.


Processing Goals

The goal header is used to indicate the client would like to discover available goals or recieve tailored metadata to achieve a goal within the context of the requested resource. When a goal preference is requested and the Profile header is missing or does not contain a declared goal, the server will respond with preference-applied header to indicate the metadata has been added to the response. When the goal preference is requested and the Profile header contains a declared goal, the server will response with preference-applied header to indicate the metadata on the response has been tailored to the requested goal. Assuming the consumer continues to send the goal preference and profile with each individual request, the server will continue to apply the header until such time as the client reaches an end state in the goal state machine.


The Profile Header

This document provides additional constraints on the Profile header to limit the semantic complexity and processing burden and define firm constraints for it's use. If the Profile header is used, it is used to convey the vocabulary used in request and response. If a CURIE is to be applied in a request, the format MUST match the form described above, and the CURIE shall apply to the root with fragment or anchor identifiers used to convey specific request semantics.


