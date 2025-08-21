# SeqWeb Developer Quickstart

This guide will quickly get you up and running with the SeqWeb development environment.

## Prerequisites

- Git installed and configured
- Bash shell (macOS Terminal, Linux, WSL, etc.)
- Basic familiarity with command line

## Quick Setup Steps

### 1. Establish your SeqWeb Development Home directory

For example:
```bash
mkdir ~/Devo/seqweb
cd ~/Devo/seqweb
```

You can use any directory name and location you like.  

After the bootstrap process is complete this location will be available as the value of the environment variable `SEQWEBDEV_HOME`.

_Note:_ By design the SeqWeb dev home folder does **not** contain a repo; instead it acts as an operational hub from which you can configure and coordinate your multi-repo SeqWeb development and work environment.  In particular after bootstrapping it will contain the SeqWeb system's master multi-repo configuration file, `seqweb.conf`, and the entry point for the SeqWeb CLI.

### 2. Run the **bootstrap** script

Copy and execute the following one-line command:

```bash
curl -s https://raw.githubusercontent.com/seqweb/seqwebcode/main/bootstrap | bash
```

The bootstrap script will then:
- Detect the current directory and record it as the SeqWeb dev home
- If needed, initialize a shared project-level `seqweb.conf` configuration file in that folder
- Complete the setup process including establishing the SeqWeb CLI 

### 3. Clone and register repositories if needed.

You'll need some or all of the following repositories to be present on your system:

- **seqwebcode** (required): Main development repository with tools and pipeline
- **seqweb** (recommended): Project documentation and website
- **seqwebdata** (optional): Generated RDF data files
- **oeisdata** (optional): OEIS source data corpus
- **.github** (SeqWeb admin only): misc. Github `seqweb.org` project collateral

Clone any needed repositories not already present using standard Git commands.  For example:

```bash
git clone https://github.com/seqweb/seqwebcode.git
git clone https://github.com/seqweb/seqweb.git 
```
You may clone them anywhere you'd like.  The default SeqWeb config assumes they are in subdirectories of the SeqWeb dev home (as though the `git clone` commands were issued there). 

- _However_ **if they are located elsewhere**, you should now edit `seqweb.conf` to reflect this.
You need not modify anything else in the file, such as entries for repos or features you aren't (yet) using.

### 4. Start the SeqWeb CLI

To begin you can just type the minimal SeqWeb CLI command

```bash
seqwebdev
```
The CLI will ensure that the SeqWeb dev environment is established then respond in a sprightly manner.

## Happy SeqWeb Development!

After completing these quickstart steps, you'll have a working SeqWeb development environment. See [Next Steps](next_steps) for further suggestions. 
