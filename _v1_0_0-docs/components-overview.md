---
title: Components Overview
layout: default
prev_section: technical-diagram
next_section: concept-extraction-service
category: Technical Content
permalink: v1_0_0-docs/components-overview/
---
## Overview

The DSP platform architecture is oriented towards availability and consistency. It comprises databases (proprietary - GraphDB - and open source - Apache Cassandra and Apache Solr), core computing services (Concept Extraction Service (CES) and Recommendation Engine) and a set of (micro) services - APIs on top of the databases and computing components.

## Feeder

The RSS Feeder API is a crawling tool for managing feeds. (link to API docs)

## Processor

The Content Processing API manages and controls the processing and reprocessing of the documents. (link to API docs)

## Content API

The Content API manages articles, documents and corpora. The operations include: add, store, update, retrieve and delete. (link to API docs)

## Apache Cassandra

<a href="{{ site.baseurl }}/v1_0_0-docs/cassandra">Apache Cassandra</a> is an open source distributed content management system, designed to handle large amounts of data across many commodity servers. It stores DSP Platform's content, users' behaviour and users' scientific specialization.

## Concept Extraction API

<a href="{{ site.baseurl }}/v1_0_0-docs/concept-extraction-service">The Concept Extraction Service (CES)</a> is a stand-alone service, which allows handling large amounts of parallel semantic annotation requests while providing high availability. Using data loaded from the GraphDB database as its dictionary of concepts (knowledge base), it recognises mentions of entities, as well as their relevance and algorithm’s confidence, in the text. CES is integrated with GraphDB™, which provides a live stream of dictionary updates to the service as they enter the knowledge base.  (link to API docs)

## CES Coordinator

The CES Coordinator distributes the annotation requests among the CES Workers and coordinates the dictionary updates. (link to API docs)

## Concept API

<a href="{{ site.baseurl }}/v1_0_0-docs/concept-api">The Concept API</a> is responsible for operations with RDF Objects such as Concepts (or named entities), Documents, Channels, Relations and Trends. (link to API docs)

## GraphDB

<a href="{{ site.baseurl }}/v1_0_0-docs/graphdb">GraphDB™</a> is a leading RDF graph database built on Ontology Web Language standards. It stores data in the form of atomic facts expressed as subjects, predicates and objects, which have relationships to other facts, and performs semantic inferencing at scale. It keeps the DSP Platform's consolidated entity profiles, bibliographic records and subject terms. (link to Graphdb Workbench)

## Apache Solr

<a href="{{ site.baseurl }}/v1_0_0-docs/apache-solr">Apache Solr</a> is an open source enterprise search platform built on Apache Lucene™. Its major features include full-text search, hit highlighting, faceted search, real-time indexing, dynamic clustering, database integration, NoSQL features and rich document (e.g., Word, PDF) handling. It manages the DSP Platform's Suggest, Search and Recommend indices.

## Search API

The Search API provides suggestions, keyword search, concept search, hybrid search, facets and custom search endpoints. (link to API docs)

## Recommender

<a href="{{ site.baseurl }}/v1_0_0-docs/recommender">The Recommendation Engine</a> takes content and user actions as input and provides recommended content based on contextual similarity and user profiles in JSON format. It also has a RESTful interface which allows tuning the complex recommendation algorithm at runtime, by setting different weights for the user behaviour components, contextual boosts, and recency relevance decay.

In a nutshell, the Recommendation Engine logs user activity, serves contextual recommendations, maintains user profiles, serves behaviour-driven recommendations, user activity insights and user clustering. (link to API docs)

## News on the Web UI

News on the Web (NOW) is a solution built on top of the DSP Platform, which aims to show its full palette of functionalities. Its powerful backend allows dynamic content aggregation, improved navigation between linked content, complex searches, dynamic creation of custom content streams, content reuse and repurposing, user-tailored recommendations, etc. Check it out [NOW](http://now.ontotext.com) - a broad and in-depth perspective of the world today.

## Ontotext Tagging Service UI

The [Ontotext Tagging Service](http.tag.ontotext.com) is a tagging tool, which provides live experience of the text analytics taking place behind the scene of the DSP Platform. Based on data from [DBpedia](http://wiki.dbpedia.org/), [Wikidata](http://wikidata.org), and [GeoNames](http://www.geonames.org), and smart machine learning algorithms, it recognises mentions of entities such as Person, Organisation, Location, keyphrases, and relationships between them, as well as their relevance and confidence to the text.
