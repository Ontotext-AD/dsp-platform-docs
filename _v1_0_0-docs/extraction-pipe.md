---
title: Extraction Pipeline
layout: default
prev_section: data-behind
next_section: brief-ux
category: Getting Started
permalink: v1_0_0-docs/extraction-pipe/
---
The concept extraction pipeline is a tool for automated analysis of large volumes of textual content, through which mentions of specific concepts and relationships between them can be discovered and represented in a machine-processable format. The pipeline utilizes a wide variety of linguistic and algorithmic resources -- such as semantic gazetteers, rules triggered by particular linguistic patterns, various statistical models for classification and sequence tagging trained against human-annotated corpora, etc. These resources are chained together in a sequence (a pipeline) that provides content enrichment of gradually increasing complexity, where each phase builds upon the results produced by the one that precedes it.

After the initial preprocessing phase (content cleanup, tokenization, sentence-splitting, morphological analysis), the pipeline performs annotation via lexicons and rules in order to extract generic concepts (numbers, measures and metrics), keyphrases (noun phrases relevant to the main topic of a document), named entity discovery and linking (matching the contents of the knowledge base against the document content and resolving any ambiguity that may occur), novel entity recognition (the discovery and handling of named entities previously unavailable in the knowledge base), and, finally, extracting relationships that may exist between the discovered named entities. Furthermore, the pipeline assesses the degree of importance of the concepts in the context of the article where they were discovered, as well as the sentiment of their immediate context.

A high-level description of the phases (sub-pipelines) involved in the concept extraction pipeline

1. Preprocessing phase

During this phase, a very basic pre-processing takes place:

- content clean-up rules (normalization of quotation marks, apostrophes, etc., provision of sentence splits at paragraph boundaries, etc.)
- tokenization (ANNIE English Tokenizer PR)
- sentence splitting (ANNIE Sentence Splitter PR)
- stemming (Snowball Stemmer PR)
- POS-tagging (OpenNLP English POS Tagger PR)
- chunking (OpenNLP English Chunker PR)
- lemmatization (Gate Morpholocial Analyser PR)

Most of the processing resources used during this phase are part of the GATE distribution. Several additional rules that improve the output and facilitate the subsequent tasks are also provided (e.g. rules that insert additional splits on newline characters and document elements available through the “Original markups” annotation set; rules that modify noun phrase chunks in order to improve the extraction of keyphrase candidates; rules that generate canonical forms for such noun phrases).

2. Keyphrase extraction phase

This phase contains logic that generates keyphrase candidates, assigns relevance scores to the candidates, and classifies them into positive or negative instances via a specialized processing resource for supervised classification. [TODO: Replace "a specialized processing resource for supervised classification" with "a heuristic ranking scheme that uses ... " in case we decide to remove the classifier completely]

3. Gazetteer-based enrichment phase

At this stage, the document content is enriched by means of a semantic gazetteer. This component annotates the character sequences that resemble mentions of concepts available in the knowledge base. The gazetteer lookups are not part of the extraction results per se, but they are used as a means of spotting "candidates" that facilitate the statistical models that produce the final set of entities.

All sub-pipelines are responsible for the execution of a single gazetteer (ANNIE Gazetteer, LD Gazetteer), and the consequent transfer of features from the gazetteer-generated annotations to other annotation types involved in the named entity recognition and disambiguation phases.

The “LD Gazetteer” component's cache is populated with instances from the following sources: DBpedia, Wikidata, Umbel [TODO: Add the complete list from Nasko's page].

4. Named entity recognition and disambiguation phase

4.1. Named entity disambiguation

Using the named entity candidates discovered by the LD Gazetteer, and given the specific article context, this phase makes use of a specialized classifier to assign a "positive" or "negative" label to each candidate. As a result, the ambiguity associated with the complete set of gazetteer lookups is eliminated – at most one named entity remains per document offset, and the redundant ("negative") named entity candidates are filtered.

The disambiguation mechanism relies on some additional metadata derived from Wikipedia:

- a short textual description of each candidate (based on DBPedia and Freebase abstracts);
- a set of URIs representing the entities that appear in the full DBpedia article for each candidate.

Various similarity scores are computed by a specialized processing resource that accesses the metadata (stored in indices prepared especially for this task) and evaluates the correspondence between the candidate and the context (the article content and the content stored in the aforementioned indices). The final classification is conducted by a separate processing resource, based on these pre-computed scores and some additional features derived via rules from the local context.

Currently, the component supports disambiguation of named entities belonging to either of the following classes: [TODO: get the current list from the "Dataset" document]

4.2. Discovery of novel named entities

Named entities not available in the “LD Gazetteer” component's cache are not recognized, and therefore not handled by the above-described disambiguation mechanism. The recognition of novel entities belonging to the “Person”, “Location” and “Organization” classes is handled by the PLO Tagger processing resource, which compensates for the lack of perfect coverage by the classifier-based tagging approach.

The results extracted during phases 4.1 and 4.2 are combined in a way that eliminates the overlapping among annotations produced by the disambiguation classifier and the PLO tagger components. The implemented logic guarantees that the disambiguated entities having a meaningful URI are preferred to the anonymous entities discovered by the PLO tagger whenever possible.

5. Generic entity extraction phase

This phase implements a rule-based enrichment with entities of generic type. Currently, these include:

- dates (normalization logic is provided as well)
- numbers
- money
- percentages
- measurements

6. Result consolidation phase

This phase contains rules that take into account all entity types discovered during the preceding phases in order to refine the extraction results. At its end, instance URIs are generated and assigned to entities that have no such identifiers.

This phase involves the execution of the “Orthomatcher” processing resource, which deals with the discovery of orthographical variations of the labels and aliases of people, location and organization entities. Subsequently, the trusted URIs of disambiguated entities are propagated to novel aliases annotated by the PLO tagger, based on linkage done by the “Orthomatcher” component. At the end of the phase, URIs are generated for the entities that have not been assigned a valid URI during the above-described phases.

7. Relation extraction phase

This phase conducts rule-based extraction of various relationships between the atomic entities discovered at the preceding stages. Currently, the following types of relations are supported:

[TODO: Get the actual list from Iva & Alex's page : https://confluence.ontotext.com/display/FT/Benchmarking+II ]


8. Clean-up phase

During this phase, a final clean-up takes place, through which redundant intermediate annotations are removed, the document readability is improved, and the annotation sets are reorganized in order to assume the structure expected by the components that process the documents after the completion of the concept extraction pipeline.

## Types of extracted information

Text can be analyzed by the pipeline at multiple levels including:

* topic extraction - recognizing important words and phrases in the text
* named entity recognition (NER) - extracting people, organization, location, time, amounts of money, etc.
* named entity linking - extracting well-defined concepts available in a pre-defined taxonomy (and stored in the knowledge base)
* extraction of relations between all types of concepts

### Topic extraction

Topics are important phrases or simply key-words that are explicitly mentioned in the document. These phrases, however, are not bound to a specific ontology or knowledge and are extracted on the fly from the stories. As such, they represent the news in terms of coverage better but do not bring along rich background information the way concepts do.

For instance, a news story about Mark Rutte mentions that he is the leader of the "People's Party for Freedom and Democracy (VVD)", however, the abbreviation "VVD" is not part of the available names for this party (as "ANP", for example). Therefore this will not be extracted by the concept extraction algorithm. Instead, it will be extracted by the topic extraction routine. Abbreviations are used in particular in informal chats and as search words. That is why having topics is very valuable for the search and navigation of content. The extraction of topics also adds dynamics and increases the coverage of the system.

### Named Entity Recognition

In the Named Entity Recognition task, an algorithm seeks to locate and classify chunks of text into pre-defined categories such as the names of persons, organizations, locations, expressions of times, quantities, monetary values, percentages, etc.

NER systems take an unannotated block of text, such as this one:

Jim bought 300 shares of Acme Corp in 2006. and produce

this one:

[TODO: Content missing in Confluence - fix]

### Named Entity Linking

Named Entity Linking (Disambiguation) is a task that aims to link a chunk of text to a rich concept representation of an object in a database. These objects, which we call concepts, have additional links to other concepts and thus represent a rich graph structure. For instance, Barack Obama is linked to the Democratic Party (his political orientation), other Democrats such as Joe Biden, a place of birth, a spouse, children, education, etc.

The main problem in this task is disambiguating between multiple candidates for a single mention of their name in the text.

[TODO: Transfer diagrams]

### Relation extraction

Relations are references between one or more concepts and things that are extracted only from the text of the story. These should not be mistaken with relations that already exist in the knowledge base. Relations add new knowledge and connections between concepts in the knowledge base and can be of the following types:

Generic Relations (they appear as Relation in the testing UI)

Generic Relations attempt to connect at least one recognized concept to other concepts or topics without predefining relationship types.

#### Person relations

Person relation expresses a relation between a person and: (i) a company where he has a position (e.g. John Smith from General Electrics); (ii) his occupation (President Obama, general director Mark Thomas); (iii) his position within a location (Mayor of London); (iv) his position within an organization active in a specific location (Ben Bernanke is the chairman of the Federal Reserve, the central bank of the United States). [TODO: Add more from Iva & Alex's page : https://confluence.ontotext.com/display/FT/DSP+Person+Relations ]

#### Organization relations

Company relations express relation between a company and: (i) its location (Siemens of Germany); (ii) its daughter company (VOX Global, a subsidiary of Omnicom Group Inc (OMC); (iii) its mother company; (iv) competitor companies (The Toulouse, France-based aircraft maker is outselling rival Bombardier Inc.); (v) company type (bank, telecom, etc.); (vi) customer companies; (vii) collaborator companies (Bloomberg Finance L.P., a Delaware limited partnership); (viii) another company such as merger/acquisition (Amgen Inc. agreed to acquire Onyx Pharmaceuticals Inc.); (ix) company's abbreviation; (x) and a quotation made by the same company ("...", an allegation denied by Herbalife.). [TODO: Add more from Iva & Alex's page : https://confluence.ontotext.com/display/FT/DSP+Organization+relations ]

#### Other relation types

Relation between two locations such as subregion of, part of, located in, etc. (Tegucigalpa, Honduras;Buddhist kingdom of Mustang, northwest of Katmandu, Nepal)
Relation between a person and quotation ("There’s a perception that doctors are meant to heal wounds, not bleed them,” Mr. Kamara said.) [TODO: Add more from Iva & Alex's page : https://confluence.ontotext.com/display/FT/Benchmarking+II ]
