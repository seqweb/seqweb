# Chaining CLI
Computer activities are commonly initiated by presenting directive lines of text of the form
```
    <command name> [optional command args]
```
to a _command line interpreter_ or _CLI_.  Here we present a handy elaboration of this pattern called a _chaining_ CLI, whose directives take the form
```
    <command name> <sub-command name> <sub-sub-command name> … [optional command args]
```
where there may be any number of levels of _sub*_ command names in the _chain_ of commands before the args (including none).

An example, taken from a system with a global key-value store, would be
```bash
seqwebdev seqvar set "key" "value"
```
where the chain of commands consists of `seqwebdev seqvar get` and the given args are `"key" "value"`.


## Semantics
Our chaining CLI pattern is intended to imply certain semantics on the interpretation of the command line:

In the usual unelaborated CLI semantics, the single command names a _program_ — a distinguished kind of function or procedure — which is then evaluated or _run_, being passed any given arguments.

In our chaining CLI, the _rightmost_ command is interpreted in exactly the same way — as a general program to which any arguments on the remainder of the command line are passed.

Similarly, all the preceding commands designate programs that are simply run in sequence from left to right.  

However — unlike the rightmost command program, whose operation is fully general and unconstrained — the preceding commands are expected to follow a proscribed pattern of behavior — they must each effectively function as chaining CLIs for everything to their right in the command line.  This is done as follows:  

- When run they may first perform any desired intialization, such as to set things up for the benefit of subcommands.  (In our example above `seqvar` initializes the key-value store that its subcommands access)

- However, unlike typical programs, they do not consume arguments from the command line in an ad hoc way.  Instead they peek at the next item on the command line (if any) and dispatch on what's there: 

  - If the next item names a subcommand, the executing command then passes control to that program to interpret the rest of the command line.  This effectively chains the command programs together, left to right, in a sort of continuation style. 

  - Otherwise, if instead there is no next item, or it does not name a subcommand, then the command's behavior is unconstrained — it may do nothing, error out, consume what it finds on the rest of the command line as a typical program does, or whatever else its author has specified.

This behavior can actually be viewed as to applying uniformly to _all_ of the command programs in the chain, with the leftmost and rightmost being limiting cases:

- the first command program effectively _is_ "the" CLI — simply set the OS's "executable" bit on the program file

- the last command by definition doesn't have any specified subcommand, so behaviorally it's just a regular program

## Embodiement
We now describe in general terms how we approach embodying chaining CLIs.  For concreteness we also reference our implementation in a *nix/Python environment.  However these patterns should be easily transferrable to other mediums.

We work with two types of persistent information entities, which are both _named containers_:
- _files_, which contain data of diverse kinds, and 
- _folders_, which may contain files and/or other folders

Files whose data consists of various forms of computer directives and associated bits of _code_ we will refer to as _programs_.  Programs are typically comprised mostly of a collection of _named function_ specifications.

Some programs are _runnable_ (others may just be libraries) — for example by having a designated `main` function.  Some of these may be specially designated by the OS as _executable_.

In our chained CLI implementation we do not require that command programs be executable, only runnable -- they are able to be run, and can call eachother, without other OS support.  

However an exception in our implementation "the" primary or "root" CLI command _is_ made executable, , as a usability convenience to better integrate it with the prevailing system model (in our example, this "entry point" is the program named `seqwebdev` which thus always appears as the first command in the chain).

We utilize naming conventions and the folder tree structures containing the command programs to define the command heirarchy organization:

- Commands are implemented by program files that are named for the command (eg `set.py` implements the `set` command)

- Commands with subcommands are further defined by using folders that are named for that command. These contain
  - the program implementing the command (ie typically the common initializations for the subcommands)
  - definitions for shared "helper" function library
  - the folders or programs for each subcommand (ie respectively having, or not having, subcommands)

Example of chaining CLI file tree organization:
```
foo/                # `foo…` command group folder
├── foo.py          # common helpers, initialization & dispatch for `foo…` commands
├── bar.py          # implementation of the `foo bar` command
├── baz/            # `foo baz…` subcommand group folder
│   ├── baz.py      # common helpers, initialization & dispatch for `foo baz…` commands
│   ├── abc.py      # implementation of the `foo baz abc` command
│   └── xyz.py      # implementation of the `foo baz xyz` command
…
```
- the top-level command is named `foo` — so the `foo/` folder is used to define a chaining "foo CLI"  
- `foo.py` does initialization for all commands (if any), then transfers control to them
  - also implements behavior for when `foo` is *not* followed by a subcommand (with or without args)
  - optional: set `chmod +x` on this program to make `foo` directly accessible in the OS CLI
- `bar.py` "leaf" commands are unconstrained
- `baz.py` initializes and dispatches to subsubcommands, analogous to `foo.py` (ie a "sub" CLI)

Possible CLI commands defined by this example
```
    foo
    foo bar
    foo baz
    foo baz abc
    foo baz xyz
```

## Annotations for performance and user support
In our Python implementation we utilize the following annotations 
```
    has_subcommands     # avoids having to look into the folder at runtime
    name                # handy for messages, avoids introspection overhead
    description         # terse description for summary lists and simple help
    help_text           # brief documentation with usage, semantics, examples, doc links, etc.
```

These also support the implmentation of a `help` command: along with a command's direct documentation, if it has subcommands these can be tersely listed (along with decorations indicating when they have subsubcommands...)

## Implementation Template

### Base Command Class
```python
class BaseCommand:
    def main(self):
        # 1. Do command-specific initializations
        self.do_initializations()
        
        # 2. Try to find and call subcommand
        if self.find_and_call_subcommand():
            return  # Subcommand handled everything
        
        # 3. No subcommand found - do this command's own work
        self.do_command_things_or_whatever()
    
    def do_initializations(self):
        """Override with command-specific initialization"""
        pass
    
    def find_and_call_subcommand(self) -> bool:
        """Command-invariant: find and call subcommand if it exists"""
        args = sys.argv[1:]
        if not args:
            return False
        
        subcommand_name = args[0]
        subcommand_file = self.get_subcommand_file(subcommand_name)
        
        if subcommand_file:
            # Import and run subcommand with remaining args
            module = importlib.import_module(f"seqwebdev.{subcommand_name}")
            module.main(args[1:])
            return True
        
        return False
    
    def do_command_things_or_whatever(self):
        """Override with command-specific functionality"""
        pass
    
    def get_subcommand_file(self, subcommand_name: str) -> Optional[Path]:
        """Look up subcommand in neighborhood directory"""
        command_dir = Path(__file__).parent
        
        # Check if it's a direct file
        subcommand_file = command_dir / f"{subcommand_name}.py"
        if subcommand_file.exists():
            return subcommand_file
        
        # Check if it's a folder with matching .py file
        subcommand_dir = command_dir / subcommand_name
        if subcommand_dir.is_dir():
            folder_command_file = subcommand_dir / f"{subcommand_name}.py"
            if folder_command_file.exists():
                return folder_command_file
        
        return None
```

### Command Implementation Examples

#### Root Command (seqwebdev.py)
```python
class SeqWebDevCommand(BaseCommand):
    def do_initializations(self):
        """Setup fundamental environment preconditions"""
        setup_fundamental_environment()
    
    def do_command_things_or_whatever(self):
        """Root command with no subcommand - show help"""
        show_help()

if __name__ == "__main__":
    SeqWebDevCommand().main()
```

#### Command Group (seqvar/seqvar.py)
```python
class SeqvarCommand(BaseCommand):
    def do_initializations(self):
        """Initialize seqvar database for subcommands"""
        init_seqvar_database()
    
    def do_command_things_or_whatever(self):
        """Seqvar command with no subcommand - show help"""
        show_seqvar_help()

if __name__ == "__main__":
    SeqvarCommand().main()
```

#### Leaf Command (seqvar/set.py)
```python
class SetCommand(BaseCommand):
    def do_initializations(self):
        """No initialization needed - parent already did it"""
        pass
    
    def do_command_things_or_whatever(self):
        """Handle set command with arguments"""
        args = sys.argv[1:]
        if len(args) < 2:
            print("Usage: seqwebdev seqvar set <key> <value>")
            return
        
        key, value = args[0], args[1]
        set_seqvar(key, value)

if __name__ == "__main__":
    SeqSetCommand().main()
```

### Key Benefits
1. **Uniform pattern** - every command follows the same two-method structure
2. **Only difference** - what each command does in `do_initializations()`
3. **Subcommand discovery** - completely uniform across all commands
4. **Clean inheritance** - just override the methods you need
5. **No duplication** - the complex logic is in the base class
