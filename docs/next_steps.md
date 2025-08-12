# Next Steps After Setup

This document covers what to do after completing the [Quickstart](quickstart.md) setup process.

## What You Now Have

After completing the quickstart, you'll have:
- The `seqweb` command available in your shell
- Your configuration saved in `seqweb.conf`
- A working SeqWeb development environment

## Setting Up Your IDE

### Cursor/VS Code Workspace

```bash
seqweb setup cursor
```

This generates a workspace configuration file that includes all your repositories.

### Other IDEs

The CLI supports other IDEs as well. Run `seqweb setup help` for options.

## Verification

Test that everything is working:

```bash
seqweb status          # Check repository status
seqweb help            # Show available commands
seqweb setup status    # Check workspace configuration
```

## Customization

Edit `seqweb.conf` to change repository paths or add new repositories:

```ini
[repos]
seqwebcode=/path/to/your/seqwebcode
seqweb=/path/to/your/seqweb
# Add other repos as needed
```

## Troubleshooting

- **Permission denied**: Make sure you have write access to your dev home directory
- **Command not found**: Restart your terminal or run `source ~/.bashrc`
- **Repo not found**: Check the paths in `seqweb.conf`

## Learning More

- Read the [SeqWeb Overview](seqweb_overview.md) for project context
- Explore the [Systems Design](systems_design.md) for architecture details
- Check out the [Polyglot Pipeline](polyglot_pipeline.md) for development patterns

## Getting Help

- Run `seqweb help` for CLI assistance
- Check the [project documentation](https://www.seqweb.org/)
- Join the [community discussions](https://github.com/seqweb/seqweb/discussions)
