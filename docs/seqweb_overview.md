---
layout: default
title: SeqWeb
---

# ![SeqWeb Project Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb

## ***SeqWeb*** _is to the_ **OEIS** _as_ **DBpedia** _is to_ **Wikipedia**.

- SeqWeb extends the OEIS, one of the world's largest mathematical knowledge bases, with an independent, open, knowledge graph resource.

- SeqWeb's mission is to further empower and amplify the discovery and development of this knowledge.

- SeqWeb is in active development with core infrastructure, architecture and ontology design and implementation underway.

- SeqWeb is seeking interested potential early collaborators, academic partners, and supporters.

See the [SeqWeb project homepage](https://www.seqweb.org/) for links to additional information.

### SeqWeb Overview
The SeqWeb project is a community effort to create a new open resource of structured content, built on the   foundation of information established by the OEIS, freely available as an independent knowledge graph on the Web. 

A knowledge graph is a datastore that provides rich ways for information to be represented, organized, accessed, augmented and used.  A familiar example is Google searches: the "knowledge cards" it displays alongside results are powered by similar metadata.

The core technologies and standards embodied by modern knowledge graphs make the world’s data more accessible, interoperable, and useful—not just for people, but also for machines as well, such as today's AI engines.  See https://www.w3.org/2013/data/ for more background.

### SeqWeb's Global Embedding Matrix

_"SeqWeb is to the OEIS as DBpedia is to Wikipedia."_

SeqWeb's tag line outlines a four-part framing analogy that describes the project in the context of the open World Wide Web ecosystem.  All four components of the analogy are Web resources addressable via public Internet domains, namely `seqweb.org`, `oeis.org`, `dbpedia.org` and `wikipedia.org`.

These four Web entities can be organized naturally along two axes: _subject focus_ and _content type_. 

- The _subject focus_ of both Wikipedia and DBpedia is general information, while for both the OEIS and SeqWeb it is the more specialized area of mathematical integer sequences.

- Along the _content type_ dimension both Wikipedia and the OEIS present rich web pages of interlinked documents intended for human consumption.  In contrast, both DBpedia and SeqWeb implement machine-readable knowledge graphs.

This framing is easily visualized by laying out the analogy as a 2-by-2 matrix.

<!--
                   General Info    Sequences  
                 +--------------+-------------+
Natural Language |  Wikipedia   |    OEIS     |
                 +--------------+-------------+
Knowledge Graph  |   DBpedia    |   SeqWeb    |
                 +--------------+-------------+
-->

|                     |              |                |
|---------------------|--------------|----------------|
|                     | _sequences_  | _general info_ |
| _knowledge graphs_  | ***SeqWeb*** | **DBpedia**    | 
| _linked documents_  | **OEIS**     | **Wikipedia**  |

Here's a thumbnail description of each component, with links to more information:

### Wikipedia
Good ol' Wikipedia is familiar to almost everyone, but we might summarize it as a community-curated online encyclopedia of general information subject matter.  It is presented as web pages of formatted natural language content, along with associated graphics, references to relevant external resources and links to other Wikipedia articles.  For more general background about Wikipedia, see (of course) https://en.wikipedia.org/wiki/Wikipedia.

### OEIS
The OEIS is likewise a comprehensive, community-curated online encyclopedia presented as rich web pages, supported by a worldwide community of contributors and editors.  However, in contrast to Wikipedia, the subject matter domain of the OEIS is more specialized, organized around mathematical integer sequences.  

In practice over many years the OEIS has become a very broad catalog of knowledge in discrete mathematics, number theory and related areas.  Originated by Neil Sloane in 1964, first appearing in book form and then initially hosted by AT&T Bell Labs, it was spun off as a non-profit charitable foundation and open website by 2009.  It serves about 1 million hits a day with about 130,000 distinct users per month.  It currently hosts entries for around 400,000 sequences, receiving about 100 contributions a day from 13,000 registered users.  Submissions are reviewed and committed by around 200 volunteer editors and admins.

For more general information regarding the OEIS, see Wikipedia's article https://en.wikipedia.org/wiki/On-Line_Encyclopedia_of_Integer_Sequences, which includes some illustrative examples and other details.

A unique aspect of the OEIS is that its articles can be retrieved by their sequence's numerical pattern, as well as more conventional search queries.  This not only makes the OEIS a valuable content-addressable knowledge resource for general users that overcomes barriers of academic jargon, it has led to many serendipitous discoveries for mathematical researchers.  The OEIS has been cited in the scientific literature over 11,000 times.

The OEIS site also supports a wiki, at https://oeis.org/wiki/Main_Page.  The OEIS wiki includes much additional information, including a helpful orientation page https://oeis.org/wiki/Welcome.

Anyone who registers as an OEIS user is assigned a "user page" in the wiki; for example https://oeis.org/wiki/User:N._J._A._Sloane (whose Wikipedia page is at https://en.wikipedia.org/wiki/Neil_Sloane).  The wiki can host user-provided content to supplement the entries, and the entries themselves employ the user-wiki URLs to assign authorship provenance, etc.  Thus the OEIS is a nexus not only of mathematical information, but a community of people, reflected in the Web.

### DBpedia
DBpedia might be described as an extensive, community-curated semantic knowledge base that augments Wikipedia.  It represents Wikipedia as a knowledge graph, and is a premier exemplar of Tim Berners-Lee's vision of the Semantic Web.   

_"...the Semantic Web is to enable people to share content ... beyond the boundaries of **strings** to the concept of **things**.”_<br>
        —- Tim Berners-Lee, _Linked Data - Design Issues_, W3C, July 2006

DBpedia provides machine-readable access to Wikipedia's underlying human-readable information.  It is implemented using the advanced data technologies (such as RDF, SPARQL, etc.) developed and standardized by the W3C and the semantic web community.  It models the Wikipedia as an open interconnected knowledge graph for computational exploration and integration.  

Semantic technologies can bridge the natural expressiveness of human language with the power and utility of modern structured data and high-performance processing.  This has become especially relevant with the advent of modern LLMs and other AI technologies.

An introduction to DBpedia can be found in the Wikipedia article https://en.wikipedia.org/wiki/DBpedia.  The DBPedia site itself also hosts informative pages, including https://www.dbpedia.org/about/.

For a concrete example, here is the actual DBpedia page corresponding to Wikipedia's DBpedia article: https://dbpedia.org/page/DBpedia.  

Note that you can easily get from one URL to the other simply by switching the prefix part of the URL while  attaching the same "DBpedia" suffix.  For less self-referential examples, try suffixing "Neil_Sloane" or "On-Line_Encyclopedia_of_Integer_Sequences" to that "https://dbpedia.org/page/" prefix instead.

### SeqWeb
Thus the goal of the SeqWeb project is to develop and provide a companion web resource for the OEIS, akin to how DBpedia extends Wikipedia.  

When fully operational, SeqWeb can be expected to tangibly include
- a knowledge graph implemented in a publicly-hosted database containing standardized linked open data
- an open ontology describing the semantics and schemas of the SeqWeb data for human and machine (eg AI) use
- a web service providing APIs and both conventional (REST) and semantic (SPARQL) query endpoints for applications
- infrastructure and automation for syncing and aligning SeqWeb with the ever-evolving OEIS
- support for ongoing "open source" development processes and the contributing community
- a technical framework encouraging extensions and experiments

#### Tenet of Independence of the OEIS from SeqWeb
A central tenet of the project is that SeqWeb _augments_ the OEIS wholly independently and additively, without burdening or impacting the OEIS in any way.  No modifications of any kind to the OEIS's style, content, technology or operations are needed or desired.  Just as Wikipedia's readers and staff need not be concerned with the existence of DBpedia, the OEIS and its community of users and editors can continue as always -- _"It ain't broke; don't fix it."_

#### Syncing SeqWeb with the OEIS via Github
Conveniently, there is no requirement that SeqWeb keep up in real time with changes to the OEIS (just as for DBpedia with Wikipedia).  But, as it happens, the OEIS uploads an update to its complete snapshot to Github every few days.  The update is applied to the repository at https://github.com/oeis/oeisdata.  This repo's directory structure maps each OEIS article to a predictable Github file path URL.  For example the OEIS sequence at URL https://oeis.org/A046970 is memorialized in the `oeisdata` repo as the file https://github.com/oeis/oeisdata/blob/main/seq/A046/A046970.seq.  SeqWeb can therefore track the `oeisdata` repo to synchronize its model with the OEIS at will, to get the updated _base model_.  

Additionally, it is expected that SeqWeb will also support a collection of diverse processing modules whose purpose is to augment the base model with additional _derived_ information.  For example, a module might apply a natural language library to a particular section of an OEIS article's data (say the bibliographic citations) in order to extract associated named entities (say authors, or journals) to be represented as triples in the SeqWeb knowledge graph.  These modules will need to be re-run when their source data changes.  The detailed architecture for these modules, and the framework for orchestrating their application, is one of the more interesting tasks now awaiting further architecture and engineering.

Development and delivery for SeqWeb are supported by Github repos, under the https://github.com/seqweb/ prefix:

- `seqwebdata` is intended to contain a snapshot of SeqWeb's knowledge graph, synchronized to track with changes to `oeisdata` and reflecting its content and structure

- `seqwebcode` is the development repo, and is intended as the source control for everything else in the SeqWeb system

[Note: currently, during early development, SeqWeb's formal ontologies and related collateral (such as vocabularies, modeling guides, etc) are assigned out of convenience to the `seqwebcode/ontology/` folder -- however we will want to revisit this and migrate them into `seqwebdata` instead]

#### Possible Benefits to the OEIS from SeqWeb
While it's impossible to predict what innovative uses for the OEIS's trove of knowledge SeqWeb will inspire, interesting outcomes can be anticipated in a few areas:

**Rigor** SeqWeb and the semantic web modeling disciplines can provide an additional level of precision to that already enjoyed by the OEIS as a professional-quality mathematical reference.  While not quite at the level of, say, an automatic proof checker, knowledge modeling best practices--such as registering definitions of terms being used in vocabularies and specifying their relationships using formal ontologies--will tend to reinforce and raise the overall quality bar.  For example these mature disciplines can help ameliorate ambiguities like whether a certain string token refers to a mathematical function, its approximate implementation in a particular programming language, or something else.

Even relatively banal statements can be made more precise and usable.  For example a sequence with a definition including text like "...primes such that..." can be expressed precisely:
```
    oeis:A000668    seq:subsetOf    oeis:A000040 .
```

**AI**  In a related vein, SeqWeb may be used to help control LLMs and turn them, in the OEIS context, from unreliable slop generators toward greater utility -- for example they could be part of a natural language query interface to the OEIS, implemented using the "Graph-RAG" pattern.

**Extensions**  Perennially, users of the OEIS come up with interesting ideas for possible features, motivated by specialized interests and use cases.  Typical examples are proposals for new keywords, perhaps accompanied by further ideas like parameterizing them.  Alas these enthusiasms usually have to be squelched, since disrupting the current underlying legacy data model would be taxing, and the consequent maintenance burden unsupportable.

SeqWeb can provide a constructive alternative avenue for these worthy proposals.  Since knowledge graphs can host an unbounded number of new assertions, and triple subgraphs provide a fully flexible yet highly-structured expressive medium, users can develop and deploy accretive extensions into SeqWeb, without disrupting pre-existing OEIS schemas and workflows.

**Federation**  Moreover, unlike traditional information assets, knowledge graphs need not be confined to single datastores.  Since triples are built entirely from URLs (IRIs actually) they can, and do, reference resources that can be anywhere on the World Wide Web.  The SPARQL query language includes features that seamlessly query  endpoints on external hosts as though they are simply subgraphs.  
Here's a simplified example showing a federated query issued to my local SPARQL endpoint that looks up a DBpedia entry:
```
SELECT ?person ?name WHERE {
  SERVICE <http://dbpedia.org/sparql> {
    ?person a foaf:Person .
    ?person rdfs:label ?name .
    FILTER (LANG(?name) = "en" && regex(?name, "Neil", "i") && regex(?name, "Sloane", "i")) .
  } 
} 
```
This returns
```
<http://dbpedia.org/resource/Neil_Sloane>   "Neil Sloane"@en
```
Thus for example a single query expression might find all sequences authored by the set of all of someone's doctoral students, as this set is obtainable from DBpedia.

This means in particular that extensions can be entirely implemented and hosted entirely independently by anyone at any time, thus removing material coordination barriers to diverse contributions.

For example a set of specialized relationships between sequences could be identified by an academic researcher, embodied as a knowledge graph maintained at their home institution, and made fully available via federation with SeqWeb -- all without requiring any operational support from either SeqWeb or the OEIS.

**Discoveries** SeqWeb will enable and encourage new sorts of explorations.  For example sequence "transforms" are an important longstanding concept in the OEIS.  But today the only way to express these relationships is with natural language text commentary like "a(n) is the Möbius transform of the primes."  With  SeqWeb this could be modeled definitively by a triple like
```
    oeis:A007444    seq:möbiusTransformOf   oeis:A000040 .
```

To support this, the "core" SeqWeb ontology will contain definitions like
```
    seq:möbiusTransformOf   rdf:type        seq:Transform ;
                            seq:definition  oeis:wiki/Möbius_transform ;
                            rdfs:domain     seq:Sequence ;
                            rdfs:range      seq:Sequence ;
                            owl:inverseOf   seq:InverseMöbiusTransformOf ;
                            ... .
```
As a possible application, one could imagine a program that systematically walks through all the sequences, applying Superseeker transforms, and adding the appropriate assertions into the SeqWeb graph whenever it gets a hit.  In aggregate, this "transform subgraph" would itself be an interesting object of study (for example compound transform relationships, cycles and cliques).  Generally, such automations will advance and expand our OEIS-inspired knowledge without adding unduly to human editorial burdens.

It will be interesting to see what innovations the addition of the SeqWeb resource into the ecosystem may catalyze.

---
