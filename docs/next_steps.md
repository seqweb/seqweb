# Next Steps After Setup

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

Edit `seqweb.conf` to change repository paths or add new repositories and other SeqWeb configuration info.

```ini
[repos]
seqweb=/path/to/your/seqweb-repo
seqwebcode=/path/to/your/seqwebcode-repo
# Add other repos as needed
```

## Setting up your IDE:

### Cursor/VS Code Workspace

```bash
seqwebdev cursor
```

This starts up Cursor in your SeqWeb dev home directory.  It first ensures there's a Cursor workspace configuration file (`seqwebdev.code-workspace`) that knows about the repositories in `seqweb.conf`along with other useful settings.  The system will either generate the file from scratch, or respectfully modify an existing one. 


[everything below here is still WIP]

### Other IDEs

The SeqWeb system will support other IDEs as well.  Run `seqwebdev setup help` for options.

## Verification

Test that everything is working:

```bash
seqwebdev status          # Check repository status
seqwebdev help            # Show available commands
seqwebdev setup status    # Check workspace configuration
```


## Troubleshooting

- **Permission denied**: Make sure you have write access to your dev home directory
- **Command not found**: Restart your terminal or run `source ~/.bashrc`
- **Repo not found**: Check the paths in `seqweb.conf`

## Learning More

- Read the [SeqWeb Overview](seqweb_overview) for project context
- Explore the [Systems Design](systems_design) for architecture details
- Check out the [Polyglot Pipeline](polyglot_pipeline) for more development patterns

## Getting Help

- Run `seqwebdev help` for CLI assistance
- Check the [project documentation](https://www.seqweb.org/)
- Join the [community discussions](https://github.com/seqweb/seqweb/discussions)
