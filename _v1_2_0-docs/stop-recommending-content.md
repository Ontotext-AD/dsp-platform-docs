---
title: Stop recommending a piece of content
layout: default
prev_section: advanced-recommendation-parameters
next_section: uploading-articles
category: HowTo's
permalink: v1_2_0-docs/stop-recommending-content/
---
In many systems, documents are not valid forever. An article may be deleted or an advertisement may expire.

In such cases it would be appropriate to stop recommending this piece of content.

```
DELETE /recommend/content/existing_doc_id/?key=api_key
```
Please, note that deleting an article does not impact the read statistics and user profiles.

That is, if a user has read just one article about heavy machinery and this article gets deleted, then the user will still have one read operation and their profile would relate to other content about heavy machinery.
