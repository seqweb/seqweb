---
layout: default
title: SeqWeb Environment Management Architecture
permalink: /docs/environment_management/
---

# SeqWeb Environment Management Architecture

## Overview

This document outlines the architecture for managing development environments, dependencies, and persistent state across the SeqWeb polyglot system. The goal is to provide a unified, scalable approach that supports multiple languages while maintaining separation of concerns and enabling user customization.

**Note**: The command-line interface for environment management will be implemented using the new nested CLI architecture described in [New CLI Architecture](new_CLI_plans.md). This document focuses on the environment management strategy and structure.

## Core Principles

1. **Unified State Management**: All SeqWeb-managed state resides under a single `.state/` directory
2. **Separation of Concerns**: Code repositories contain only code; runtime artifacts live in the workspace
3. **Context Isolation**: Multiple language environments can coexist without interference
4. **Persistence**: Environments persist across sessions and focus shifts
5. **User Customization**: SeqWeb provides sensible defaults while respecting user preferences
6. **Scalability**: Patterns generalize across languages and tools

## Architecture Structure

### Directory Layout

```
seqwebdev home/
├── .state/                    # All SeqWeb-managed state
│   ├── seqvar/               # Key-value configuration store
│   │   └── seqvar.db         # SQLite database
│   ├── env/                  # Language-specific environments
│   │   ├── python/
│   │   │   ├── packages/     # Installed Python packages
│   │   │   ├── config/       # Python-specific configuration
│   │   │   └── activate.sh   # Context activation script
│   │   ├── java/
│   │   │   ├── libs/         # JAR files and dependencies
│   │   │   └── config/       # Java-specific configuration
│   │   └── cl/
│   │       ├── quicklisp/    # Quicklisp setup
│   │       └── config/       # CL-specific configuration
│   ├── data/                 # Shared datasets (if local)
│   └── tools/                # Other persistent tools/configs
├── seqweb.conf               # Repository locations and environment configs
└── [repository directories]   # seqweb, seqwebcode, etc.
```

### Key Components

#### **`.state/seqvar/`**
- **Purpose**: Centralized key-value configuration store
- **Implementation**: SQLite database (existing seqvar system)
- **Usage**: Repository locations, environment settings, user preferences
- **Access**: Via `seqwebdev get/set/vars` commands

#### **`.state/env/<language>/`**
- **Purpose**: Language-specific development environments
- **Structure**: Consistent across languages with language-appropriate subdirectories
- **Persistence**: Environments persist until explicitly modified
- **Isolation**: Each language environment is independent

#### **`.state/data/`**
- **Purpose**: Local copies of shared datasets when needed
- **Examples**: OEIS data snapshots, generated knowledge graphs
- **Management**: Controlled by SeqWeb, not user-managed

#### **`.state/tools/`**
- **Purpose**: Other persistent tools and configurations
- **Examples**: IDE workspace files, build artifacts, cache files

## Environment Setup Commands

### Command Structure

```bash
seqwebdev setup <component> [options]
```

### Component Types

#### **Language Environments**
```bash
seqwebdev setup python [options]     # Python development environment
seqwebdev setup java [options]       # Java development environment
seqwebdev setup cl [options]         # Common Lisp development environment
```

#### **Tools and Services**
```bash
seqwebdev setup cursor               # Cursor/VS Code workspace
seqwebdev setup allegrograph         # AllegroGraph database
seqwebdev setup fuseki               # Apache Jena Fuseki
```

### Python Environment Options

```bash
seqwebdev setup python                    # Basic setup with core dependencies
seqwebdev setup python --add-package pandas numpy  # Additional packages
seqwebdev setup python --dev-tools pytest black mypy  # Development tools
seqwebdev setup python --python-version 3.12  # Specific Python version
```

*Note: These commands will be implemented using the new nested CLI architecture described in `new_CLI_plans.md`. The `setup` command will auto-discover available subcommands and provide appropriate help and execution.*

## Context Activation

### How It Works

1. **`seqwebdev run`** detects the target file type
2. **Automatically activates** the corresponding language environment
3. **Executes the code** with full access to SeqWeb capabilities
4. **Restores previous context** when execution completes

### Benefits

- **Transparent**: Users don't need to manually activate environments
- **Isolated**: Doesn't interfere with other Python work
- **Consistent**: Same pattern across all languages
- **Automatic**: "Just works" when running SeqWeb code

## Migration Strategy

### Phase 1: Structure Establishment
1. Create new `.state/env/` directory structure
2. Migrate existing seqvar database to `.state/seqvar/`
3. Update code to use new paths

### Phase 2: CLI Architecture Implementation
1. Implement new nested CLI command system (see `new_CLI_plans.md`)
2. Create `seqwebdev setup python` command using new architecture
3. Test with RDFLib installation
4. Validate context activation

### Phase 3: Command Expansion
1. Add support for other languages (Java, Common Lisp)
2. Implement additional setup commands (Fuseki, Cursor)
3. Extend to other verb groups (start, stop, status)
4. Add user customization features

## User Customization

### Default vs. Custom

- **SeqWeb provides**: Sensible defaults and convenience setup
- **Users can customize**: Environment preferences and additional tools
- **SeqWeb respects**: Existing user configurations
- **Non-intrusive**: Users can ignore SeqWeb setup if preferred

### Customization Examples

- **Package preferences**: Additional libraries beyond SeqWeb requirements
- **Development tools**: Testing frameworks, code formatters, linters
- **IDE integration**: Workspace configurations, debugging setups
- **Performance tuning**: Compiler flags, runtime options

## Future Considerations

### Language Expansion
- **JavaScript/Node.js**: npm/yarn package management
- **Rust**: Cargo and crates.io integration
- **Go**: Go modules and dependency management

### Tool Integration
- **Containerization**: Docker and container orchestration
- **Cloud Services**: AWS, GCP, Azure integration
- **CI/CD**: GitHub Actions, GitLab CI integration

### Community Development
- **Plugin System**: Community-contributed commands using nested CLI architecture
- **Configuration Sharing**: User preference templates
- **Documentation**: Community-maintained setup guides
- **Command Extensions**: Easy addition of new commands and subcommands

## Implementation Notes

### File Permissions
- **`.state/`** directories should be user-writable
- **Activation scripts** should be executable
- **Package directories** should follow language-specific conventions

### Error Handling
- **Graceful degradation**: Fall back to system defaults if setup fails
- **Clear messaging**: Inform users what's happening and what to do
- **Recovery options**: Provide ways to reset or repair environments

### Performance
- **Lazy loading**: Only activate environments when needed
- **Caching**: Cache environment information for faster access
- **Parallel setup**: Support concurrent environment setup for multiple languages
