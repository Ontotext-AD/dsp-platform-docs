---
title: Publishing Demo HowTo
layout: default
prev_section: virtualbox-demo
next_section: configuration-setups
category: Miscellaneous
permalink: v1_0_0-docs/publishing-demo-how-to/
---
## Service management
All services are executed using tomcat user.

* Service definitions: `/etc/init/tomcat-<service-id>.conf`
* Control service via: `service tomcat-<service-id> <stop/start/restart/status>`
* Tomcat installation: `/opt/tomcat`
* Tomcat instances: `/opt/tomcat/instances`


## GraphDB™
* License location: `/opt/tomcat/instances/graphdb-workbench/GRAPHDB_ENTERPRISE.license`
* Data dir location: `/var/lib/graphdb/graphdb-workbench`

## Apache Solr™
* Solr home: `/opt/tomcat/instances/solr/solr-home`

## Logs
* Location: `/var/log/upstart/tomcat-<service-id>.log`

## Delete sample(all) documents

#### Manual
You can use GraphDB™ Workbench (`http://<ip_address>:<graphdb_port>/graphdb/sparql`) do to it manually by executing following SPARQL query:

```
PREFIX pub-old: <http://ontology.ontotext.com/publishing#>

delete { ?s ?p ?o }
where {

    { ?s a pub-old:Document }
    union
    { ?s a pub-old:TextMention }
    union
    { ?s a pub-old:Image }
    union
    { ?s a pub-old:DocumentFeature }

    ?s ?p ?o .
}
```

#### REST client
POST request to http://<ip_address>:<graphdb_port>/graphdb/repositories/pub/statements with the following SPARQL query:

```
PREFIX pub-old: <http://ontology.ontotext.com/publishing#>

delete { ?s ?p ?o }
where {

    { ?s a pub-old:Document }
    union
    { ?s a pub-old:TextMention }
    union
    { ?s a pub-old:Image }
    union
    { ?s a pub-old:DocumentFeature }

    ?s ?p ?o .
}
```


#### CURL

```
curl -X POST http://<ip_address>:<graphdb_port>/graphdb/repositories/pub/statements --data-urlencode 'update=
PREFIX pub-old: <http://ontology.ontotext.com/publishing#>

delete { ?s ?p ?o }
where {

    { ?s a pub-old:Document }
    union
    { ?s a pub-old:TextMention }
    union
    { ?s a pub-old:Image }
    union
    { ?s a pub-old:DocumentFeature }

    ?s ?p ?o .
}'
```

## Create channels
See the [Concept Publishing Documentation - Create Channels section](http://ontotext-ad.github.io/concept-extraction-docs/v1_3_1-docs/annotating_content/)

## Perform concept extraction on document
See the [Concept Extraction Documentation - Annotating Content section](http://ontotext-ad.github.io/concept-extraction-docs/v1_3_1-docs/annotating_content/)

## Process document

## Working with GraphDB™
See the [GraphDB™ Documentation](http://graphdb.ontotext.com/).
