---
layout: default
title: Content-based Recommendation
prev_section: installation
next_section: get-behavioural-recommendations
category: HowTo's
permalink: v1_2_0-docs/content-based-recommendation/
---

<div class="info-badge">All example request URLs on this page are relative to the application root. For instance, if the web application is deployed at http://10.0.0.10:8080/api and the example says to request <code>/recommend/contextual</code>, the full URL would be http://10.0.0.10:8080/api/recommend/contextual.</div>

To receive content-based recommendations for existing article, do the request:

`
GET /recommend/contextual?contentid=existing_id
`

and it will return the following (example) response:

<pre><code>
{
  "version": "Ontotext Recommendations API",
  "type": "RECOMMENDATION",
  "status": "OK",
  "articles": [
  {
    "id": "test-document-1",
    "title": "Example article 1 title",
    "score": 0.1532602161169052,
    "published": "2014-03-13T15:43:06Z",
    "url": "http://example.org/test-document-1",
    "popularity": 12
    }, {
      "id": "test-document-1",
      "title": "Example article 2 title",
      "score": 0.1324324723490948,
      "published": "2014-03-13T15:43:06Z",
      "url": "http://example.org/test-document-2",
      "popularity": 8
    }
    // possibly more documents
    ]
  }
</code></pre>

## Parameters

`/recommend/contextual` has the following query parameters:

- `contentid` (required) - existing article identifier;
- `count` (optional, default = 10) - maximum number of articles to return;
- `sort` (optional, default = rel) - sorting method, valid arguments are "pop"(popularity), "rel"(relevancy) and "date";
- `recency` (optional, default = <empty>) - limits the age of the articles returned by the recommendation. The supported values for recency are "amonthago", "aweekago", and the integers representing the maximum number of days ago. For example, recency=5 will only return articles newer than five days ago. If omitted, no age filter is applied.
