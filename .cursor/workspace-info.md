# SeqWeb Multi-Repo Workspace Setup

This workspace contains five repositories:

**seqwebcode** (main development) - Code, documentation, ontology, tools
**seqwebdata** (generated RDF data) - Output .ttl files from the pipeline
**oeisdata** (OEIS source corpus) - ~400,000 .seq files, read-only data resource
**seqweb** (project documentation) - Project documentation at https://www.seqweb.org/
**.github** (organization profile) - Controls the SeqWeb organization landing page

## Opening in Cursor

To open this workspace in Cursor:

1. Open Cursor
2. File → Open Folder
3. Select the `seqweb` directory
4. Cursor will automatically detect the workspace configuration

## Performance Notes

- **oeisdata** and **seqwebdata** are excluded from indexing due to large file counts
- Focus development work in **seqwebcode**
- The workspace configuration automatically handles exclusions

## Workspace Configuration

- `.cursor/settings.json` - Cursor workspace settings
- `.cursor/workspace.code-workspace` - VS Code workspace definition
- `.cursor/rules/` - Cursor AI rules for the project

## Development Focus

- Main development happens in `seqwebcode`
- Data repositories are for reference and output



## Repository URLs

- seqwebcode: https://github.com/seqweb/seqwebcode.git
- seqwebdata: https://github.com/seqweb/seqwebdata.git  
- oeisdata: https://github.com/oeis/oeisdata.git
- seqweb: https://github.com/seqweb/seqweb.git
- .github: https://github.com/seqweb/.github.git

## Folder Structure Examples

Different developers can use different folder structures:

**Example 1: Sibling directories**
```
~/projects/
├── seqwebcode/              # Main development repository
├── seqweb/                  # Project documentation
├── .github/                 # Organization profile
├── seqwebdata/              # Generated RDF data
└── oeisdata/                # OEIS source corpus
```

**Example 2: Custom organization**
```
~/Devo/
├── seqweb/seqwebcode/       # Main development repository
├── seqweb/seqweb/           # Project documentation
├── seqweb/.github/          # Organization profile
└── Data/
    ├── SeqWeb/seqwebdata/   # Generated RDF data
    └── OEIS/oeisdata/       # OEIS source corpus
``` 