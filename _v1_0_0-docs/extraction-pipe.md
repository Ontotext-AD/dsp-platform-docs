---
title: Extraction Pipeline
layout: default
prev_section: data-behind
next_section: brief-ux
category: Getting Started
permalink: v1_0_0-docs/extraction-pipe/
---
## Overview
The Ontotext Concept Extraction Pipeline is a tool for automated analysis of large volumes of textual content, through which mentions of specific concepts and relationships between them can be discovered and represented in a machine-processable format. It enriches the content aggregated from various news sources based on data from [DBpedia](wiki.dbpedia.org/), [WikiData](http://wikidata.org/), [GeoNames](http://www.geonames.org/) and others. The pipeline utilizes  a wide variety of linguistic and algorithmic resources -- such as semantic gazetteers, rules triggered by particular linguistic patterns, various statistical models for classification and sequence tagging trained against human-annotated corpora, etc.. These resources are chained together in a sequence (hence the name pipeline) that provides content enrichment of gradually increasing complexity, where each phase builds upon the results produced by the one that precedes it.The extraction pipe recognises mentions of entities such as Person,  Organisation, and Location, and links them if possible to a particular Knowledge Base ([DBpedia](wiki.dbpedia.org/), [WikiData](http://wikidata.org/), [GeoNames](http://www.geonames.org/) etc.) . it also tags the particularly relevant nounphrases, called keyphrases thus allowing a fast grasp into the topic of the document. Furthermore it detects  relationships between the extracted entities, as well as their relevance and confidence to the text.   


**Sketch of the pipeline:**


- After the initial preprocessing phase (content cleanup, tokenization, sentence-splitting, morphological analysis), the pipeline performs annotation via lexicons and rules in order to extract some generic concepts like numbers, measures and metrics. 
 
- In a further step the **keyphrases**  are extracted. These are  noun phrases that are particularly relevant to the document and as such tend to describe its topic in a concise form. 
 
- Similarly as the keyphrases, the **named entities (NE)** mentioned in the document provide valuable information on the subject of the document.   They are extracted in the NE  discovery and linking phase of the pipeline matching the contents of the knowledge base  against the document content using semantic Gazetteers  and resolving any ambiguity that may occur by means of a machine learning word sense disambiguation algorithm.
 
- Since not all NE in the document are present in the knowledge base we also perform **novel named entity recognition** the discovery and handling of named entities previously unavailable in the knowledge base. 
 
- In a final step the pipeline extracts **relationships** that may exist between the discovered named entities. 
 
- For all extracted keyphrases and NE, that may or may not be present in the knowledge base, the pipeline assesses the degree of importance (**relevance score**) of the concepts in the context of the article where they were discovered, as well as the sentiment of their immediate context.
 



We continue with a  high-level description of the phases (sub-pipelines) involved in the concept extraction pipeline:


## Concept extraction pipeline phases

<img src="{{ site.baseurl }}/img/Tagasauris_pipeline.png" alt="Pipeline" style="width:400px;height:800px; margin: 0 auto">

### Preprocessing phase

During this phase, a very basic pre-processing takes place:

- Content clean-up rules (normalization of quotation marks, apostrophes, etc., provision of sentence splits at paragraph boundaries, etc.);
- Tokenization (ANNIE English Tokenizer PR);
- Sentence splitting (ANNIE Sentence Splitter PR);
- Stemming (Snowball Stemmer PR);
- POS-tagging (OpenNLP English POS Tagger PR);
- Chunking (OpenNLP English Chunker PR);
- Lemmatization (Gate Morpholocial Analyser PR).

Most of the processing resources used during this phase are part of the GATE distribution. However, several additional rules that improve the output and facilitate the subsequent tasks are also provided:

- Rules inserting additional splits on newline characters and document elements available through the “Original markups” annotation set;
- Rules modifying noun phrase chunks in order to improve the extraction of keyphrase candidates;
- Rules generating canonical forms for such noun phrases.

### Keyphrase extraction phase

This phase contains logic that generates keyphrase candidates, assigns relevance scores to the candidates, and classifies them into positive or negative instances via a heuristic ranking scheme that uses the term frequency, inverse document frequency and the offset of a given mention from the beginning of the document.

### Gazetteer-based enrichment phase

At this stage, the document content is enriched by means of a semantic gazetteer. This component annotates the character sequences that resemble mentions of concepts available in the knowledge base. The gazetteer lookups are not part of the extraction results per se, but they are used as a means of spotting "candidates" that facilitate the statistical models that produce the final set of entities.

All sub-pipelines are responsible for the execution of a single gazetteer (ANNIE Gazetteer, LD Gazetteer), and the consequent transfer of features from the gazetteer-generated annotations to other annotation types involved in the named entity recognition and disambiguation phases.

The “LD Gazetteer” component's cache is populated with instances from the latest versions of [DBpedia](http://wiki.dbpedia.org/) and [Wikidata](http://wikidata.org) datasets. In addition, there are links to [Geonames](http://www.geonames.org) instances and mappings to [Schema.org](http://schema.org) and [Umbel](http://umbel.org/) classes, which increase the accuracy of the type detection for named entities.

### Named entity recognition and disambiguation phase

#### Named entity disambiguation

Using the named entity candidates discovered by the LD Gazetteer, and given the specific article context, this phase makes use of a specialized classifier to assign a "positive" or "negative" label to each candidate. As a result, the ambiguity associated with the complete set of gazetteer lookups is eliminated – at most one named entity remains per document offset, and the redundant ("negative") named entity candidates are filtered.

The disambiguation mechanism relies on some additional metadata derived from Wikipedia:

- A short textual description of each candidate (based on DBPedia and Freebase abstracts);
- A set of URIs representing the entities that appear in the full DBpedia article for each candidate.

Various similarity scores are computed by a specialized processing resource that accesses the metadata (stored in indices prepared especially for this task) and evaluates the correspondence between the candidate and the context (the article content and the content stored in the aforementioned indices). The final classification is conducted by a separate processing resource, based on these pre-computed scores and some additional features derived via rules from the local context.

Currently, the component supports disambiguation of named entities belonging to either of the following classes:

* Major classes:
  * Person - individuals in the dataset;
  * Location - various places such as geographical regions, natural locations, public or commercial places, buildings, etc. All countries are also marked as Location;
  * Organization - profit and non-profit organizations, sports teams, military alliances, government institutions;

* Additional classes:
  * Event - temporary or scheduled events such as festivals, competitions, gatherings, concerts, etc.;
  * Work - intellectual or artistic creation;
  * Animal - multicellular eukaryotic organisms;
  * Plant - multicellular eukaryotic organisms of the kingdom Plantae;

#### Discovery of novel named entities

Named entities not available in the “LD Gazetteer” component's cache are not recognized, and therefore not handled by the above-described disambiguation mechanism. The recognition of novel entities belonging to the “Person”, “Location” and “Organization” classes is handled by the PLO Tagger processing resource, which compensates for the lack of perfect coverage by the classifier-based tagging approach.

The results extracted during *Named entity disambiguation* and *Discovery of novel named entities* are combined in a way that eliminates the overlapping between annotations produced by the disambiguation classifier and the PLO tagger components. The implemented logic guarantees that the disambiguated entities with a meaningful URI are preferred to the anonymous entities discovered by the PLO tagger, whenever possible.

### Generic entity extraction phase

This phase implements a rule-based enrichment with entities of generic type. Currently, these include:

- Dates (normalization logic is provided as well);
- Numbers;
- Money;
- Percentages;
- Measurements.

### Result consolidation phase

This phase contains rules that take into account all entity types, discovered during the preceding phases in order to refine the extraction results. At its end, instance URIs are generated and assigned to entities that have no such identifiers.

This phase involves the execution of the “Orthomatcher” processing resource, which deals with the discovery of orthographical variations of the labels and aliases of people, location and organization entities. Subsequently, the trusted URIs of disambiguated entities are propagated to novel aliases annotated by the PLO tagger, based on linkage done by the “Orthomatcher” component. At the end of the phase, URIs are generated for the entities that have not been assigned a valid URI during the previous phases.

### Relation extraction phase

This phase conducts rule-based extraction of various relationships between the atomic entities discovered at the preceding stages. Currently, the following types of relations are supported:

* RelationAcquisition
* RelationOrganizationAbbreviation
* RelationOrganizationAffiliatedWithOrganization / RelationSubOrganization
* RelationOrganizationHasCompetitor
* RelationOrganizationHasCustomer
* RelationOrganizationLocation
* RelationOrganizationQuotation
* RelationPersonHasRoleInLocation
* RelationPersonHasRoleInOrganization
* RelationPersonHasRoleWithinOrganizationInLocation
* RelationPersonQuotation
* RelationPersonRole

### Clean-up phase

During this phase, a final clean-up takes place, through which redundant intermediate annotations are removed, the document readability is improved, and the annotation sets are reorganized in order to assume the structure expected by the components that process the documents after the completion of the concept extraction pipeline.

## Types of extracted information

Text can be analyzed by the pipeline at multiple levels including:

* Topic extraction - recognizing important words and phrases in the text;
* Named entity recognition (NER) - extracting people, organization, location, time, amounts of money, etc.;
* Named entity linking - extracting well-defined concepts available in a pre-defined taxonomy (and stored in the knowledge base);
* Extraction of relations between all types of concepts.

### Topic extraction

Topics are important phrases or simply key-words that are explicitly mentioned in the document. These phrases, however, are not bound to a specific ontology or knowledge and are extracted on the fly from the stories. As such, they represent the news in terms of coverage better but do not bring along rich background information the way concepts do.

For instance, a news story about Mark Rutte mentions that he is the leader of the "People's Party for Freedom and Democracy (VVD)", however, the abbreviation "VVD" is not part of the available names for this party (as "ANP", for example). Therefore this will not be extracted by the concept extraction algorithm. Instead, it will be extracted by the topic extraction routine. Abbreviations are used in particular in informal chats and as search words. That is why having topics is very valuable for the search and navigation of content. The extraction of topics also adds dynamics and increases the coverage of the system.

### Named entity recognition

In the Named entity recognition task, an algorithm seeks to locate and classify chunks of text into pre-defined categories such as the names of persons, organizations, locations, expressions of times, quantities, monetary values, percentages, etc.

NER systems take an unannotated block of text, such as this one:

_Jim bought 300 shares of Acme Corp in 2006._

and produce this one:

<img src="{{ site.baseurl }}/img/NER.png" alt="NER" style="width:350px;height:50px; margin: 0 auto">

### Named Entity Linking

Named entity linking (Disambiguation) is a task that aims to link a chunk of text to a rich concept representation of an object in a database. These objects, which we call concepts, have additional links to other concepts and thus represent a rich graph structure.

For instance, Barack Obama is linked to the Democratic Party (his political orientation), other Democrats such as Joe Biden, a place of birth, a spouse, children, education, etc.

The main problem in this task is disambiguating between multiple candidates for a single mention of their name in the text.

<img src="{{ site.baseurl }}/img/Tagasauris_disambiguation.png" alt="Disambiguation" style="width:800px;height:400px; margin: 0 auto">

### Relation extraction

Relations are references between one or more concepts and things that are extracted only from the text of the story. These should not be mistaken with relations that already exist in the knowledge base. Relations add new knowledge and connections between concepts in the knowledge base and can be of the following types:

#### Generic Relations (they appear as Relation in the testing UI)

Generic Relations attempt to connect at least one recognized concept to other concepts or topics without predefining relationship types.

#### Person relations

Person relation expresses a relation between a person and:

* the role role he/she is playing in a given situation/organization/location (RelationPersonRole)\\
E.g.: "These crush plants are having a tough time finding supply," said *Rich Nelson, chief strategist* at Allendale, a Chicago futures broker."
* an organization, characterized by the role he/she has in it (RelationPersonHasRoleWithinOrganization)\\
E.g.: "North Korea usually raises tensions during [US and South Korean] exercises to soften up their partners before major talks," says *Andrei Lankov, a professor at Kookmin University*."
* an organization when the role of the person with respect to the organization is unspecified (RelationPersonRelatedToOrganization)\\
E.g.: "Overall, the net policy changes are negligible and will have little immediate impact on growth," said *Kevin Gardiner of Barclays*."
* the role of the person with respect to the organization and the location of the organization (RelationPersonHasRoleWithinOrganizationInLocation)\\
E.g. "*Kevin Gardiner, chief investment officer for Europe at Barclays Wealth*".
* a location where he plays role (RelationPersonHasRoleInLocation)\\
E.g. "London School of Economics holds a public lecture on "The Future of London within the UK" at which *London Mayor Boris Johnson* will speak."
* a quotation of him/her (RelationPersonQuotation)\\
E.g. Winston Churchill said: "Success is not final, failure is not fatal. It is the courage to continue that counts."

#### Organization relations

Company relations express relation between:

* two organizations where one is a suborganization of the other (RelationSubOrganization)\\
E.g. "She has an MPhil in criminology at Cambridge university and a PhD in psychology at the *Institute of Cognitive Neuroscience at University College London*."
* Organization abbriviation (RelationOrganizationAbbriviation)\\
E.g. "There has been increasing pressure from NGOs and lobby groups asking that scientists "with industry relations" should be excluded from advisory panels of the European Commission and its agencies, such as the *European Food Safety Authority (EFSA)* in Parma or the *European Medicines Agency (EMA)* in London, saying that they would not be able to act as independent experts."
* an organization and a location where it has site (RelationOrganizationLocation)\\
E.g. "Tomoya Shibayama, an expert on civil and environmental engineering at *Tokyo's Waseda University*, says researchers are reaching back into Japan's past."
* two organizations where one is a provider and the other is customer (RelationOrganizationHasCustomer)\\
E.g. "For the time being, *Honda will provide engines to McLaren* on an exclusive basis, but that does not preclude it from supporting other teams in the future."
* two organizations, which are in competition (RelationOrganizationHasCompetitor)\\
E.g “*VW has reported several quarters of strong earnings as falling sales at its mass-market rivals in Europe left carmakers such as France's PSA Peugeot Citroen and Ford* of the US nursing overcapacity and considering plant closures.”
* two organizations, which are in a partnership (RelationOrganizationPartnership)\\
E.g. However, two people close to the talks said *ArcelorMittal had formed a consortium with Japan's Nippon Steel* to buy the plant.
* two organizations where one is in a position of acquiring the other one (RelationAcquisition, RelationMerger)\\
E.g. In January 2012, *IBM acquired mobile development platform Worklight*, which supports HTML5 development, while SAP's Sybase business is currently working on mobile front-end products that use a mixture of HTML5 and native code.
* two organizations. In the current pipeline these are relations between two organizations which occur in the same sentence and which are not arguments of one and the same existing annotation. (RelationOrganizationOrganization)\\
E.g. But LCH.Clearnet also acts as another reminder why ICE and NYSE are seeking the EU path.
* an organization and a quotation of it (RelationOrganizationQuotation)\\
E.g. “Forrester says: "The internet has the ability to change every part of the economy. We are talking about trillions of dollars that can be pulled out of cost, which would then free people to spend their money on making better and cheaper products. There is growth hiding in the economy if we simply take advantage of all the multiple efficiencies.”

#### Other relation types

* The relation between two locations such as subregion of, part of, located in, etc.\\
E.g. "These days, it is the Chinese who are showing up in this far tip of the *Buddhist kingdom of Mustang, northwest of Katmandu, Nepal*. "
