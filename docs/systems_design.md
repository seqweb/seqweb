---
layout: default
title: SeqWeb Systems Design
---
# ![SeqWeb Project Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb Systems Design

This document provides a systematic, concrete overview of the form and substance of SeqWeb.  For background and orientation regarding the project's context, motivation, and aims, readers should refer to the [SeqWeb Overview](/docs/seqweb_overview.md).

To ensure contextual grounding, this presentation follows a top-down, _breadth-first_ order.  It first presents SeqWeb as an Internet _system_, operating closely within its Web _ecosystem_ with two others.  It then summarizes each of the relevant component _subsystems_ of those systems.  Finally, it overviews the particular subsystems.  More detailed designs and implementations for each subsystem are then left to be addressed in their own linked documents.

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

Each subsystem is scoped to a separate area of concern or particular _domain_ of operation.  From an internal or implementation perspective, they are best described using their own domain-specific vocabularies.

### ![OEIS Icon](/assets/icons/oeis_icon_64x64.png) OEIS Subsystems

The OEIS system operates and maintains a set of closed, proprietary subsystems that store, search, and serve the OEIS data via the `www.oeis.org` website, hosts a user-developed wiki at the `www.oeis.org/wiki` subsite, and provides online tooling for its large volunteer community to review and edit submissions via the `www.oeis.org/drafts` subsite.

Most importantly, from SeqWeb's perspective, the OEIS operates what we will call its _Publication Subsystem_ that regularly uploads updated snapshots of the entire OEIS article corpus and associated collateral materials into the GitHub system.

### ![GitHub Icon](/assets/icons/github_icon_64x64.png) GitHub Subsystems

The GitHub system supports four primary _Repository Subsystems_:
- ![OEIS Data Icon](/assets/icons/oeisdata_icon_32x32.png) `oeisdata`
- ![SeqWeb Data Icon](/assets/icons/seqwebdata_icon_32x32.png) `seqwebdata`
- ![SeqWeb Code Icon](/assets/icons/seqwebcode_icon_32x32.png) `seqwebcode`
- ![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) `seqweb`

The first is associated with `oeis.org` and the other three with `seqweb.org`.  The OEIS and SeqWeb systems themselves are operationally decoupled and are related exclusively through their data repositories in GitHub.  

![OEIS Data Icon](/assets/icons/oeisdata_icon_32x32.png) As mentioned above, the OEIS system uploads updated snapshots to its `oeisdata` repo.  

![SeqWeb Data Icon](/assets/icons/seqwebdata_icon_32x32.png) Then, downstream, the SeqWeb system sources this input and outputs a derived knowledge graph into its `seqwebdata` repo.  

![SeqWeb Code Icon](/assets/icons/seqwebcode_icon_32x32.png) All code for the SeqWeb system's processing, for serving the results, and for developer tooling, resides in the `seqwebcode` repo.

![SeqWeb Icon](/assets/icons/seqweb_icon_32x32.png) Finally, the `seqweb` repo contains the technical documentation and project collateral, and provides content for the www.seqweb.org website via the _GitHub Pages Subsystem_.

### ![SeqWeb Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb Subsystems




