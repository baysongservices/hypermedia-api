The Hyperbinding Media Type

The Hyperbinding Media Type is used to define a hypermedia-api protocol binding to map the semantics of a hypervocabulary to an underlying protocol uniform interface. For protocols which lack native mechanisms to convey metadata separate from data content, this will be used to discover and bind to the metadata for the message.


Complete Example
```
{
  "bindingName":"Standard Http Binding",
  "protocolName":"HyperText Transfer Protocol",
  "protocolScheme":"https",
  "linkLocation":"metadata",
  "root":"Link",
  "operations":{
    "retrieve":"GET",
    "information":"HEAD",
    "state":"OPTIONS",
    "place":"PUT",
    "idempotentReplace":"PUT",
    "unsafeModify":"PATCH",
    "remove":"DELETE",
    "tell":"POST",
    "ask":"POST"
  }
}
