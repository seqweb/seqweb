# SeqWeb Developer (SeqWebDev) Onboarding Model

[This document develops from the base described in onboarding_model.md]

> “Give me a place to stand and a lever long enough, and I will move the world.”<br>
> _--Archimedes_ (as quoted by Pappus, _Synagoge_ VIII, prop. 10)

SeqWeb development (aka "SeqWebDev") activity spans several Github repositories, including
- oeis/oeisdata: read-only, contains OEIS foundation data
- seqweb/seqwebdata: writes are machine generated only, no manual pushes (probably similar policy to oeisdata), contains SeqWeb's graph data
- seqweb/seqweb: mainly content for public/community-facing static web site www.seqweb.org with docs/collateral served by Github/Jekyll, updates reviewed, otherwise minimal "seed" materials to help seqweb devs bootstrap - any code, config or other data not for direct human consumption should be minimized
- seqweb/seqwebcode: the bulk of the code and data used to: 
1. generate seqwebdata from oeisdata plus additional resources (eg oeiswiki etc)
2. implement non-www services at the seqweb.org domain (eg api.seqweb.org etc)
3. provide "ops" tools to support these services (eg build, test, deploy, monitor etc)
4. provide "dev" tools & resources for implementing and extending all of the above
- seqweb/.github - support for seqweb.org's github "persona" (org admin only)
- optionally additional private developer-managed repos (eg libraries, federations, etc)

The locus of most SeqWebDev work will be `seqwebcode`, with documentation in `seqweb` plus optionally any local custom dev environments.

Given this multi-repo project span, it seems best, therefore, that seqwebdevs establish a local "home" SeqWebDev environment / directory / folder that is *distinct from* those of the clones of the various repos.

For example I use a "parent" home folder, `~/Devo/seqweb` for my seqwebdev work.  It in turn contains  `.../seqweb` and `.../seqwebcode` (and `.../.github`) subfolders for the clones of each of those repos.  The data repos, however, are kept under `~/Devo/Data` which is in turn subdivided by "org", giving `.../OEIS/oeisdata` and `.../SeqWeb/seqwebdata`.  

This folder schema happens to suit my personal operational requirements (eg it sequesters data by "owner", but allows code to be organized by "project") but these considerations aren't universal, and generally the SeqWeb system must be flexible about inter-repo level relationships.

Having a repo-independent seqwebdev home folder also facilitates project-level tooling support.  For example my Cursor IDE "workspace" needs to incorporate all 5 repos, and  the tool configuration information is best kept in the dev home folder, not in any of the repos.

An argument was initially considered to make the `seqweb` repo folder also be the home dev folder, since it sounds the most "generic".  However this would tend to abrade good seperation-of-concerns practice in several ways:
1. attracting more code/implementation/ops-oriented content (for example the SeqWeb CLI implementation) which by definition belongs in `seqwebcode`.
2. accumulating more local private dev-specific material within the scope of a Git-managed repo.  Although `.gitignore` provides a fine way to exempt such material, that also adds yet more things to manage.  Better is to just not have it entrained at all.
3. scope-creep, incorporating material outside the original, essential, driver for this repo in the first place, which was having a convenient Github-managed static web site.
4. the home dev folder intrinsically needs to reference multiple repos, in order to provide system-level coordination -- therefore no matter which other repo we put it in, it will be referencing things outside the "mission" of that repo.
An option might be to make yet another repo, but then almost all of its content would be private and thus unsharable anyway.  And then we'd have to name it, and `seqweb` needs to be reserved for public project-oriented resources (eg the website and docs).  We could call it `seqwebdev`, but then that seems confusingly redundant with `seqwebcode` (eg where should the CLI code go?)

Therefore we posit that the recommended practice should be to define a seqwebdev home folder not correlated with any repo.

This then raises the question of how to most efficiently bootstrap a new seqwebdev?  What is the simplest possible setup process, minimizing manual operations, editing and contact with internal implementation details?  Ideally setup should just involve at some point typing the paths to the repos (and perhaps a tiny bit more) just once into a config somewhere (where?) and then having everything "just work".  What should the quickest possible Quickstart instructions say?

