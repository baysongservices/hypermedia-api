```
version
includes - Name collisions will be referred to as curie:name within the resolves field. Best practice would be to explicitly resolve any conflicts in resolves, but by default definitions are first come first served. The resolved object can perform two actions, it can alias a new name from a curied collision, or it will assign the correct reference for the disputed name within this document. As this requires resolution of collisions it is REQUIRED this field resolves all included documents before processing the name resolutions. 
    link[]
        href
        curie
    resolutions
        alias
        target
resources - resource composite names and affordances share a namespace. A vocabulary author SHOULD strive to define the vocabulary without collisions, however sometimes this is inevitable and the affordance shall be prefixed by the resource name in the [resourceName:affordance] format.
    name
    description
    docs - may point to additional fields such as an ALPS profile to describe additional semantics
        link
            href
            curie
        value
        format
    attributes
        name - the name field can be referred to directly, in the event of any naming collisions processors should implicitly namespace to [resource name:attribute name]
        description
        type [textual, numeric, boolean, complex]
        docs 
            link
                href
                curie
            value
            format
    composites
        resName
        name
        description
        docs
            link
                href
                curie
            value
            format
    affordances
        ?resName - parent resource name
        name
        description
        docs
            link
                href
                curie
            value
            format
        type - not sure which
            safe / idempotent / unsafe
            inquire / place/ replace / remove / tell
        message
            name
affordances
    name
    description
    docs
        link
            href
            curie
        value
        format
    type - not sure which
        safe / idempotent / unsafe
        inquire / place/ replace / remove / tell
    message
        name
messages - the name and attributes of a resource is considered a message, you can also send link headers w/ the request to include composites
    name
    description
    docs
        link
            href
            curie
        value
        format
    attributes
        name
        description
        docs
            link
                href
                curie
            value
            format
```
