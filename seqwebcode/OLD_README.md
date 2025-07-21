# seqwebcode

_**SeqWeb** is to the OEIS as DBpedia is to Wikipedia._<br>
Project level documentation may be found at the [SeqWeb Documentation](https://seqweb.github.io/seqweb/)

## Repository Overview
This **seqwebcode** repository contains the code, documentation, and ontology for converting OEIS sequence data into a semantic web knowledge graph.

### Related Repositories

- **oeisdata**: `git@github.com:oeis/oeisdata.git` - OEIS source corpus (~400,000 .seq files, read-only data resource)
- **seqwebdata**: `git@github.com:seqweb/seqwebdata.git` - Generated RDF data (.ttl files, output data)
- **seqwebcode**: `git@github.com:seqweb/seqwebcode.git` - This repository (code, docs, ontology, tools)
- **seqweb**: `git@github.com:seqweb/seqweb.git` - Project documentation repository (documentation at https://seqweb.github.io/seqweb/)
- **.github**: `git@github.com:seqweb/.github.git` - Organization profile repository (controls the SeqWeb organization landing page)

> **Note for Cursor users**: This workspace includes all repositories. The data repositories (oeisdata, seqwebdata) contain large numbers of files and should not be indexed. Focus development work in seqwebcode.

### Multi-Repo Workspace Setup

For development with the main repositories:

1. **Automatic setup**: Run `./tools/setup_workspace.sh` to clone the related repositories and configure the workspace
2. **Manual setup**: Clone the repositories manually and open `seqwebcode` in Cursor - the workspace configuration will automatically include the other repositories
3. **Workspace configuration**: The `.cursor/` directory contains workspace settings that exclude large data directories from indexing

#### Repository Path Configuration

Each developer can specify their own repository paths:

1. **Copy the template**: `cp config/env.sh config/env.local.sh`
2. **Edit paths**: Update `config/env.local.sh` with your repository locations
3. **Generate workspace**: Run `python3 tools/generate_workspace_config.py`

**Example configurations:**
```bash
# Option 1: Sibling directories (default)
export SEQWEBDATA_PATH="$(pwd)/../seqwebdata"
export OEISDATA_PATH="$(pwd)/../oeisdata"

# Option 2: Custom paths
export SEQWEBDATA_PATH="~/Devo/Data/SeqWeb/seqwebdata"
export OEISDATA_PATH="~/Devo/Data/OEIS/oeisdata"

# Option 3: Absolute paths
export SEQWEBDATA_PATH="/Users/yourname/projects/seqwebdata"
export OEISDATA_PATH="/Users/yourname/data/oeisdata"
```

The `config/env.local.sh` file is gitignored and should not be committed.

See `.cursor/workspace-setup.md` for detailed setup instructions.

## QuickStart

1. **Clone the repository:**
   ```bash
   git clone https://github.com/seqweb/seqwebcode.git
   cd seqwebcode
   ```

2. **Make the CLI executable:**
   ```bash
   chmod +x seqweb
   ```

3. **Set up the development environment:**
   ```bash
   ./seqweb setup
   ```

4. **Start using SeqWeb:**
   ```bash
   ./seqweb help
   ```

That's it! The `./seqweb` command is now available for development work.


## Project Structure

```
seqwebcode/
├── docs/                  # Documentation
│   ├── technical/            # Technical specifications, APIs, etc.
│   ├── process/              # Development processes, workflows
│   └── plans/                # Project plans, roadmaps, milestones
├── src/                   # Source code
│   ├── java/                 # Java implementations
│   ├── python/               # Python implementations  
│   ├── cl/                   # Common Lisp implementations
│   └── scripts/              # Shell scripts and utilities
├── ontology/                 # RDF ontology definitions and vocabularies
├── tests/                 # Test suites
│   ├── java/
│   ├── python/
│   └── cl/
├── data/                  # Local data files, samples
└── examples/              # Example usage and demonstrations
```

