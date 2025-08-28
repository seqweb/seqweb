---
layout: default
title: Initial Fabricator Development Plans
permalink: /docs/first_fabricators/
---

# Initial fabricator development plans

Essential background context (in order):
- [SeqWeb Overview](../docs/seqweb_overview.md)
- [Systems Design](../docs/systems_design.md)
- [Polyglot Pipeline Architecture](../docs/polyglot_pipeline.md)

## Monoglot development & wrappers

For our first fabricators we are going to work entirely in Python.  Therefore, because the "polyglot" considerations won't be relevant -- we could -- if we wanted to be really scrappy -- implement a lot of core functionality by writing only the "destructuring inner functions" for our modules, and dispense with the "CLI wrappers".  We will not, however, do this, for several reasons, one of them being that having the wrappers can make debugging more convenient -- the wrappers enable us to use the *SeqWeb* CLI's `run` command to easily execute modules and pipelines manually.

## Code framework

Initially we will put all fabrication-related code in `seqwebcode/src/python/app/fab/` for convenience.  This will include individual specific fabricator and module implementations, along with all shared and support code.  Later we can revisit this and reorganize or refactor as desired.

We will place an initial implementation of `run_pipeline()` in `pipeline.py` and use it for our early fabricator functions, which will reside in `fabricators.py`.  These will consist of basic linear pipelines composed of module functions, from `modules.py`.

Our early pipelines will operate on only one `A-number` at a time.  For example they might read the specified `.seq` file from the `oeisdata` repo, and write a corresponding `.ttl` file into the `seqwebdata` repo.  

Therefore modules in these pipelines can expect to see, at a minimum, the key "A-number" in their inboxes, along with a "noisy" flag.

## Initial "Hello World" milestone

Our first fabricator will be called `fab0`.  It will simply run a pipeline consisting of a single module function called `mod0`, whose only action will be to print out the value of the incoming `A-number`.

We will test this out by issuing the SeqWeb CLI command like
```
seqwebdev run seqwebcode/src/python/app/fab/fab0.py "A123456"
```

For now we won't do any input validation or error handling -- the implementation should be as straightforward and simple as possible and hence can just fail in any pythonically native way.  Running this command as a "smoke test" will be our initial validation of the basic system; no further testing will be required at this time.

## Implementation details

### File structure
```
seqwebcode/src/python/app/fab/
├── __init__.py          # Package initialization
├── pipeline.py          # run_pipeline() function
├── mod0.py             # mod0 module (inner function + CLI wrapper)
└── fab0.py             # fab0 fabricator (inner function + CLI wrapper)
```

### Key components

**`mod0.py`**: Contains both the inner function `mod0()` and a CLI wrapper. The inner function follows the polyglot pattern: takes an inbox dictionary and returns an outbox dictionary. The CLI wrapper handles argument parsing and calls the inner function.

**`pipeline.py`**: Contains the `run_pipeline()` function that orchestrates pipeline execution. It takes a list of module functions and an initial inbox, processes each module in sequence, and returns the final outbox.

**`fab0.py`**: Contains the main fabricator logic that creates an initial inbox with the A-number, defines a pipeline with `mod0`, and executes it using `run_pipeline()`. Also includes a CLI wrapper for standalone execution.

### Pipeline execution

The `run_pipeline()` function:
- Takes a list of module functions and an initial inbox
- Processes each module in sequence, passing the outbox from one module as the inbox to the next
- Returns the final outbox (enabling fabricators to act as modules in larger pipelines)
- Follows the polyglot pipeline architecture specification

### CLI integration

Both `mod0.py` and `fab0.py` can be run as standalone programs:
- `python3 mod0.py A123456` - Direct execution of mod0
- `python3 fab0.py A123456` - Direct execution of fab0
- `seqwebdev run seqwebcode/src/python/app/fab/fab0.py A123456` - Via SeqWeb CLI

The `--noisy` flag enables verbose output for debugging.

### Data flow

1. CLI receives A-number argument
2. `fab0` creates initial inbox with A-number and noisy flag
3. `run_pipeline()` executes the pipeline with `mod0` module
4. `mod0` prints the A-number and returns the inbox unchanged
5. Final outbox is returned and displayed as JSON

This demonstrates the complete end-to-end dataflow through the Fabricator framework using a minimal amount of functionality.


