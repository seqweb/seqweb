# SeqWeb Multi-Repo Workspace Setup

This workspace contains five repositories:

1. **seqwebcode** (main development) - This repository
2. **seqwebdata** (generated RDF data) - Path configured in `config/env.local.sh`
3. **oeisdata** (OEIS source corpus) - Path configured in `config/env.local.sh`
4. **seqweb** (project documentation) - Project documentation at https://www.seqweb.org/
5. **.github** (organization profile) - Controls the SeqWeb organization landing page

## Opening in Cursor

To open this workspace in Cursor:

1. Open Cursor
2. File → Open Folder
3. Select the `seqwebcode` directory
4. Cursor will automatically detect the workspace configuration

## Important Notes

- The data repositories (oeisdata, seqwebdata) contain large numbers of files and are excluded from indexing
- Focus development work in seqwebcode
- The workspace configuration automatically excludes large directories from search and file watching

## Manual Setup (if needed)

If the automatic workspace detection doesn't work:

1. Configure your repository paths in `config/env.local.sh`
2. Run `python3 tools/generate_workspace_config.py` to generate the workspace configuration
3. Open seqwebcode in Cursor - it should now detect the workspace configuration

## Repository Path Configuration

Each developer can specify their own repository paths:

1. Copy the template: `cp config/env.sh config/env.local.sh`
2. Edit the paths in `config/env.local.sh` to match your setup
3. Run `python3 tools/generate_workspace_config.py` to update the workspace configuration

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
├── seqwebdata/              # Generated RDF data
└── oeisdata/                # OEIS source corpus
```

**Example 2: Custom organization**
```
~/Devo/
├── seqweb/seqwebcode/       # Main development repository
└── Data/
    ├── SeqWeb/seqwebdata/   # Generated RDF data
    └── OEIS/oeisdata/       # OEIS source corpus
```

**Example 3: Absolute paths**
```
/Users/developer/projects/seqwebcode/
/Users/developer/data/seqwebdata/
/Users/developer/data/oeisdata/
```

**Example 4: Personal organization (mlb's setup)**
```
/Users/mlb/Devo/seqweb/seqwebcode/     # Main development repository
/Users/mlb/Devo/Data/SeqWeb/seqwebdata/ # Generated RDF data  
/Users/mlb/Data/OEIS/oeisdata/         # OEIS source corpus
/Users/mlb/Devo/seqweb/seqweb/         # Project wiki
/Users/mlb/Devo/seqweb/.github/        # Organization profile
```

This example shows a pattern where:
- Code repositories are kept in `/Users/mlb/Devo/[org]/[repo]`
- Data repositories are kept in `/Users/mlb/Devo/Data/[Org]/[repo]` or `/Users/mlb/Data/[Org]/[repo]`

Configure your paths in `config/env.local.sh` to match your setup. 