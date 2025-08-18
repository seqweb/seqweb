# SeqWeb Workspace Setup Guide

## Repository Structure

The SeqWeb project consists of multiple repositories that work together:

| Repository | Purpose | GitHub URL | Local Path Pattern |
|------------|---------|------------|-------------------|
| `seqwebcode` | Main development repository | `https://github.com/seqweb/seqwebcode.git` | `/path/to/dev/[org]/[repo]` |
| `seqwebdata` | Knowledge graph data snapshots | `https://github.com/seqweb/seqwebdata.git` | `/path/to/data/[Org]/[repo]` |
| `seqweb` | Project wiki repository | `https://github.com/seqweb/seqweb.git` | `/path/to/dev/[org]/[repo]` |
| `.github` | Organization profile | `https://github.com/seqweb/.github.git` | `/path/to/dev/[org]/[repo]` |
| `oeisdata` | OEIS data (external) | `https://github.com/oeis/oeisdata.git` | `/path/to/data/[Org]/[repo]` |

## Local Organization Patterns

Developers are free to organize their local repositories according to their preferences. Common patterns include:

- **Code repositories**: Keep in a development directory (e.g., `~/Devo/[org]/[repo]`)
- **Data repositories**: Keep in a separate data directory (e.g., `~/Data/[Org]/[repo]`)
- **External data**: Keep in organization-specific data directories

## Setup Instructions

1. Clone the repositories according to your preferred organization pattern
2. Ensure all repositories are accessible to your development environment
3. Configure any necessary environment variables or paths

## Personal Configuration

For persistent workspace mappings across development sessions, consider creating a local configuration file (e.g., `~/.cursor-workspace-config.json`) that maps workspace names to local paths.

This file should not be committed to version control as it contains personal local paths. 