ToDos

This is a general dumping ground and parking lot for things that need doing or at least remembering.

### "core" sequences:
$ python3 -i "src/python/get_core_oeis_numbers.py" 
>>> core = get_oeis_core_numbers()
Successfully logged into OEIS
>>> core
[1, 2, 4, 5, 7, 9, 10, 12, 14, 19, 27, 29, 31, 32, 35, 40, 41, 43, 45, 48, 55, 58, 69, 79, 81, 85, 88, 105, 108, 109, 110, 111, 112, 120, 123, 124, 129, 140, 142, 161, 166, 169, 182, 203, 204, 217, 219, 225, 244, 262, 272, 273, 290, 292, 302, 311, 312, 326, 330, 364, 396, 521, 578, 583, 593, 594, 602, 609, 612, 670, 688, 720, 793, 796, 798, 959, 961, 984, 1003, 1006, 1034, 1037, 1045, 1055, 1057, 1065, 1097, 1113, 1147, 1157, 1190, 1221, 1222, 1227, 1285, 1333, 1349, 1358, 1405, 1462, 1477, 1478, 1481, 1489, 1511, 1519, 1615, 1622, 1699, 1700, 1764, 1906, 1969, 2033, 2083, 2106, 2110, 2113, 2275, 2322, 2378, 2426, 2487, 2530, 2531, 2572, 2620, 2654, 2658, 2808, 3094, 3136, 3415, 3418, 3484, 4011, 4018, 4526, 5036, 5100, 5101, 5117, 5130, 5230, 5408, 5470, 5588, 5811, 5843, 6318, 6530, 6882, 6894, 6966, 7318, 8275, 8277, 8279, 8292, 8683, 10060, 18252, 20639, 20652, 20653, 25487, 27641, 27642, 35099, 38566, 38567, 38568, 38569, 49310, 55512, 56239, 70939, 74206, 104725, 112798, 217831, 226898, 246655]


### Command Line & Dev environment
- Where we left off Waldo had created a sort of cursor-infected $workspace command or something -- fix this
- figure out $setup vs $start/stop vs $run -- in Myriad the "service providers" were hardwired (ie Fuseki/LLM/http) but in SeqWeb there will be need for 1. plugins and 2. possibly multiple providers (eg Fuseki locally vs Allegro at seqweb.org)
- what's the deal with open source and Jira and stuff?  how about "issues" vs dev projects?


### Webhouse
- "Where" do the non-Sequence entities (such as Users etc) "live"?  What do they look like?
- seq/ has subdirs for every 1000 seqs -- should anticipate this pattern will probably repeat -- for example 13000 Users in alphabetical buckets -- also other things in the OEIS (index topics?) is divided up "encyclopedia style" ("Extracti to Gamb"). -- so file mapping is maybe <class>/<bucket>/<entity>.rdf -- what does rsc do in that oeisdata/files/ dir?
- it would be nice to support a REST (maybe read-only) interface that could make any bucketing transparent.
- also only seqs and their files are in oeisdata repo, other stuff such as Users have to be retrieved from the wiki site 

### Webwright
- Need to anticipate eventual introduction of incremental updates; will need to track support provenance -- "what does this triple depend on?"  what fabricator created it and maybe needs to get rerun to refresh it?  how do things get deleted?  are they all in different "subgraphs" each with its own "management" policy?  what requirement does this put on minimum acceptable triple store functionality?
- what's the policy about how much should be duplicated/cached in seqwebdata?
- Decision: let's initially go with "full hydration", meaning all the text will be present, that is "denormalized". -- If storage footprint is an inhibiting issue initially let's just reduce the number of entities rather than add complication by having the strings be "outside" the TS
- Anyway need to look at having only partially populated corpus -- cases (we should support a mix of both)
- - dense: for example the first thousand, ie everything in A000/
- - sparse: for example the 183 core sequences
- 3 levels of sequences:
- - highly-permissive base class
- - subclass conforming to OEIS rules, maybe apply SHACL to verify this
- - subsubclass of seqs actually in the OEIS
- what are the conventions for new/generated sequences not yet in the OEIS?  and/or drafts? (in OEIS and/or in SeqWeb)
- have way to render SeqWeb sequences back into OEIS internal format, for example to facilitate submission to OEIS

### General strategy plan for fabrication development
- From support < OEIS website functionality, through ~=OEIS capabilities to > OEIS
1.  stub entries with just <OEIS URL> a seq:Sequence; (& maybe name, comment, date) # need to distinguish "level" of sequence
2.  include all text as a single blob
3.  section text into multiple "internal" blobs only by tag
4.  tag-specific processing
4.a quasi-general purpose multi-line segmentation
4.b aggregation (eg STU tags, Mathematica/Maple)
4.c special cases (eg data, offset, keywords)
5. blob annotation, eg entity extraction (A-links, Users, URLs/hrefs)
6. generation of new sequences & relations
- what augmentations go in "main" ttl file, what in aux files? (cases: same subject, non-subject, non-sequence)

### Licenses & IP stuff
- Review the licenses.  Maybe get some pro bono advice on the content.  I believe that seqwebdata is a "derivative work" (like say the Annotated Alice) and that the hybrid license is OK/correct, but at some point this should be verified.
- I also beleive that, at worst, the OEISF could just require the MIT part get removed, and that meanwhile there's no real risk to the OEIS's IP
- Someday maybe the copyright holder should also change from Prometheus Sherpas, LLC, but for now this provides both a legal owner, along with at least a little distancing from me personally -- at some point maybe talk this over with an open-source guru about contributor expectations etc.
- In any case make sure *something* gets "served" appropriately at each access point.  See seqweb/_dev/migration/LICENSE-GUIDE.md for some (outdated) notes about some of the places this comes up (2025-08-08 added a footer to the website)



