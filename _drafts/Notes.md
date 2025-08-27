# Notes


Some informal analysis (thinking out loud):
 Here's some vocab/dramatis personae, with current bindings:
1. The *universal* (bash) SeqWeb CLI entry point: `seqwebdev/seqwebdev`.
2. The SeqWeb (Python) *Implementation* entry point AND initializer: `seqwebcode/seqwebdev`.
3, The CLI command *dispatcher*: `seqwebcode/src/python/cli/cli.py`.
4. The (bash) *bootstrap* code that the Quickstart has you `curl`: `seqwebcode/bootstrap`.
5. The *seqvar* "library": `seqwebcode/src/python/seqvar/'.
6. The *workspace* "library": `seqwebcode/src/python/workspace/`.
7. The (SeqWeb) *CLI* "library": `seqwebcode/src/python/cli/`.
8. The SeqWeb dev home folder (seqwebdev): location variable, established dynamically


The Quickstart process establishes (1) by having the user execute the`curl` which reads (4) off of Github and pipes it to bash for execution.