Initial Interaction with the root resource.
```
GET http://exampleblog.org

200 OK
Preference-Applied: vocabulary=<http://example.org/blog/vocab/blog1>
Preference-Applied: protocol-binding=<http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
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

In this case, as the value for the reference portion `#posts` refers to a resource definition within the vocabulary, and the vocabulary defines this relation type to refer to a root resource `#posts`.

The client chooses to apply the same protocol-binding and uses it to resolve the vocabulary definition to a protocol operation, an HTTP method in this case. The client now has all the context it needs to know what when it interacts using this link, and just as important how it will to execute the request.
```
GET /posts
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#posts>
Prefer: hypermedia-api
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: curie=blog
```
In this request the client has chosen to use the higher version vocabulary the service supports, and has also elected for hypermedia-api metadata on the response in the vocabulary to be CURIEd with the value `blog` through it's use of the curie preference value.
```
200 OK
Preference-Applied: vocabulary=<http://example.org/blog/vocab/blog1.1#posts>
Preference-Applied: protocol-binding=<http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie=blog
Link: </posts/15>; rel="next blog#post/getPost",
      </posts/14>; rel="blog#post/getPost",
      </posts/13>; rel="blog#post/getPost",
      </posts/12>; rel="blog#post/getPost",
      </posts/11>; rel="blog#post/getPost",
      rel="self blog#addPost"
```
As you can see from the response, the server has elected to apply the curie preference, and every hypermedia-api metadata link within the vocabulary has been curied for ease of client binding, readability as well as a reduction in bytes on the wire.

The server has also returned a list of links related to the `/posts` resource, lets look at `</posts/15>; rel="next blog#post/getPost"` in particular. As you can see here this link uses both a registered IANA relation type `next` as well as a curied extension relation type `blog#post/getPost`. The client then uses the previously cached vocabulary and protocol-binding to resolve this safe retrieve resource affordance to a HTTP GET with no message content.
```
GET /posts/15
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#post/getPost>
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie=blog
```
The client simply executes the request on the protocol as the processing of the hypervocabulary and protocol-binding explicitly and unambiguously stipulate.
```
200 OK
Content-Type: application/json
Preference-Applied: vocabulary=<http://example.org/blog/vocab/blog1.1#post>
Preference-Applied: protocol-binding=<http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie=blog
Link: rel="self blog#post/addComment blog#post/reportPost blog#post/approvePost",
      rel="http://example.org/blog/goals/approveAndPublish"; type="application/goal+json",
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
Now that we have accessed a resource with a representation, we start to see some additional metadata from the server as links without URIs. These links refer to the `self` context, meaning they are links to the same resource they are attached to in this example, and what they do is express the current affordances available to the resource.

Lets look at the `rel="http://example.org/blog/goals/approveAndPublish"; type="application/goal+json"` link a little deeper. This link suggests there is a Goal of type `application/goal+json` defined at the extension relation type resolvable URI value of `http://example.org/blog/goals/approveAndPublish` related 

In contrast to many alternative approaches to hypermedia where the hypermedia is embedded as part of a unique media type, hypermedia-api utilizes existing robust web standards to apply this metadata _without_ requireing specialized content types for processing, or inadvertantly breaking the underlying protocol uniform interface. As you can see from the interactions thus far, the server has yet to reply to any of these requests with a body.

This has large impact on the utility of this methodology in a number of ways. This enhances cache reliability using the native cache management capabilities of HTTP by adding another means of comparison across media types to augment eviction of stale entries. As this is implemented using metadata only, it also means this methodology is truely backwards compatible. This system can be implemented on existing services in a way which _completely_ supports both hypermedia-api clients and the existing client base, and can be gradually phased in.


Start from arbitrary entry point:
```
GET http://exampleblog.org/posts/15
```

```
200 OK
Content-Type: application/json
Preference-Applied: vocabulary=<http://example.org/vocab/blog1#post>
Preference-Applied: protocol-binding=<http://example.org/bindings/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: profile
Preference-Applied: curie=blog
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


Here we encounter the follow your nose dilema. If it doesn't have the bound vocabulary you have no idea there is a publishPost affordance, how do you publish then.. try everything? The goal will curate your interaction.

```
GET http://example.org/blog/goals/approveAndPublish
Prefer: return=minimal
```

```
200 OK
Content-Type: application/json
{
  "start":"#post/approvePost"
}
```

```
POST /posts/15
Content-Type: application/json
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#post/approvePost>
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie=blog
Prefer: goal=http://example.org/blog/goals/approveAndPublish
{
  "message":"This is approved for publishing."
}
```

```
200 OK
Content-Type: application/json
Preference-Applied: vocabulary=<http://example.org/vocab/blog1#post>
Preference-Applied: protocol-binding=<http://example.org/bindings/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: profile
Preference-Applied: curie=blog
Preference-Applied: goal=http://example.org/blog/goals/approveAndPublish
Link: rel="next blog#post/publish"
{
  "postContents":"The best post ever!",
  "postPublishDate": null,
  "postApproval": true
}
```

```
POST /posts/15
Content-Type: application/json
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#post/publishPost>
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie=blog
Prefer: goal=http://example.org/blog/goals/approveAndPublish
{
  "message":"Check out this long share message, and our post here!",
  "shortMessage":"Check out our post!",
  "imageUrl="..."
}
```

```
200 OK
Content-Type: application/json
Preference-Applied: vocabulary=<http://example.org/blog/vocab/blog1.1#post>
Preference-Applied: protocol-binding=<http://example.org/bindings/http/http-standard>
Preference-Applied: hypermedia-api
Preference-Applied: curie=blog
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
