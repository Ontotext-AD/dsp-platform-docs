---
title: Publishing Demo HowTo
layout: default
prev_section: virtualbox-demo
next_section: configuration-setups
category: Miscellaneous
permalink: v1_0_0-docs/publishing-demo-how-to/
---
## Service management
All services are executed using a Tomcat user.

* Service definitions: `/etc/init/tomcat-<service-id>.conf`
* Control service via: `service tomcat-<service-id> <stop/start/restart/status>`
* Tomcat installation: `/opt/tomcat`
* Tomcat instances: `/opt/tomcat/instances`


## GraphDB™
* In order to change GraphDB™ license, replace current license file: `/opt/tomcat/instances/graphdb-workbench/GRAPHDB_ENTERPRISE.license` with your license.
If you want to change the license name or location, please, set the `owlim-license` property to the appropriate license file in setenv file:
`/opt/tomcat/instances/graphdb-workbench/bin/setenv.sh`
* License location: `/opt/tomcat/instances/graphdb-workbench/GRAPHDB_ENTERPRISE.license`
* Data dir location: `/var/lib/graphdb/graphdb-workbench`

## Apache Solr™
* Solr home: `/opt/tomcat/instances/solr/solr-home`

## Logs
* Location: `/var/log/upstart/tomcat-<service-id>.log`

## Delete sample(all) documents

#### Using GraphDB™ Workbench
You can use GraphDB™ Workbench (`http://<ip_address>:<graphdb_port>/graphdb/sparql`) to do it manually by executing the following SPARQL query:

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
There are two ways to delete all documents using a REST client. 

##### First one is to do a POST request to `http://<ip_address>:<graphdb_port>/graphdb/repositories/pub/statements` with the following SPARQL query:

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

###### CURL
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

##### Second method is to run a DELETE request against `http://<ip_address>:<concept_api_port>/concept-api/documents`

###### CURL
```
curl -X DELETE http://<ip_address>:<concept_api_port>/concept-api/documents
```

## Create channels
See the [Concept Publishing Documentation - Create Channels section](http://ontotext-ad.github.io/concept-pub-docs/v1_1_0-docs/channels/)

## Perform concept extraction on document
See the [Concept Extraction Documentation - Annotating Content section](http://ontotext-ad.github.io/concept-extraction-docs/v1_3_1-docs/annotating_content/)

## Process document

The Publishing Demo has a dedicated service (the Processor) for annotating and storing content. The content is then available for browsing within the NOW Web UI.

The processor works with Ontotext-specific Generic Document Schema format, presenting documents either as XML or JSON.

In order to correctly visualize content in NOW Web UI, documents in Generic Schema Format need to have the following features: `title`, `summary`, `category`, `creationDate`, where `category` should match a category defined for a channel. Any other features can be used for additional channel filtering.

#### Example of a document in Generic Document Schema Format - XML

```
<?xml version="1.0" encoding="UTF-8"?>
<tns:document xmlns:tns="http://www.ontotext.com/DocumentSchema" xmlns="http://www.w3.org/1999/xhtml" id="http://www.natureworldnews.com/articles/20797/20160414/jailbreak-octopus-escaped-new-zealand-aquarium.htm">
  <tns:document-parts>
    <tns:feature-set>
      <tns:feature>
        <tns:name type="xs:string">title</tns:name>
        <tns:value type="xs:string">Jailbreak! Octopus Escapes From New Zealand Aquarium</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">summary</tns:name>
        <tns:value type="xs:string">A resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">source</tns:name>
        <tns:value type="xs:string">http://www.natureworldnews.com/articles/20797/20160414/jailbreak-octopus-escaped-new-zealand-aquarium.htm</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">category</tns:name>
        <tns:value type="xs:string">Science and Technology</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">creationDate</tns:name>
        <tns:value type="xs:string">2016-04-14T11:37:14Z</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">hasImage</tns:name>
        <tns:value type="xs:string">http://images.natureworldnews.com/data/images/full/23698/chicago-blackhawks-v-detroit-red-wings-game-six.jpg</tns:value>
      </tns:feature>
      <tns:feature>
        <tns:name type="xs:string">encoding</tns:name>
        <tns:value type="xs:string">UTF-8</tns:value>
      </tns:feature>
    </tns:feature-set>
    <tns:document-part id="1" part="TITLE">
      <tns:content>Jailbreak! Octopus Escapes From New Zealand Aquarium</tns:content>
    </tns:document-part>
    <tns:document-part id="2" part="BODY">
      <tns:content>A resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity.
A resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity.
Inky, an octopus about the size of the soccer ball, managed to find his way out to the ocean during the night leaving only traces of suction cap prints.
Upon surveying the scene of the crime and investigating Inky's prints, aquarium staff discovered that Inky managed to escape his tank by squeezing through a small gap at the top. He then waltzed eight feet across the hole, finding his way to a 164-foot long runoff pipe that led out into Hawke's Bay.
Keepers of the aquarium only found out about Inky's triumphant escaped the next day when they saw his roommate Blotchy alone in their tank.
In a report from Radio New Zealand , Rob Yarrall, manager of the National Aquarium, said that they searched the pipes where Inky used to escape, but he was already gone.
"He managed to make his way to one of the drain holes which go back to the ocean and off he went," shared Yarrall. "And he didn't even leave us a message."
The great escape actually happened a few months ago, but it was only now that words spread out, according to CBS News . Inky has been living in the aquarium since it was turned over by a fisherman who caught him in a clay pot in 2014.
He was named Inky, after the suggestion of Gerry Townsend won the "name the octopus" competition organized by the Napier City Council.
In the city's press release in 2014, Kerry Hewitt, curator of the exhibits, have mentioned that Inky was getting used in his new aquarium life but they needed to amuse him every now and then, or he would get bored.
Boredom must be the motivation of Inky to leave his caged life for the vast ocean. At present, there are still no words of Inky's whereabouts.</tns:content>
    </tns:document-part>
  </tns:document-parts>
  <tns:annotation-sets />
</tns:document>
```

#### Example of a document in Generic Document Schema Format - JSON

```
{
  "id" : "http://www.natureworldnews.com/articles/20797/20160414/jailbreak-octopus-escaped-new-zealand-aquarium.htm",
  "feature-set" : [ ],
  "document-parts" : {
    "feature-set" : [ {
      "name" : {
        "type" : "XS_STRING",
        "name" : "title"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "Jailbreak! Octopus Escapes From New Zealand Aquarium"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "summary"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "A resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "source"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "http://www.natureworldnews.com/articles/20797/20160414/jailbreak-octopus-escaped-new-zealand-aquarium.htm"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "category"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "Science and Technology"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "creationDate"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "2016-04-14T11:37:14Z"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "hasImage"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "http://images.natureworldnews.com/data/images/full/23698/chicago-blackhawks-v-detroit-red-wings-game-six.jpg"
      }
    }, {
      "name" : {
        "type" : "XS_STRING",
        "name" : "encoding"
      },
      "value" : {
        "type" : "XS_STRING",
        "lang" : null,
        "value" : "UTF-8"
      }
    } ],
    "document-part" : [ {
      "feature-set" : [ ],
      "id" : "1",
      "part" : "TITLE",
      "content" : {
        "text" : "Jailbreak! Octopus Escapes From New Zealand Aquarium",
        "node" : [ ]
      }
    }, {
      "feature-set" : [ ],
      "id" : "2",
      "part" : "BODY",
      "content" : {
        "text" : "A resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity.\nA resident octopus of the National Aquarium of New Zealand has made the recent headline due to its cunning and brilliant escape from captivity.\nInky, an octopus about the size of the soccer ball, managed to find his way out to the ocean during the night leaving only traces of suction cap prints.\nUpon surveying the scene of the crime and investigating Inky's prints, aquarium staff discovered that Inky managed to escape his tank by squeezing through a small gap at the top. He then waltzed eight feet across the hole, finding his way to a 164-foot long runoff pipe that led out into Hawke's Bay.\nKeepers of the aquarium only found out about Inky's triumphant escaped the next day when they saw his roommate Blotchy alone in their tank.\nIn a report from Radio New Zealand , Rob Yarrall, manager of the National Aquarium, said that they searched the pipes where Inky used to escape, but he was already gone.\n\"He managed to make his way to one of the drain holes which go back to the ocean and off he went,\" shared Yarrall. \"And he didn't even leave us a message.\"\nThe great escape actually happened a few months ago, but it was only now that words spread out, according to CBS News . Inky has been living in the aquarium since it was turned over by a fisherman who caught him in a clay pot in 2014.\nHe was named Inky, after the suggestion of Gerry Townsend won the \"name the octopus\" competition organized by the Napier City Council.\nIn the city's press release in 2014, Kerry Hewitt, curator of the exhibits, have mentioned that Inky was getting used in his new aquarium life but they needed to amuse him every now and then, or he would get bored.\nBoredom must be the motivation of Inky to leave his caged life for the vast ocean. At present, there are still no words of Inky's whereabouts.\n",
        "node" : [ ]
      }
    } ]
  },
  "annotation-sets" : [ ]
}
```

#### Processing a document through the Processor

Any REST client can be used to prepare this PUT request to the Processor. The document content should be set as request body, and `Content-Type` header should be set according to the selected Generic Document Schema format (JSON or XML):
- JSON: `application/vnd.ontotext.ces.document+json`
- XML: `application/vnd.ontotext.ces.document+xml`

A sample CURL command (assume that the document above is stored in a file named `octopus.(json|xml)`:

```
> curl -X PUT -H "Content-Type: application/vnd.ontotext.ces.document+json" --data @octopus.json http://localhost:19098/processor/process

> curl -X PUT -H "Content-Type: application/vnd.ontotext.ces.document+xml" --data @octopus.xml http://localhost:19098/processor/process
```

The return value represents the Unique Resource Identifier (URI) under which the document was stored in the database. Any URL will be used directly as URI, while any non-URI formatted identifiers will be prefixed with a default Ontotext namespace.

## Working with GraphDB™
See the [GraphDB™ Documentation](http://graphdb.ontotext.com/).
