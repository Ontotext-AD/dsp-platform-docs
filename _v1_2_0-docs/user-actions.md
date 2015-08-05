---
title: User Actions
layout: default
prev_section: uploading-articles
next_section: faq
category: HowTo's
permalink: v1_2_0-docs/user-actions/
---

<div class="info-badge">All example request URLs in this section are relative to the application root. For instance, if the web application is deployed at http://10.0.0.10:8080/api and the example says to request <code>/user</code>, the full URL would be http://10.0.0.10:8080/api/user.</div>

### Storing user actions

User actions are usually tracked by an external publication system. Currently we support one kind of action, which may be considered equivalent to "User has read" or "User is interested in". Depending on the content specifics and the audience, different systems may opt for different schema of registering user actions. For example, a user may be considered interested in an article if they commented on it or scrolled to the second screen, or a combination of both.

The relation is established by the following call:

```
POST /user?key=api_key&userid=user_id&contentid=existing_article_id
```
where "recommend" is the name of the installed recommendation application.

When the relation is established, a similar result is returned:

<pre><code>
{
  "version": "Ontotext Recommendations API",
  "type": "SUCCESS",
  "status": "OK",
  "message": "User updated: id = user-1"
}
</code></pre>

Query parameters:

* `userid` (required) - the user reading an article;
* `contentid` (required) - the article that the user is reading;
* `timestamp` (optional) - the time when the action occurred. If not specified, the action occurs "now".

### Obtaining a list of user reads

This is done by the following call:

```
GET recommend/user/?key=api_key&userid=user_id
```

The result is as follows:

<pre><code>
{
  "version": "Ontotext Recommendations API",
  "type": "USER_INFO",
  "status": "OK",
  "response":
  {
      "numberOfReads": 4,
      "articles":
      [
          {
              "id": "doc-en-1",
              "title": "Hello London!",
              "score": 0.8853250741958618,
              "published": "2014-10-14T17:00:37Z",
              "url": "http://example.org/hello-london-article-1",
              "popularity": 3
          },
          {
              "id": "doc-en-2",
              "title": "Hello London Again!",
              "score": 0.8853250741958618,
              "published": "2014-10-15T17:00:37Z",
              "url": "http://example.org/hello-london-article-2",
              "popularity": 1
          }
      ]
  }
}
</code></pre>

The user with ID *user-1* has read the article with ID *doc-en-1* three times and the article with ID *doc-en-2* once.

The result is `numberOfReads`=4 for the user as well as `popularity`=3 and `popularity`=1 for the two articles. Popularity is global as it does not depend on the given user.

### Obtaining a count of user reads

This is done by the following calls:

<pre><code>
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=aweekago
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=aweekago&amp;to=2
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=10&amp;to=2
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=10
</code></pre>

The `to` parameter is optional and defaults to present time.
Both `from` and `to` can take the following values:

* amonthago;
* aweekago;
* integers representing number of days.

A single number is returned as a result. Note that if a user has read the same article twice, it would be considered as two reads.

Let's say you want to build a chart showing your news site activity on a daily basis for the last 10 days.

You could use this method to get reads for each day and plot them in а chart:

<pre><code>
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=30&amp;to=29
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=29&amp;to=28
GET /usagestats/userreads/?key=&lt;api_key&gt;&amp;from=28&amp;to=27
...
</code></pre>


### Obtaining statistics on most active users

This method uses the following call:

```
GET /usagestats/mostactiveusers/?key=api_key&from=aweekago&count=5
```

It has the same `from` and `to` parameters as `usagestats/userreads` and returns the top users with most reads. The `count` parameter shows the size of the list to be returned.

The method has the following optional parameters:

* `minActivity`
* `maxActivity`
* `uniqueArticles` - true/false, counts only the unique articles.
