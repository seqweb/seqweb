---
layout: default
title: New CLI Architecture - Nested Command System
permalink: /docs/new-CLI-plans/
---

# New CLI Architecture: Nested Command System

## Overview

This document outlines the design and implementation plan for extending the SeqWeb CLI to support nested subcommands with unlimited depth. The new system will provide a powerful, scalable command structure while maintaining the clean, discoverable architecture of the current CLI.

## Design Principles

1. **Pure Discovery-Based**: No manual subcommand declarations - directory structure is the source of truth
2. **Orthogonal Functionality**: Subcommands are just functions that happen to be discoverable
3. **Zero Redundancy**: Directory structure = command structure, no manual sync needed
4. **Unlimited Nesting**: Support for commands like `seqwebdev setup python packages install rdflib`
5. **Verb-Oriented Organization**: Commands grouped by action (setup initially, expandable later)
6. **Performance Optimized**: Minimal directory scanning, efficient command resolution
7. **Backward Compatible**: All existing commands continue working unchanged during transition

## Architecture

### Directory Structure

```
commands/
├── __init__.py
├── cursor.py             # seqwebdev cursor
├── dict.py               # seqwebdev dict
├── get.py                # seqwebdev get
├── help.py               # seqwebdev help
├── run.py                # seqwebdev run
├── set.py                # seqwebdev set
├── vars.py               # seqwebdev vars
└── setup/                # seqwebdev setup
    ├── __init__.py       # Auto-generated group command
    ├── python.py         # setup python (initial implementation)
    └── java.py           # setup java (stub for future)
```

### Command Types

#### **Top-Level Commands**
- **Direct commands**: `vars.py`, `run.py` - simple, single-purpose commands
- **Group commands**: `setup/` - directory containing subcommands (expandable to other groups later)

#### **Subcommands**
- **Pure functionality**: Each subcommand is just a function that happens to be discoverable
- **No structural knowledge**: Subcommands don't know they're part of a command hierarchy
- **Standard interface**: All inherit from `BaseCommand` with consistent properties

## Implementation Patterns

### 1. Enhanced BaseCommand Class

```python
class BaseCommand:
    """Base class for all commands"""
    
    @property
    def name(self) -> str:
        """Command name (e.g., 'setup', 'python', 'start')"""
        return self.__class__.__name__.lower().replace('command', '')
    
    @property
    def description(self) -> str:
        """Brief description for help listings"""
        return "No description provided"
    
    @property
    def help_text(self) -> str:
        """Detailed help text"""
        return f"Usage: seqwebdev {self.name}\n  {self.description}"
    
    @property
    def has_subcommand_group(self) -> bool:
        """Performance optimization: avoid re-scanning directory"""
        return False
    
    def execute(self, args):
        """Execute the command with given arguments"""
        raise NotImplementedError("Subclasses must implement execute")
```

### 2. Auto-Discovery Registry

```python
def get_available_commands() -> Dict[str, Type]:
    """Get all available commands, supporting nested structure"""
    commands = {}
    commands_dir = Path(__file__).parent / "commands"
    
    # Scan for top-level commands
    for item in commands_dir.iterdir():
        if item.is_dir() and not item.name.startswith('_'):
            # Directory = command group with subcommands
            commands[item.name] = load_command_group(item)
        elif item.is_file() and item.suffix == '.py' and item.name != '__init__.py':
            # File = top-level command
            commands[item.stem] = load_single_command(item)
    
    return commands

def load_command_group(group_dir: Path) -> Type:
    """Load a command group by scanning its subdirectory"""
    # Auto-generate group command that discovers subcommands dynamically
    return create_auto_group_command(group_dir)
```

### 3. Auto-Generated Group Commands

```python
def create_auto_group_command(group_dir: Path) -> Type:
    """Create a group command that auto-discovers subcommands"""
    
    class AutoGroupCommand(BaseCommand):
        def __init__(self):
            self._group_dir = group_dir
            self._subcommands = self._discover_subcommands()
        
        def _discover_subcommands(self) -> Dict[str, Type]:
            """Discover subcommands by scanning directory"""
            subcommands = {}
            for py_file in self._group_dir.glob("*.py"):
                if py_file.name != "__init__.py":
                    subcommand_name = py_file.stem
                    subcommand_class = load_single_command(py_file)
                    subcommands[subcommand_name] = subcommand_class
            return subcommands
        
        @property
        def name(self) -> str:
            return group_dir.name
        
        @property
        def description(self) -> str:
            return f"Manage {group_dir.name} operations"
        
        @property
        def help_text(self) -> str:
            base_help = f"Usage: seqwebdev {self.name} <subcommand>\n\nAvailable subcommands:\n"
            for name, cmd_class in self._subcommands.items():
                # Get description from subcommand class
                desc = getattr(cmd_class(), 'description', 'No description')
                base_help += f"  {name:<15} {desc}\n"
            return base_help
        
        @property
        def has_subcommand_group(self) -> bool:
            """Performance optimization: avoid re-scanning directory"""
            return True
        
        def execute(self, args):
            if len(args) < 2:
                print(self.help_text)
                return
            
            subcommand = args[1]
            if subcommand in self._subcommands:
                # Execute subcommand with remaining args
                subcommand_class = self._subcommands[subcommand]
                subcommand_instance = subcommand_class()
                subcommand_instance.execute(args[2:])
            else:
                print(f"Unknown {self.name} subcommand: {subcommand}")
                print(f"Run 'seqwebdev help {self.name}' for available options")
    
    return AutoGroupCommand
```

### 4. Subcommand Implementation Example

```python
# commands/setup/python.py - pure functionality, no command structure knowledge
from ..base import BaseCommand

class SetupPythonCommand(BaseCommand):
    """Setup Python development environment"""
    
    @property
    def name(self) -> str:
        return "python"  # Just the subcommand name, not full path
    
    @property
    def description(self) -> str:
        return "Setup Python development environment with required packages"
    
    @property
    def help_text(self) -> str:
        return """Usage: seqwebdev setup python [options]
  Setup Python development environment for SeqWeb development.
  
  Options:
    --add-package PKG    Install additional packages
    --dev-tools          Install development tools (pytest, black, etc.)
    --python-version V   Use specific Python version"""
    
    def execute(self, args):
        # Pure functionality - no knowledge of being a subcommand
        print("Setting up Python environment...")
        # ... actual setup logic
```

## Command Resolution

### Path Resolution Algorithm

1. **Parse command path**: `seqwebdev start fuseki` → `["start", "fuseki"]`
2. **Resolve top-level**: `start` → `commands/start/__init__.py`
3. **Resolve subcommand**: `fuseki` → `commands/start/fuseki.py`
4. **Execute with remaining args**: `fuseki.py.execute(args[2:])`

### Unlimited Nesting Support

The system can handle commands of any depth:
- `seqwebdev setup python` → `commands/setup/python.py`
- `seqwebdev setup python packages` → `commands/setup/python/packages.py`
- `seqwebdev setup python packages install` → `commands/setup/python/packages/install.py`

## Help System

### Command Help Levels

1. **`seqwebdev help`**: Lists all top-level commands with descriptions
2. **`seqwebdev help setup`**: Shows setup command description + list of subcommands
3. **`seqwebdev help setup python`**: Shows detailed help for specific subcommand

### Help Output Examples

```bash
$ seqwebdev help
Available commands:
  cursor   Manage Cursor/VS Code workspace file for SeqWeb development
  dict     Display seqvar key-value pairs as a dictionary
  get      Get a seqvar value by key
  help     Show help information for SeqWeb commands
  run      Execute a program with proper SeqWeb environment setup
  set      Set a seqvar value by key
  setup    Manage setup operations
  vars     Display all contents of the seqvar database

$ seqwebdev help setup
Usage: seqwebdev setup <subcommand>

Available subcommands:
  python   Setup Python development environment with required packages
  java     Setup Java development environment (stub for future)

$ seqwebdev help setup python
Usage: seqwebdev setup python [options]
  Setup Python development environment for SeqWeb development.
  
  Options:
    --add-package PKG    Install additional packages
    --dev-tools          Install development tools (pytest, black, etc.)
    --python-version V   Use specific Python version
```

## Migration Strategy

### Phase 1: Foundation (Current)
1. ✅ **New architecture documented** - This document
2. ✅ **Environment management structure** - `.state/env/` directories created
3. ✅ **Seqvar migration** - Database moved to `.state/seqvar/seqvar.db`

### Phase 2: CLI Restructure
1. **Rename `setup.py` to `foothold.py`** - Avoid naming conflict
2. **Create new registry with auto-discovery** - Support nested commands
3. **Create nested command directories** - `commands/setup/` initially
4. **Implement auto-generated group commands** - Dynamic subcommand discovery
5. **Preserve existing commands** - All current commands continue working unchanged

### Phase 3: Command Migration
1. **Keep existing commands unchanged** - All current commands continue working as-is
2. **Test nested command discovery** and execution for new setup command
3. **Verify help system** works at all levels
4. **Implement `seqwebdev setup python`** - Python environment setup with RDFLib

### Phase 4: New Functionality
1. **Add other setup commands** - Java (from stub), Fuseki, Cursor
2. **Consider other verb groups** - Start, stop, status commands (future consideration)
3. **Add "all" subcommands** for convenience operations (optional)
4. **Evaluate grouping existing commands** - Decide if current commands should be organized into groups

## Benefits

### **For Developers**
- **Zero maintenance**: Adding new commands is just adding files
- **Clean separation**: Each command type has its own file
- **Consistent patterns**: All commands follow the same interface
- **Easy testing**: Commands can be tested independently

### **For Users**
- **Intuitive organization**: Commands grouped by action, not object
- **Powerful nesting**: Support for complex command chains
- **Comprehensive help**: Help available at every level
- **Future extensible**: Easy to add new commands and groups

### **For System**
- **Scalable architecture**: Supports unlimited command complexity
- **Performance optimized**: Minimal directory scanning
- **Memory efficient**: Commands loaded only when needed
- **Maintainable**: Clear separation of concerns

## Future Considerations

### **Plugin System**
- **Community commands**: Allow external command contributions
- **Dynamic loading**: Load commands from external sources
- **Configuration**: User-configurable command discovery paths

### **Advanced Features**
- **Command aliases**: Short forms for common commands
- **Tab completion**: Shell completion for nested commands
- **Command history**: Track and suggest frequently used commands
- **Batch operations**: Execute multiple commands in sequence

### **Integration**
- **IDE support**: Better integration with development environments
- **CI/CD integration**: Commands for automated deployment
- **Cloud services**: Commands for cloud infrastructure management

## Implementation Notes

### **File Naming Conventions**
- **Command files**: `verb.py` (e.g., `start.py`, `stop.py`)
- **Group directories**: `verb/` (e.g., `start/`, `stop/`)
- **Subcommand files**: `object.py` (e.g., `fuseki.py`, `python.py`)

### **Error Handling**
- **Graceful degradation**: Fall back to system defaults if setup fails
- **Clear messaging**: Inform users what's happening and what to do
- **Recovery options**: Provide ways to reset or repair environments

### **Performance Considerations**
- **Lazy loading**: Only scan directories when needed
- **Caching**: Cache command discovery results
- **Minimal I/O**: Avoid unnecessary file system operations

## Conclusion

This new CLI architecture provides a powerful, scalable foundation for the SeqWeb development system. By leveraging pure discovery-based command resolution and maintaining orthogonal functionality, we create a system that's both powerful and maintainable.

The key insight is that **directory structure equals command structure** - there's no need for manual declarations or maintenance. The system automatically adapts to new commands and subcommands as they're added, while providing users with an intuitive, powerful interface for managing all aspects of SeqWeb development.

The migration path is designed to be incremental and non-disruptive, allowing us to build on a proven foundation while extending functionality in a controlled, testable manner.
