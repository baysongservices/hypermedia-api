The Goal Media Type

The goal media type is used to express a state machine to allow a server to dynamically tailor the hypermedia for a request without the burden of maintaining state on the server or redundant logic within the client.

This specification defines the media type "application/goal+json" to express this state machine. As the name convention implies this format is based upon json to express a normalized flattened directed graph of nodes and edges. The nodes in this graph are valid resources in the linked hypervocabulary and the edges are the hypermedia affordance transitions. Each graph MUST declare at least 1 node in the machine as a START node, and one node as an END node though the graph MAY define more nodes as START and END nodes.

Vocabulary
The vocabulary property MUST refer to a valid hypervocabulary.

State
A state in the graph is a node which must obey the following rules for it to be valid. It must have at least one edge transitioning in or out of the node. It MUST link to a valid resource definition in a hypervocabulary. Finally, it MUST be reachable by the normal transitioning of the state machine. A server MAY support discovery of the goal at any State within the machine.

START State
An start state MUST adhere to all the properties of a State with the additional requirement that it also MUST NOT have any valid transitions entering the START State. As a START State, this state will be one which the goal MUST be discoverable, as such it does not require an initial transition to start the machine.

END State
An END State MUST adhere to all the properties of a State with the additional requirement that it MUST NOT have any valid transitions leaving the END State.

Transition
A transition MUST be a valid affordance within the related vocabulary or a value in the standard IANA relationship type registry.

All items in the goal will be internally identified by a UUID to disambiguate between resources which can be both a START State and intermediate States within a Goal. The document will appear in the following structure.
```
{
  "version":"0.1",
  "name":"approveAndPublish",
  "start":["726d9d0a-b029-4bcb-82f4-1c7e19e41de8"],
  "vocabulary":"http://example.org/blog/vocab/blog1.1",
  "states":[],
  "transitions":[]
}
```

The start field is an order array of starting States which SHOULD be used by services to determine which State a client should use to begin the Goal interaction. A client will request this simplified representation of the goal by retrieving the goal with the preference header `Prefer: return=minimal`.

The structure of a State node consists of "stateId", "stateName", and "type" required fields. The "stateId" is a UUID to uniquely identify a state within a document. The "stateName" field is a valid resource within the referenced hypervocabulary. The type field consists of one of three values: start, step, end.

```
{
  "stateId":"726d9d0a-b029-4bcb-82f4-1c7e19e41de8",
  "stateName":"#post",
  "type": "start"
}
```

The structure of a transition consists of the following required fields: "source", "destination", and "affordance". To increase the human readability of the format the following optional fields can be set from the corresponding State values as well: "sourceName", and "destinationName". The "source" and "destination" fields are the UUIDs of the corresponding States. The affordance field is the affordance within the referenced vocabulary which will result in the state transition. As all the required fields result in a unique compound key within the document, no unique key is required for transition nodes.

```
{
  "source":"726d9d0a-b029-4bcb-82f4-1c7e19e41de8",
  "destination":"bc64e960-5913-4655-a258-88ce06b69b2d",
  "affordance":"#post/approvePost",
  "sourceName":"#post",
  "destinationName":"#post"
}
```

Minimal Representation
If a client requests a minimal representation of the document, the server MUST return an prioritized array of all the start states for the client to discover. The client MUST select the first state from it's available hyeprmedia controls.
```
{
  "start":["#post/approvePost"]
}
```

Complete Example
```
{
  "version":"0.1",
  "name":"approveAndPublish",
  "start":["726d9d0a-b029-4bcb-82f4-1c7e19e41de8"],
  "vocabulary":"http://example.org/blog/vocab/blog1.1",
  "states":[
    {
      "stateId":"726d9d0a-b029-4bcb-82f4-1c7e19e41de8",
      "stateName":"#post",
      "type":"start"
    },   
    {
      "stateId":"bc64e960-5913-4655-a258-88ce06b69b2d",
      "stateName":"#post",
      "type":"step"
    },
    {
      "stateId":"99263316-2ddd-4db4-9088-8887351b04f1",
      "stateName":"#post",
      "type":"end"
    }
  ],
  "transitions":[
    {
      "source":"726d9d0a-b029-4bcb-82f4-1c7e19e41de8",
      "destination":"bc64e960-5913-4655-a258-88ce06b69b2d",
      "affordance":"#post/approvePost",
      "sourceName":"#post",
      "destinationName":"#post"
    },
    {
      "source":"bc64e960-5913-4655-a258-88ce06b69b2d",
      "destination":"99263316-2ddd-4db4-9088-8887351b04f1",
      "affordance":"#post/publishPost",
      "sourceName":"#post",
      "destinationName":"#post"
    }
  ]
}
```
This small but complete example demonstrates the full range of the capabilities of a goal. A single resource becomes three states because of the unique rules for the state-machine which allows the server processing request metadata against this model to very efficiently map the correct affordances for the rest of the Goal.
