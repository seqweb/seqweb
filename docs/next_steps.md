---
layout: default
title: Next Steps After Quickstart
permalink: /docs/next_steps/
---

# Next Steps After Quickstartß

This document covers what to do after completing the [Quickstart](quickstart) setup process.

## After completing the quickstart:
- You have an initialized **SeqWeb development** environment, centered in _your chosen directory_.
- The directory path is stored as the value of the env var `SEQWEBDEV_HOME`.
- Your configuration info (eg repo locations) is saved in the file `seqweb.conf` there.
- You can invoke the SeqWeb CLI using the `seqwebdev` command in your shell.  Type
```bash
seqwebdev help
```
for additional info.


## Configuration & Customization

You should now edit `seqweb.conf` if you need to change repository paths or add new repositories.
You can also add/modify other SeqWeb configuration info sections if you wish.

```ini
[repos]
seqweb="/path/to/your/seqweb-repo"
seqwebcode="/path/to/your/seqwebcode-repo"
# Add other repos as needed
```

## SeqWeb development environment persistent key-value store: `seqvar`

The SeqWeb system uses a simple persistent "environment variable" facilty referred to as the _`seqvar` store_.  In addition to polyglot programmatic access to get and set these values, code can also retrieve them as dictionaires filtered by key string patterns.  The CLI supports all these too, as well as a command to dump the entire store.  Try
```bash
seqwebdev vars
```
to see the "online" version of your configuration along with other data.

(The `seqvar` store is implemented as a SQLite database whose backing files are in the `.state/` subdirectory of the `seqwebdev` home folder.)


## Setting up your IDE:

### Cursor/VS Code Workspace

```bash
seqwebdev cursor
```

This ensures there's a Cursor workspace configuration file (`seqwebdev.code-workspace`) that knows about the repositories in `seqweb.conf`along with other useful settings.  The system will either generate the workspace file from scratch, or respectfully modify an existing one. It uses information from the `seqvar` store to create the augmented workspace configuration file.

### Other IDEs
***This is currently still WIP***<br>
The SeqWeb system will support other IDEs as well.  Run `seqwebdev setup help` for options.


## Learning More
Check the [online project documentation](https://www.seqweb.org/), in particular:
- Read the [SeqWeb Overview](seqweb_overview) for project context
- Explore the [Systems Design](systems_design) for architecture details
- Check out the [Polyglot Pipeline](polyglot_pipeline) for ETL development patterns

## Getting Help
- Run `seqwebdev help` for CLI hints
- To reach the SeqWeb dev community and admins, see the project's [Contact](contact) page.
