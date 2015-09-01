---
title: CES API Documentation
layout: default
prev_section: components-overview
next_section: recommender
category: Technical Content
permalink: v1_0_0-docs/concept-extraction-service/
---

The Concept Extraction Service (CES) is a stand-alone service, which allows handling large amounts of parallel semantic annotation requests while providing high availability. Using data loaded from the GraphDB database as its dictionary of concepts (knowledge base), it recognises mentions of entities, as well as their relevance and algorithm’s confidence, in the text.

CES is integrated with GraphDB, which provides a live stream of dictionary updates to the service as they enter the knowledge base. Since concept extraction applications are usually based on a mixture of machine learning routines and rules, CES also provides a Re-training API, which allows both manual and automated re-training and evaluation of the underlying statistical models.

For more details, see the [Concept Extraction Services API Documentation](http://ontotext-ad.github.io/concept-extraction-docs/).
