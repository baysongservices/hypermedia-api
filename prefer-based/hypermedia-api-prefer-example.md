Start from home:

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

GET /posts
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#posts>
Prefer: hypermedia-api
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: curie=blog

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

GET /posts/15
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#post/getPost>
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie=blog

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

Start from arbitrary:
GET http://exampleblog.org/posts/15

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


The follow your nose dilema. If it doesn't have the bound vocabulary you have no idea there is a publishPost affordance, how do you publish then.. try everything? The goal will curate your interaction.

GET http://example.org/blog/goals/approveAndPublish
Prefer: return=minimal

200 OK
{
  "start":"#post/approvePost"
}

POST /posts/15
Prefer: vocabulary=<http://example.org/blog/vocab/blog1.1#post/approvePost>
Prefer: protocol-binding=<http://example.org/bindings/http/http-standard>
Prefer: hypermedia-api
Prefer: curie=blog
Prefer: goal=http://example.org/blog/goals/approveAndPublish
{
  "message":"This is approved for publishing."
}

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


POST /posts/15
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

