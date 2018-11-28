Initial Interaction with the root resource.
```
GET http://exampleblog.org

200 OK
Profile: <http://example.org/blog/vocab/blog1,
	  http:example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: profile
Link: <http://example.org/blog/vocab/blog1>; rel="vocabulary"; type="application/hyper-vocabulary+json",
      <http://example.org/blog/vocab/blog1.1>; rel="vocabulary"; type="application/hyper-vocabulary+json",
      <http://example.org/bindings/http//http-standard>; rel="protocol-binding"; type="application/hyper-binding+json",
      <http://example.org/bindings/http/http-strict-privacy>; rel="protocol-binding"; type="application/hyper-binding+json",
      </users>; rel="http://example.org/blog/vocab/blog1#users",
      </posts>; rel="http://example.org/blog/vocab/blog1#posts",
      </comments>; rel="http://example.org/blog/vocab/blog1#comments"
```
On initial interaction at the base URL using the prefernce model the server replies with the Preference-Applied header for vocabulary, protocol-binding, and hypermedia-api to inform knowledable clients of what defaults the server will apply. It also sends links to the available vocabulary and protocol-binding resources with the newly registered vocabulary and protocol-binding standard relation types.

In order to conform to the hypermedia-api specification over HTTP, these links must point to valid vocabulary and protocol-binding definitions, in this case `application/hypervocabulary+json` and `application/hyperbinding+json` documents respectively. In subsequent interactions, the consumer is able to choose which vocabulary and protocol binding to use much like any other negotiated value.

The vocabulary preference header value is evaluated in two parts, the name and the refernce components. Name of the vocabulary is all of the URI value which prefaces the fragment identifier `#`, or the whole URI if the fragment identifier is not present. The reference value refers to the fragment identifier and everything which follows, and this refers to the portion of the document which describes the root context for this message request or response.

Lets look at the link to `</posts>; rel="http://example.org/blog/vocab/blog1#posts"`.

In this case, as the value for the reference portion #posts refers to a resource definition within the vocabulary, and the vocabulary defines this relation type to refer to a root resource `#posts`.

The client chooses to apply the same protocol-binding and uses it to resolve the vocabulary definition to a protocol operation, an HTTP method in this case. The client now has all the context it needs to know what when it interacts using this link, and just as important how it will to execute the request.
```
GET /posts
Profile: <blog http://example.org/blog/vocab/blog1.1#posts,
          http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie
Prefer: profile
```
In this request the client has chosen to use the higher version vocabulary the service supports, and has also elected for hypermedia-api metadata on the response in the vocabulary to be CURIEd with the value `blog` through it's use of the curie preference value.
```
200 OK
Profile: <blog http://example.org/blog/vocab/blog1.1#posts,
          http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie
Preference-Applied: profile
Link: </posts/15>; rel="next blog#post/getPost",
      </posts/14>; rel="blog#post/getPost",
      </posts/13>; rel="blog#post/getPost",
      </posts/12>; rel="blog#post/getPost",
      </posts/11>; rel="blog#post/getPost",
      rel="self blog#addPost"
```
As you can see from the response, the server has elected to apply the curie preference, and every hypermedia-api metadata link within the vocabulary has been curied for ease of client binding, readability as well as a reduction in bytes on the wire.

The server has also returned a list of links related to the `/posts` resource, lets look at `</posts/15>; rel="next blog#post/getPost"` in particular. As you can see here this link uses both a registered IANA relation type next as well as a curied extension relation type `blog#post/getPost`. The client then uses the previously cached vocabulary and protocol-binding to resolve this safe retrieve resource affordance to a HTTP GET with no message content.
```
GET /posts/15
Profile: <blog http://example.org/blog/vocab/blog1.1#post/getPost,
          http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie
Prefer: profile
```
The client simply executes the request on the protocol as the processing of the hypervocabulary and protocol-binding explicitly and unambiguously stipulate.
```
200 OK
Content-Type: application/json
Profile: <blog http://example.org/blog/vocab/blog1.1#post,
          http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie
Preference-Applied: profile
Link: rel="self blog#post/addComment blog#post/reportPost blog#post/approvePost",
      <http://example.org/blog/goals/approveAndPublish>; rel="goal"; type="application/goal+json"
      </users/2>; rel="author",
      </users/14>; rel="blog#lastEditedBy",
      </comments/333>; rel="blog#comment",
      </comments/352>; rel="blog#comment",
      </comments/358>; rel="blog#comment"
{
  "postContents":"The best post ever!",
  "postPublishDate": null,
  "postApproval": false
}
```
Now that we have accessed a resource with a representation, we start to see some additional metadata from the server as links without URIs. These links refer to the self context, meaning they are links to the same resource they are attached to in this example, and what they do is express the current affordances available to the resource.

Lets look at the `<http://example.org/blog/goals/approveAndPublish>; rel="goal"; type="application/goal+json"` link a little deeper. The link relation type of `goal` defines the target of this link as a document which defines a multi-interaction stateless goal of type `application/goal+json`. If a link with this relation type is available on a resource, it means the current context is able to start the multi-step interaction with an available affordance listed as a start state in the goal document. This is another simple discovery step which will direct the interaction of the user with the service.

One final note for this responce, in contrast to the many alternative approaches to hypermedia where the controls are embedded in a unique media type, hypermedia-api utilizes existing robust web standards to apply this metadata without requireing specialized content types for processing. This also prevents some subtle and inadvertant violations of the underlying protocol uniform interface. As you can see from the interactions thus far, the server had yet to reply to any of these requests with a body, and yet the interaction was completely discoverable, negotiated, and dynamically driven.

This has large impact on the utility of this methodology in a number of ways. This enhances cache reliability using the native cache management capabilities of HTTP by adding another means of comparison across media types to augment eviction of stale entries. As this is implemented using metadata only, it also means this methodology is truely backwards compatible. This system can be implemented on existing services in a way which completely supports both hypermedia-api clients and the existing client base, and can be gradually phased in.
```
GET http://exampleblog.org/posts/15
```
As this style services will exist on the internet or other networks using bookmarkable URIs, it is important to demonstrate how the interaction is bootstrapped if a client directly links to a resource with a stored URI and no metadata on the initial request. Take note of the fact that this is the same resource and operation for the previous request.
```
200 OK
Content-Type: application/json
Preference-Applied: curie=(blog, http://example.org/blog/vocab/blog1)
Preference-Applied: vocabulary=<blog#post>
Preference-Applied: protocol-binding=<http://example.org/bindings/http-standard>
Preference-Applied: hypermedia-api
Link: <blog>; rel="vocabulary"; type="application/hyper-vocabulary+json",
      <http://example.org/blog/vocab/blog1.1>; rel="vocabulary"; type="application/hyper-vocabulary+json",
      <http://example.org/bindings/http//http-standard>; rel="protocol-binding"; type="application/hyper-binding+json",
      <http://example.org/bindings/http/http-strict-privacy>; rel="protocol-binding"; type="application/hyper-binding+json",
      </posts/15>; rel="self blog#post/addComment blog#post/reportPost blog#post/approvePost",
      <http://example.org/blog/goals/approveAndPublish>; rel="goal"; type="application/goal+json",
      </users/2>; rel="author",
      </users/14>; rel="blog#lastEditedBy",
      </comments/333>; rel="blog#comment",
      </comments/352>; rel="blog#comment",
      </comments/358>; rel="blog#comment"
{
  "postContents":"The best post ever!",
  "postPublishDate": null,
  "postApproval": false
}
```

We now encounter the follow your nose dilema. If it doesn't have the bound vocabulary you have no idea there is a publishPost affordance, how do you publish then.. try everything? The goal will curate your interaction.

```
GET http://example.org/blog/goals/approveAndPublish
Prefer: return=minimal
```
The application/goal+json mediaType supports a minimal return representation consisting of an ordered array of affordances to apply when initiating goal processing.
```
200 OK
Content-Type: application/json
{
  "start":"#post/approvePost"
}
```
The specification requires the server to return the goal when the appropriate affordances exist in the current resource state. Servers may also choose to return goals at other times for discovery or other purposes. Therefor it is possible the ordered array doesn't contain any affordance name values, and it is not possible to initiate the goal processing.
```
POST /posts/15
Content-Type: application/json
Profile: <blog http://example.org/blog/vocab/blog1.1#post/approvePost,
          http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie
Prefer: profile
Prefer: goal=http://example.org/blog/goals/approveAndPublish
{
  "message":"This is approved for publishing."
}
```
In this request interaction we see the client performing a POST operation with a message structure it discovered in the vocabulary. This request is still providing the vocabulary field which is used by the server to interpret this POST request as a #post/approvePost message.

Additionally, the client expresses it's preference to complete the http://example.org/blog/goals/approveAndPublish goal through the preference header goal value to prevent it from coupling itself to the internal implementation flow logic of the server.
```
200 OK
Content-Type: application/json
Profile: <blog http://example.org/vocab/blog1#post,
          http://example.org/blog/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: profile
Preference-Applied: curie
Preference-Applied: goal=http://example.org/blog/goals/approveAndPublish
Link: rel="next blog#post/publish"
{
  "postContents":"The best post ever!",
  "postPublishDate": null,
  "postApproval": true
}
```
In this response we are able to highlight the effect the goal header has on the response metadata from the server. The only affordances the server returns in this case are the ones necessary to complete the goal. Additionally all of the composite relationship links have been removed in order to minimize the requirements of these focused interactions.

This example only has a single affordance, but if multiple transitions are available and the goal is defined to allow it the server can also use the next relation type to suggest the next step.
```
POST /posts/15
Content-Type: application/json
Profile: <blog http://example.org/blog/vocab/blog1.1#post/publishPost,
          http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie
Prefer: goal=http://example.org/blog/goals/approveAndPublish
{
  "message":"Check out this long share message, and our post here!",
  "shortMessage":"Check out our post!",
  "imageUrl="..."
}
```
As directed, the client proceeds to send the next message within the goal.
```
200 OK
Content-Type: application/json
Profile: <blog http://example.org/blog/vocab/blog1.1#post,
          http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie
Link: rel="self blog#post/addComment blog#post/reportPost",
      </users/2>; rel="author",
      </users/14>; rel="blog#lastEditedBy",
      </comments/333>; rel="blog#comment",
      </comments/352>; rel="blog#comment",
      </comments/358>; rel="blog#comment"
{ 
  "postContents":"The best post ever!",
  "postPublishDate": {now},
  "postApproval": true
}
```
As the `http://example.org/blog/goals/approveAndPublish` is complete, you'll notice the server has not returned the Preference-Applied header value to signify the processing is complete. The full picture is determined by understanding the HTTP status code as well, which in this case as a `200 OK` indicates the goal processing reached an end state successfully.

As there is no longer an active goal limiting the metadata, the server has resumed sending the available affordances and related composite resources.

Finally while these interactions have updated the canonical representation of the resource, the client was able to use the service by binding and interacting only with the vocabulary and protocol binding. At no point in this interaction did the client ever hardcode a value, or parse a URI in order to learn something about the resource or service, or execute the next step.
