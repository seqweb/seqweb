---
layout: default
title: SeqWeb Ecosystem
---
# ![SeqWeb Project Icon](/assets/icons/seqweb_icon_64x64.png) SeqWeb Ecosystem

The SeqWeb _Ecosystem_ contains three large dynamic _Systems_:
- the **OEIS**, which is independent, 
- **SeqWeb**, which extends the OEIS, 
- and **GitHub**, which connects them together.

## Systems

The **OEIS** System is associated with the _Org_ `oeis.org`.  It is owned and operated by the OEIS Foundation, a 501(c)3 charitable organization headquartered in New Jersey.  It supports the public OEIS website and endpoints, and is the primary source for the encyclopedic mathematical sequence content and related materials that the SeqWeb system augments.

The **SeqWeb** System is associated with the Org `seqweb.org`.  It is currently owned and operated by Prometheus Sherpas LLC, a California corporation (pending intended future reassignment to a fully non-commerical party).  Its development is the objective of this project.  SeqWeb's focus is providing knowledge graph "metadata" that augments the OEIS, and also providing related online services.

The **GitHub** System is associated with the Org `github.org`.  It is owned and operated by Microsoft.  It is a well-known code, data and content repository and development-services provider, used for numerous proprietary and open-source projects.  GitHub connects the SeqWeb project Ecosystem together, and supports its development and use.  

## Subsystems

### OEIS Subsystems

The OEIS System operates and maintains a set of closed proprietary Subsystems that store, search and serve the OEIS data via the `www.oeis.org` website; host a user-developed wiki at the `www.oeis.org/wiki` subsite; and provide online tooling for the volunteer editors to review and edit submissions via the `www.oeis.org/drafts` subsite.

Most importantly, from SeqWeb's perspective, the OEIS operates what will call its _Publication Subsystem_ that regularly uploads updated snapshots of the entire OEIS article corpus and associated collateral materials into the GitHub System.

### GitHub Subsystems

The GitHub System supports four primary _Repository Subsystems_ named `oeisdata`, `seqwebdata`, `seqwebcode` and `seqweb`.  The first is associated with `oeis.org` and the other three with `seqweb.org`.

The OEIS and SeqWeb are related exclusively through data repositories in GitHub: as mentioned above, the OEIS System uploads updated snapshots to its `oeisdata` repo.  Then, downstream, the SeqWeb System sources this input and outputs a derived knowledge graph into its `seqwebdata` repo.  

All code for the SeqWeb Sytem's processing, for serving the results, and for developer tooling, resides in the `seqwebcode` repo.

Finally the `seqweb` repo contains the technical documentation and project collateral, and provides content for the www.seqweb.org website via the _GitHub Pages Subsystem_.


### SeqWeb Subsystems

