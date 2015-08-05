---
title: Known Issues
layout: default
prev_section: upgrade-to-a-newer-version
next_section: troubleshooting
category: Miscellaneous
permalink: v1_2_0-docs/known-issues/
---

## `GET /content/{id}` - doesn't appear to work with the article ID as URI

This is related to a spring bug/limitation when a URI is passed as a `@PathVariable`. It's also a bit ugly...
Though it seems we might be able to workaround as suggested [here](http://stackoverflow.com/questions/16332092/spring-mvc-pathvariable-with-dot-is-getting-truncated). We look at it again when we can.
