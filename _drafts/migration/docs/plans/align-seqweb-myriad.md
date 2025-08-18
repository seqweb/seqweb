# Plan: Align SeqWeb with Myriad Configuration Pattern

## Overview

This document outlines the plan to align SeqWeb's configuration system with the Myriad pattern, which provides a clean separation between user-configurable atomic parameters and system-derived environment variables.

## Current State Analysis

### Myriad Pattern (Reference)
- **`myriad.conf`**: Single atomic configuration file with user-editable parameters
- **`config/env.sh`**: Pure derivation engine that sources `myriad.conf` and computes all environment variables
- **`myriad` CLI**: Sets `MYRIAD_HOME` and sources `config/env.sh` before executing any command
- **Simple derivation**: `env.sh` contains mostly one-line exports and concatenations
- **Self-documenting**: `env.sh` serves as reference for all available environment variables

### SeqWeb Current State
- **`config/env.sh`**: Mixed user configuration and system logic
- **`config/env.local.sh`**: User-specific overrides (complex)
- **`seqweb` CLI**: Python-based with environment setup
- **Complex configuration**: Multiple files, unclear separation of concerns

## Target State

### 1. **Create `config/seqweb.conf` - Single Atomic Configuration**
```bash
# config/seqweb.conf

### Atomic SeqWeb configuration dictionary

# TODO: REVIEW AND UPDATE THESE SETTINGS FOR YOUR LOCAL ENVIRONMENT
# These values are specific to the current developer's setup and should be modified
# to match your local environment.

# Repository paths (user-configurable)
SEQWEBDATA_PATH="$HOME/Devo/Data/SeqWeb/seqwebdata"
OEISDATA_PATH="$HOME/Devo/Data/OEIS/oeisdata"

# Service configuration (anticipatory)
SEQWEB_HTTP_PORT=8080
```

### 2. **Simplify `config/env.sh` - Pure Derivation Engine**
```bash
# config/env.sh

### Load atomic configuration data
set -o allexport
source "$SEQWEB_HOME/config/seqweb.conf"
set +o allexport

### Derive additional environment bindings

# Core paths (derived from atomic values)
export SEQWEB_HOME="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
export SEQWEBDATA_PATH="$SEQWEBDATA_PATH"
export OEISDATA_PATH="$OEISDATA_PATH"

# /seqweb file tree paths
export SEQWEB_DATA_DIR="$SEQWEB_HOME/data"
export SEQWEB_INTERMEDIATE_DIR="$SEQWEB_DATA_DIR/intermediate"
export SEQWEB_OUTPUT_DIR="$SEQWEB_DATA_DIR/output"
export SEQWEB_LOGS_DIR="$SEQWEB_DATA_DIR/logs"
export SEQWEB_TMP_DIR="$SEQWEB_HOME/tmp"
export SEQWEB_COMMAND_DIR="$SEQWEB_HOME/tools/commands"

# Service URLs
export SEQWEB_HTTP_URL="http://localhost:${SEQWEB_HTTP_PORT}"

# Pipeline configuration (anticipatory)
export SEQWEB_BOX_FORMAT="json"

# Language runtimes (anticipatory - add when implementing polyglot pipeline)
# export SEQWEB_JAVA="$SEQWEB_JAVA"
# export SEQWEB_PYTHON="$SEQWEB_PYTHON"
# export SEQWEB_CL="$SEQWEB_CL"
```

### 3. **Update `seqweb` CLI Command**
```bash
#!/bin/bash

# Set SEQWEB_HOME to the directory containing this script
export SEQWEB_HOME="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Source environment configuration first
source "$SEQWEB_HOME/config/env.sh"

# Function to list available commands
list_available_commands() {
    echo "  Available commands:"
    # List commands from tools/commands/
}

# Function to show usage
show_usage() {
    if [[ -z "$1" ]]; then
        echo "❌ Missing command"
    else
        echo "❌ Unknown command: $1"
    fi
    echo "  Run 'seqweb help' for a list of available commands"
}

# Main command processing
if [[ $# -eq 0 ]]; then
    echo "🧬 SeqWeb: OEIS Knowledge Graph Development 🧬"
    show_usage
    exit 1
fi

command=$1
shift

# Execute the command using Python command system
python3 "$SEQWEB_HOME/tools/cli.py" "$command" "$@"
```

### 4. **Simplify Setup Command**
```python
def execute(self, args):
    print("🧬 Setting up SeqWeb development environment...")
    
    # Make seqweb executable
    self._make_executable()
    
    # Create basic data directories
    self._create_data_directories()
    
    # Generate workspace configuration
    self._generate_workspace_config()
    
    print("✅ Setup complete!")
    print("💡 Edit config/seqweb.conf to configure repository paths")
```

### 5. **Add `show` Command (Myriad-style)**
```python
class ShowCommand(BaseCommand):
    def execute(self, args):
        if args and args[0] == "config":
            self._show_config()
        elif args and args[0] == "env":
            self._show_environment()
        else:
            self._show_all()
    
    def _show_config(self):
        print("🧬 SeqWeb Configuration:")
        print(f"  SEQWEB_HOME: {os.environ.get('SEQWEB_HOME')}")
        print(f"  SEQWEBDATA_PATH: {os.environ.get('SEQWEBDATA_PATH')}")
        print(f"  OEISDATA_PATH: {os.environ.get('OEISDATA_PATH')}")
    
    def _show_environment(self):
        print("🧬 SeqWeb Environment Variables:")
        for key, value in os.environ.items():
            if key.startswith('SEQWEB_'):
                print(f"  {key}: {value}")
```

### 6. **Update Workspace Generation**
```python
def get_repo_paths():
    """Get repository paths from seqweb.conf"""
    config_file = Path(__file__).parent.parent / "config" / "seqweb.conf"
    
    if not config_file.exists():
        raise FileNotFoundError("config/seqweb.conf not found")
    
    # Parse seqweb.conf (simple key=value format)
    config = {}
    with open(config_file, 'r') as f:
        for line in f:
            line = line.strip()
            if line and not line.startswith('#') and '=' in line:
                key, value = line.split('=', 1)
                config[key.strip()] = value.strip().strip('"')
    
    seqwebdata_path = os.path.expanduser(config.get('SEQWEBDATA_PATH', ''))
    oeisdata_path = os.path.expanduser(config.get('OEISDATA_PATH', ''))
    
    return seqwebdata_path, oeisdata_path
```

## Implementation Steps

### Phase 1: Core Configuration (Current)
1. ✅ Create `config/seqweb.conf` with minimal atomic parameters
2. 🔄 Update `config/env.sh` to source and derive from `seqweb.conf`
3. 🔄 Update `seqweb` CLI to follow Myriad pattern
4. 🔄 Update workspace generation to read `seqweb.conf` directly

### Phase 2: Command System
5. 🔄 Simplify `setup` command to essential operations only
6. 🔄 Add `show` command for configuration inspection
7. 🔄 Update all commands to use new environment system

### Phase 3: Cleanup and Documentation
8. 🔄 Remove `config/env.local.sh` and related complexity
9. 🔄 Update documentation to emphasize single `seqweb.conf` file
10. 🔄 Add `config/README.md` explaining the configuration system

## File Structure Changes

```
seqwebcode/
├── seqweb                   # CLI entry point (Myriad-style)
├── config/
│   ├── seqweb.conf         # Single atomic configuration
│   ├── env.sh              # Pure derivation from seqweb.conf
│   └── README.md           # Configuration documentation
├── tools/
│   ├── cli.py              # Python command dispatcher
│   ├── generate_workspace_config.py  # Read seqweb.conf directly
│   └── commands/
│       ├── setup.py        # Simplified setup
│       ├── show.py         # New: display configuration
│       ├── help.py         # Updated help
│       └── workspace.py    # Updated to use seqweb.conf
```

## Benefits of Myriad Alignment

- **Single atomic configuration**: Only `seqweb.conf` needs user editing
- **Self-documenting env.sh**: Shows all available environment variables
- **Simple derivation**: No complex logic, just concatenation and expansion
- **Consistent pattern**: Follows established Myriad approach
- **Clear separation**: User config vs. system logic
- **Team-friendly**: Familiar pattern for developers

## Migration Strategy

1. Create `config/seqweb.conf` with current values from your environment
2. Update `config/env.sh` to source and derive from `seqweb.conf`
3. Update `seqweb` CLI to follow Myriad pattern
4. Update all tools to read from `seqweb.conf` directly
5. Remove `config/env.local.sh` and related complexity
6. Add `show` command for configuration inspection
7. Update documentation and examples

## Future Considerations

- **Language runtimes**: Add `SEQWEB_JAVA`, `SEQWEB_PYTHON`, `SEQWEB_CL` when implementing polyglot pipeline
- **Logging**: Add `SEQWEB_LOG_LEVEL` when implementing logging system
- **Advanced configuration**: Consider template-based configuration for different environments
- **Validation**: Add configuration validation and error checking

## Notes

- This plan preserves all existing functionality while simplifying the configuration system
- The polyglot pipeline architecture remains unchanged
- All tools and commands will continue to work with the new configuration system
- The workspace generation will be more reliable and consistent 