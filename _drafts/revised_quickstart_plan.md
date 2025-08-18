# Revised Quickstart Plan - Bootstrap Process Background

## Current Bootstrap Status
- Environment variable SEQWEBDEV_HOME is set and exported to .bashrc
- System needs to find the path to seqwebcode repo (required for downstream steps)

## Repository Requirements
- **seqwebcode**: Required (contains SeqWeb CLI implementation)
- **seqweb**: Recommended
- **seqwebdata**: Optional
- **oeisdata**: Optional
- **.github**: Rarely needed (seqweb.org admins only)

## Architecture Notes
- Configuration interaction details mediated by "wizard" program (complicated module with tricky requirements)
- seqwebcode repo required because it contains SeqWeb CLI implementation
- CLI entry point is `cli.py` in `seqwebcode/tools/`
- CLI support code in `lib/` folder
- Individual top-level operations implemented in `commands/` folder

## Bootstrap Requirements
- Locate repositories and register them in `seqweb.conf`
- Modify .bashrc to alias shell `seqweb` command to CLI entry point
- Users should be able to type `seqweb <command name> <command args>` (similar to git command)
- May need to "chmod x" something (TBD)
