# Bootstrapping and Footholding

## How the SeqWeb Development Environment is established,

The _SeqWeb Development Environment_ or "_seqwebdev env_" is established on the user/developer's local host through the following procedures and processes:

1. To intially bootstrap the SeqWeb system, the user, per the [SeqWeb Developer Quickstart](quickstart.md), curls `seqwebcode:bootstrap` directly from Github, piping it through bash, which executes it

2. When executed during initial bootstapping the `seqwebcode:bootstrap` script does the following:

    2.1. Sets the OS env var `SEQWEBDEV_HOME` to the current directory.  This dir will henceforth be the designated "SeqWeb development home" directory.
    
    2.2. If one doesn't exist yet, it creates a default `seqweb.conf` (TOML) file there with default repo paths
    
    2.3. Writes a script into `$SEQWEBDEV_HOME/seqwebdev` and makes it executable. Thenceforth this is known as the "Universal SeqWeb CLI Entry Point" -- "universal" because it's a generic bash script. This is what's supposed to get executed when you issue the CLI command `seqwebdev`.
    
    2.4. Updates the .bashrc file (or equivalent) to export `SEQWEBDEV_HOME`
    
3. The user then does any manual bootstrapping operations needed, such as cloning repos and configuring `seqweb.conf`.  They conclude the bootstrapping phase by starting a fresh console session and calling the CLI for the first time.

4. When the `seqwebdev` command is executed by the user to access the CLI, the `$SEQWEBDEV_HOME/seqwebdev` "Universal" entrypoint then 

    4.1. Uses filesystem discovery to find the `seqwebcode:` repo by looking for directories named `seqwebcode` containing the `bootstrap` file, 

    4.2. Uses the repo as a prefix to construct a path to the "Implementation" (ie Python) entrypoint for the CLI, which is at `seqwebcode:src/python/app/cli/seqwebdev`

    4.3. Makes sure the "Implementation" entrypoint file is executable. and then executes it, passing through whatever arglist the Universal entrypoint was given

5. When executed, the `seqwebcode:src/python/app/cli/seqwebdev` "Implementation" entrypoint:

    5.1. Does some "Path massaging" 

    5.2. Imports the `main` function from the initial "anchor" command implementation for the "chaining" CLI, which is currently at `seqwebcode:src/python/app/cli/chain/seqwebdev/seqwebdev.cli`
    
    5.3. Calls this `main` as its own, thereby starting the chaining command line interpretation process.

6. Then the anchor command `seqwebdev` and any others proceed down the chain of command programs, executing according to their specific implementations.  They are built on the `BaseCommand` class, defined in `seqwebcode:src/python/app/cli/chain/base_command.py` (qv).


## Discussion and development next-steps
(_as of 2025-09-02_)

I currently assume that the one-time bootstrapping steps 1, 2 and 3 in this process are about as clear and clean as current technology allows.  Naturally I would like it if `seqwebcode:bootstrap` could be made a little more compact and elegant, but overall the bootstrapping implementation details are well-enough packaged to be acceptably hidden, and these steps aren't in need of immediate revision.

I also think step 6 is in pretty good shape with advent of the new chaining CLI (with one proviso I'll get to below).

However steps 4 and 5 could perhaps benefit from more attention.  

These involve the exceptional `seqwebdev` _entrypoints_, characterized by eponymous program files without explicit type that present as executable extensions with the "shell" style interaction model (following the pattern of `git` and `python3` itself, among others).

One question might be: why have two-level indirection?  Perhaps steps 4 and 5 should be combined?  While this unification has some appeal it is also true that segmenting into "Universal" and "Implementation" entrypoints does impose a natural "separation of concerns".  The former helps present a language-agnostic "OS style" interface, whereas the latter embraces the fact of the CLI's concrete implementation in Python.  This is also reflected in their being written in two different languages.  So the mutiple indirection isn't a determinative motive for merging them, by itself (pace Occam).  But I'm open to motivated alternatives.

More pragmatically concerning are the "preamble" code sections for 4.1 and 5.1.  These seem slightly dodgy: arcane, fragile and fussy to maintain.  Can we somehow do better?

### Re. the Universal entrypoint and 4.1

The worst code smell is the grungy `awk`-driven TOML-config parsing kludge to get the `seqwebcode:` path.  Is there a nicer way to do this?  

Better would be to somehow use some kind of "TOML aware" config parsing.  Curiously TOML-related facilities are  purportedly available in the `lib/` directories of all four currently supported languages.  Would it make sense to somehow try to factor those a bit and use them, instead of the ad hoc `awk` approach?  

Best of all would be some scheme that doesn't require TOML parsing at all (for example perhaps somehow asking Github or 'git` where it cloned the repo?)  I'm open to considering other "discovery" and/or configuration alternatives.

Another minor question: is it really necessary for the Universal entrypoint to `chmod` the Implementation entrypoint?  Does the `exec` require it?  Otherwise note the Implementation entrypoint is *not* intended to be called by a user or anything other than the Universal entrypoint, so the `chmod` seems overkill.  Not critcal, but it would be cleaner without it.

(Note: changing the Universal entrypoint entails changing the text of the script in the `bootstrap`; anything that comes from the repo will get overwritten by the boostrap process)

### Re. the Implementation entrypoint and 5.1

The Python path gets messaged solely to enable importing the CLI anchor's "main".  However it does something that is much broader in scope and potentially helpful to _all_ SeqWeb Python code: it puts the entire `seqwebcode:src/python/` directory in the Python Path, making everything conveniently accessible to everything else.  This allows, for example, `app/` programs to easily access `lib/` utilities.

However embedding this into the CLI entrypoint process, while needed and beneficial, shoehorns a very general strategy into a highly specific solution.  Perhaps it should be somehow factored out and sited elsewhere?
 
Such broad enabling functionality embodies a kind of transitional mission: it doesn't need or want to be integrated in the scrappy, minimalist, bootstrapping process, yet it clearly contributes wide foundational utility across the activities that follow.  To give this mission a name, by analogy I call these kinds of post-bootstrap operations `footholding`.

Footholding encompasses the establishment of a standard ambient enabling background environment of features and capabilities against which implementations and operations can be defined and developed.  The bootstrap creates the minimal initial conditions which the foothold subsequently provisions and fleshes out.

An example is SeqWeb's `seqvar` store and config file support.  The bootstrap functions using just the single "OS env var" `SEQWEBDEV_HOME`, but then these SeqWeb features expand the foothold to include useful items of SeqWeb domain-specific, but general-purpose, utility.
  
The polyglot environment raises issues around how the foothold implementation is packaged and sited.  For some naturally generic cross-cutting items, such as the `seqvar` environment variable bindings, the pattern is established that parallel support for these is implemented in each language's `lib/` subdirectory.

However, abstracting a bit, it's clear we need to anticipate highly idiosyncratic and language-specific footholding — roughly analogous to Python's Path hacking — but in the other languages (say for Java packages).  What patterns should these follow?

Bringing all this back to the immediate concrete problems we are focusing on: 

Can we factor out the Python path-hacking in the CLI's Implementation entrypoint to somewhere that's not CLI-specific, so that other code (say fabrictors) not currently wedded to the CLI can benefit from it?  

Or, alternatively, should we double down on the CLI as instrinsic to SeqWeb, and require all code to somehow work "through" it?  While attractive in some ways I'm concerned about how this would work with, say, running Java programs the implement Webhouse cloud or build operations.  How do other polyglot systems approach this conundrum?

And in any case, where should footholding code be sited?  In the `lib/` for each language?  

That might make sense, but it may beg a dependency issue: if the foothold's factored out, how is the CLI going to find the foothold code it needs that enables it to find things?

Perhaps there's some kind of "polyglot" foothold that the bootstrap system runs and afterward each language has its own foothold it accesses thereafter?

## Waldo's Recommended Solutions
(_as of 2025-09-03_)

**Entrypoint consolidation**: Keep the two-level separation. The Universal entrypoint provides OS-agnostic interface while Implementation handles Python-specific concerns. The separation is clean and follows established patterns.

**TOML parsing**: Replace the fragile `awk`-driven TOML parsing with filesystem discovery. Look for `seqwebcode` directories containing the `bootstrap` file as a signature. This eliminates TOML parsing entirely while maintaining the current architecture.

**Python path management**: Create a `seqwebcode:src/python/lib/foothold.py` module that handles path setup. The CLI Implementation entrypoint imports and calls `foothold.setup_python_path()`. This makes the functionality reusable by Fabricators without CLI dependency.

**Footholding architecture**: Site footholding code in each language's `lib/` directory with a common interface pattern. Each language implements `foothold.setup_environment()` that handles language-specific path/package management.

**Dependency management**: The CLI Implementation entrypoint imports foothold utilities directly from `lib/foothold.py`. Since the CLI already sets up the Python path, it can access foothold code immediately.

**Polyglot integration**: Don't require all code to work "through" the CLI. Instead, provide foothold utilities that any component can import. Java Webhouse operations can use their own foothold setup, while Python Fabricators use the Python foothold.

**Implementation priority**: Start with the Python path refactoring since it's the immediate concrete problem. Create `lib/foothold.py`, move the path setup there, and update the CLI to use it. This establishes the pattern for other languages.
