---
title: Get Behavioural Recommendations
layout: default
prev_section: content-based-recommendation
next_section: advanced-recommendation-parameters
category: HowTo's
permalink: v1_2_0-docs/get-behavioural-recommendations/
---
<div class="info-badge">All request URLs in the document are relative to the application root. E.g. If the web application is deployed at http://10.0.0.10:8080/api and this document says to request <code>/recommend/behavioural</code>, the full URL would be http://10.0.0.10:8080/api/recommend/behavioural.</div>

Behavioural recommendations are based on the user profile. They combine data on user interests with a set of configuration parameters that define weight for each factor.

Here is a simple example.
We have two categories of articles:

- Articles with IDs *doc-garden-1*, *doc-garden-2*, etc. are related to gardens, garden tools, flowers, etc.
- Articles with IDs *doc-travel-1*, *doc-travel-2* etc. are related to geographic places. They contain mainly names of places, transportation terms, etc.

In this example, the articles of the second category are more visited - they have four times more reads on average than the articles on gardening.

We have a user with ID *test-user-1*. This user has read only two articles: *doc-garden-1* and *doc-garden-3*.

You can get recommended content for this user by calling:
<code>
GET /recommend/behavioural/?key=api_key&userid=test-user-1&count=4&sort=rel
</code>

This is the result:
<pre><code>
{
  "version": "Ontotext Recommendations API",
  "type": "RECOMMENDATION",
  "status": "OK",
  "articles":
  [
    {
      "id": "doc-travel-1",
      "title": "Hello London!",
      "score": 0.6092376112937927,
      "published": "2014-10-24T17:00:37Z",
      "url": "http://example.org/hello-london-article-1",
      "popularity": 12
    },
    {
      "id": "doc-travel-2",
      "title": "Hello London Again!",
      "score": 0.5170392990112305,
      "published": "2014-10-24T17:00:37Z",
      "url": "http://example.org/hello-london-article-2",
      "popularity": 11
    },
    {
      "id": "doc-garden-2",
      "title": "My Spring Garden!",
      "score": 0.47985148429870605,
      "published": "2014-11-01T17:00:37Z",
      "url": "http://example.org/my-garden-2",
      "popularity": 9
    },
    {
      "id": "doc-travel-3",
      "title": "Hello Paris!",
      "score": 0.4229651391506195,
      "published": "2014-10-24T17:00:37Z",
      "url": "http://example.org/hello-paris-article-1",
      "popularity": 11
    }
  ]
}
</code></pre>

Although the user is interested mostly in gardening, the travel articles are on the top of the list. Higher relevance score is calculated for them because they are more popular - that is, they have been visited more often.

The popularity weight may be lowered by POST-ing the request and adding JSON-encoded weight parameter such as:

`{ "beh.weight.popularity": 0.2 }`


`Content-type` of the request will be `application/json`. See Advanced recommendation parameters for more information.

This changes the weight of popularity to 0.2 (down from the default 0.9) and the result is:

<pre><code>
{
   "version": "Ontotext Recommendations API",
   "type": "RECOMMENDATION",
   "status": "OK",
   "articles": [
    {
      "id": "doc-garden-2",
      "title": "My Spring Garden!",
      "score": 0.9111301898956299,
      "published": "2014-11-01T17:00:37Z",
      "url": "http://example.org/my-garden-2",
      "popularity": 9
    },
    {
      "id": "doc-garden-4",
      "title": "Work in the Garden!",
      "score": 0.7094958424568176,
      "published": "2014-11-01T17:00:37Z",
      "url": "http://example.org/my-garden-4",
      "popularity": 8
    },
    {
      "id": "doc-travel-1",
      "title": "Hello London!",
      "score": 0.6092376112937927,
      "published": "2014-10-24T17:00:37Z",
      "url": "http://example.org/hello-london-article-1",
      "popularity": 12
    },
    {
      "id": "doc-travel-2",
      "title": "Hello London Again!",
      "score": 0.5170392990112305,
      "published": "2014-10-24T17:00:37Z",
      "url": "http://example.org/hello-london-article-2",
      "popularity": 11
    }
  ]
}
</code></pre>

Note how the relevance scores change when popularity is not that important for it.

You can also combine a user profile with the content he/she is currently looking at.

Some possible scenarios could be:

- The user is reading a news article and you want to recommend more articles.
- The user may be reviewing a CV and you want to recommend suitable job descriptions.

The way to add this contextual information is via the *contentid* parameter:

<code>
GET /recommend/behavioural/?key=api_key&amp;userid=test-user-1&amp;count=4&amp;sort=rel&amp;contentid=doc-garden-1
</code>


Now, as both the user profile and the related content is about gardening, we get:

<pre><code>
{
    "version": "Ontotext Recommendations API",
    "type": "RECOMMENDATION",
    "status": "OK",
    "articles":
  [
    {
      "id": "doc-garden-2",
      "title": "My Spring Garden!",
      "score": 0.8721830248832703,
      "published": "2014-11-01T17:00:37Z",
      "url": "http://example.org/my-garden-2",
      "popularity": 11
    },
    {
      "id": "doc-garden-4",
      "title": "Work in the Garden!",
      "score": 0.2629146873950958,
      "published": "2014-11-01T17:00:37Z",
      "url": "http://example.org/my-garden-4",
      "popularity": 8
    }
  ]
}
</code></pre>

## Parameters

`/recommend/behavioural` has the following query parameters:

- `userid` (required) - the ID of the user to whom to return the recommendations;
- `contentid` (optional) - the existing article identifier. If passed, the recommendations will be computed with respect to this article and the user history;
- `count` (optional, default = 10) - the maximum number of articles to return;
- `sort` (optional, default = rel) - the sorting method: valid arguments are "pop"(popularity), "rel"(relevancy), and "date";
- `recency` (optional, default = <empty>) - limits the age of the articles returned by the recommendation. The supported values for recency are "amonthago", "aweekago", and integers representing the maximum number of days ago. For example, recency=5 will only return articles newer than five days ago. If omitted, no age filter is applied.
