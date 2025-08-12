# SeqWeb Developer Quickstart

This guide will quickly get you up and running with the SeqWeb development environment.

## Prerequisites

- Git installed and configured
- Bash shell (macOS Terminal, Linux, WSL, etc.)
- Basic familiarity with command line

## Quick Setup Steps

### 1. Clone required repositories

You'll need some or all of the following repositories available on your system:

- **seqwebcode** (required): Main development repository with tools and pipeline
- **seqweb** (recommended): Project documentation and website
- **seqwebdata** (optional): Generated RDF data files
- **oeisdata** (optional): OEIS source data corpus
- **.github** (SeqWeb admin only): misc. Github `seqweb.org` project collateral

Clone the required repositories using standard Git commands. For example:

```bash
git clone https://github.com/seqweb/seqwebcode.git ~/Devo/Code/seqwebcode
git clone https://github.com/seqweb/seqweb.git ~/Devo/seqweb/seqweb
```

### 2. Establish your SeqWeb Development Home directory

For example:
```bash
mkdir ~/Devo/seqweb
cd ~/Devo/seqweb
```

You can use any directory name and location you like.  

After the bootstrap process is complete this location will be available as the value of the environment variable `SEQWEBDEV_HOME`.

_Note:_ By design the SeqWeb dev home folder does **not** contain a repo; instead it acts as an operational hub from which you can configure and coordinate your multi-repo SeqWeb development and work environment.  In particular after bootstrapping it will contain the SeqWeb system's master multi-repo configuration file, `seqweb.conf`.

### 3. Run the **bootstrap** script

Copy and execute the following one-line command:

```bash
curl -s https://raw.githubusercontent.com/seqweb/seqwebcode/main/bootstrap | bash
```

The bootstrap script will then:
- Detect the current directory and record it as the SeqWeb dev home
- Initialize the shared project-level `seqweb.conf` configuration file in that folder
- Run a configuration wizard to populate the repository locations into `seqweb.conf`.
- Complete the setup process including CLI configuration

## What Happens Next

After completing these steps, you'll have a working SeqWeb development environment. See [Next Steps](next_steps.md) for what to do next. 