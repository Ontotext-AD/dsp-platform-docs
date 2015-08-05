---
title: Installation
layout: default
prev_section: quick-start
next_section: content-based-recommendations
category: Getting Started
permalink: v1_2_0-docs/installation/
---

## General prerequisites

* [Java 7 or later](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Apache Cassandra 2.x.x](http://cassandra.apache.org/)
* [Apache Solr 4.7.0](http://lucene.apache.org/solr/), Note that more recent versions of Solr are known to have an issue with the popularity index field
* Credentials to our Nexus publishing repos

## Standard (and easy) setup

This guide is the bare minimum that needs to be done in order to setup a working system. Scalability and high availability are addressed separately.

1. Install and run Cassandra.
2. [Install Solr 4.7.0](https://cwiki.apache.org/confluence/display/solr/Installing+Solr).

	2.1. In your `solr.solr.home` directory create a new directory named `recommend` and unpack the contents of [`recommendation-solr-core-1.x.y-bin.zip`](http://maven.ontotext.com/content/repositories/publishing-releases/com/ontotext/recommend/recommendations-solr-core/1.2.0/recommendations-solr-core-1.2.0-bin.zip) there.

	2.2. Add the Java system property `cassandra.host` to a comma-separated list of hosts running Cassandra set-up in step 1. For example, if Cassandra is running on 192.168.1.1, then add `-Dcassandra.host=192.168.1.1` to the Java command starting Solr.

	2.3. Start Solr.

3. Deploy [recommendations-web.war](http://maven.ontotext.com/content/repositories/publishing-releases/com/ontotext/recommend/recommendations-web/1.2.0/recommendations-web-1.2.0.war) to an application container of your choice. We usually use [Apache Tomcat](http://tomcat.apache.org/tomcat-7.0-doc/appdev/installation.html). Some Java system properties need to be set on the container (in the case of Tomcat, they should go in `bin/setenv.sh` or `bin/setenv.bat`, depending on your platform):
	
	2.1. `-Dcom.ontotext.recommend.solr=http://<solrhost>:<solrport>/<solrpath>/recommend` where _solrhost_ and _solrport_ point to the box running Solr set up in step 2.; and _solrpath_ is the Solr web app name, usually just `solr`.

	2.2. `-Dcassandra.host=<cassandra-host>`, same as in the Solr setup.
