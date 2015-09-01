---
title: Recommendation Engine Documentation
layout: default
prev_section: concept-extraction-service
next_section: concept-api
category: Technical Content
permalink: v1_0_0-docs/recommender/
---
The DSP platform includes a recommendations engine, which can be used to serve relevant content and ads, either based solely on context, or on user behaviour, or both. The behavioural aspect of the engine feeds from the semantic fingerprints that people leave when they browse content on the web. The system also incorporates a time component, responsible for the so called recency boost. It can be configured according to the use case - a news website usually tends to require fresh content in the top results, whereas a librarian tool opts for the most relevant matches.

The Recommendation engine takes content and user actions as input and provides recommended content based on contextual similarity and user profiles in JSON format. It also provides RESTful interface to tune the complex recommendation algorithm at runtime, by setting different weights for the user behaviour components, contextual boosts, and recency relevance decay.

For more details, see the[Recommendation Engine Documentation](http://ontotext-ad.github.io/recommend-pub-docs/).
