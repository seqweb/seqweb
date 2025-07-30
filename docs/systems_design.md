---
layout: default
title: SeqWeb Systems Design
---
# ![SeqWeb Project Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb Systems Design

This document provides a systematic, concrete overview of SeqWeb's form and substance.  For background and orientation regarding the project's context, motivation, and aims, readers should refer to the [SeqWeb Overview](/docs/seqweb_overview.md).

To ensure contextual grounding, this presentation follows a top-down, _breadth-first_ order.  It first presents SeqWeb as an Internet _system_, operating closely within its Web _ecosystem_ with two others.  It then summarizes each of the relevant component _subsystems_ of those systems.  Finally, it provides an overview of the particular subsystems.  More detailed designs and implementations for each subsystem are then left to be addressed in their own linked documents.

[Note: Since SeqWeb is currently an early-stage work-in-progress, many presentations will only be sketches, with brief development hints, leaving things open for future collaborations.]

## Systems of the Ecosystem

The SeqWeb ecosystem includes:
- ![OEIS Icon](/assets/icons/oeis_icon_32x32.png) **OEIS**, an independent system and primary data source,
- ![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) **SeqWeb**, which extends and augments the OEIS,
- ![GitHub Icon](/assets/icons/github_icon_32x32.png) **GitHub**, which connects them and provides infrastructure and tooling.

Each system is associated with a distinct _org_ (organizational entity), which serves as the shared identity for an Internet domain and the human organization that supports and governs its content and services.

![OEIS Icon](/assets/icons/oeis_icon_32x32.png) The **OEIS** system is associated with `oeis.org`. It is owned and operated by the OEIS Foundation, a 501(c)(3) nonprofit organization headquartered in New Jersey. The foundation maintains the public OEIS website and associated endpoints, and stewards the encyclopedic database of integer sequences and related resources. 

![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) The **SeqWeb** system is associated with `seqweb.org`. It is currently owned and operated by Prometheus Sherpas LLC, a California corporation, with an intended future transition to a fully non-commercial entity. Development and operation of SeqWeb is the primary objective of this project. Its mission is to deliver a knowledge graph that semantically enriches and extends the OEIS, along with related online services.

![GitHub Icon](/assets/icons/github_icon_32x32.png) The **GitHub** system is associated with `github.org`. It is owned and operated by Microsoft. GitHub is a widely used platform for hosting code, data, and collaborative development workflows. In the SeqWeb ecosystem, GitHub hosts the two key data repositories used by the other systems.  It also hosts the SeqWeb website and provides infrastructure for development, documentation, maintenance, and community engagement.

## Subsystems of the Systems

Having identified the three major systems that comprise the SeqWeb ecosystem, we now examine their component subsystems to understand how each system is organized and functions.

Each subsystem is scoped to a separate area of concern or particular operational _domain_.  From an internal or implementation perspective, they are best described using their own domain-specific vocabularies.

### ![OEIS Icon](/assets/icons/oeis_icon_64x64.png) OEIS Subsystems

The OEIS system operates and maintains a set of closed, proprietary subsystems that:
- store, search, and serve the OEIS data via the `www.oeis.org` website
- host a user-developed wiki at the `www.oeis.org/wiki` subsite
- provide online tooling for its large volunteer community to review and edit submissions via the `www.oeis.org/drafts` subsite

Most importantly, from SeqWeb's perspective, the OEIS operates what we refer to as its _Publication Subsystem_ that regularly uploads updated snapshots of the entire OEIS article corpus and associated collateral materials into the GitHub system (described below).

### ![GitHub Icon](/assets/icons/github_icon_64x64.png) GitHub Subsystems

The GitHub system provides the repository infrastructure that enables the OEIS and SeqWeb systems to store and deliver data.  It also hosts SeqWeb's project code, docs, collateral, and community website.  

The GitHub system supports four primary _Repository Subsystems_:
- ![OEIS Data Icon](/assets/icons/oeisdata_icon_32x32.png) `oeisdata`
- ![SeqWeb Data Icon](/assets/icons/seqwebdata_icon_32x32.png) `seqwebdata`
- ![SeqWeb Code Icon](/assets/icons/seqwebcode_icon_32x32.png) `seqwebcode`
- ![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) `seqweb`

The first repo is associated with `oeis.org` and the other three repos with `seqweb.org`.  Because the OEIS and SeqWeb systems are intentionally operationally decoupled, they are correlated through structural parallels between their data in GitHub--for example the `seqwebdata` file named `A000000.ttl` would be derived using information from the corresponding `oeisdata` file named `A000000.seq`.

![OEIS Data Icon](/assets/icons/oeisdata_icon_32x32.png) As mentioned above, the OEIS system uploads updated snapshots to its `oeisdata` repo.  

![SeqWeb Data Icon](/assets/icons/seqwebdata_icon_32x32.png) Then, downstream, the SeqWeb system sources this input and outputs the knowledge graph, archiving it into its `seqwebdata` repo.  

![SeqWeb Code Icon](/assets/icons/seqwebcode_icon_32x32.png) All code for the SeqWeb system's processing, for serving the results, and for developer tooling, resides in the `seqwebcode` repo.

![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) The `seqweb` repo contains the technical documentation and project collateral, and provides content for the www.seqweb.org website via the _GitHub Pages Subsystem_.

### ![SeqWeb Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb Subsystems

The SeqWeb system design is driven by two major subsystem domains:
- the **Webwright** subsystem that _builds_ the knowledge graph content
- the **Webhouse** subsystem that _delivers_ that content in various ways

#### Webwright

The Webwright subsystem constructs the knowledge graph by orchestrating an ensemble of _Fabricators_. Each Fabricator is responsible for generating a specific portion of the knowledge graph, and implements a polyglot processing pipeline (see [Polyglot Pipeline Architecture](/docs/polyglot-pipeline.md)) composed of individually-implemented software _Modules_ designed to interface readily with related Modules.

For example, a Fabricator might extract entities from selected OEIS entry text, and then build RDF triples linking the subject sequence to those entities via specific relationships. Upstream Modules in this Fabricator would handle entity extraction; downstream Modules would generate the RDF; others may support intermediate transformation, filtering, or enrichment.

The Webwright framework is intentionally open-ended and designed for collaborative extension. Community contributors—whether mathematicians, language analysts, scientific sociologists or others—are encouraged to develop new Fabricators or share advanced Module technologies that reflect their own research interests and domain expertise. As an open-source project, SeqWeb encourages these contributions to build the knowledge graph, and then expand its scope and richness as it evolves.

#### Webhouse

The Webhouse subsystem delivers the constructed knowledge graph and its related artifacts to users and services. This includes the main sequence graph itself, supporting ontologies, definitions for derived or associated entities (like Users, Transforms, etc) and various forms of collateral content.

Webhouse acts as a _semantic lakehouse_—a unified delivery layer that exposes this content through multiple interfaces: a SPARQL endpoint, RESTful resource APIs, downloadable data snapshots, and interactive user tools such as a SPARQL query console. While static RDF data is published via the `seqwebdata` repository for archival and distribution, the Webhouse serves as the live hub through which all dynamic access, integration, and interaction occurs.

As part of the open-source SeqWeb project, the Webhouse subsystem similarly encourages contributions from systems and data engineers interested in building out its infrastructure and delivery capabilities. As the scope of the system grows—with larger graphs, multiplying ensembles of Fabricators to orchestrate, more diverse endpoints to serve, and higher query volume—many opportunities exist for applying distributed processing, scalable storage, and web performance optimization expertise. Infrastructure contributors can help engineer critical components such as the triple store and ensure that SeqWeb remains both accessible and robust as a public resource—alongside the OEIS, DBpedia and Wikipedia.






