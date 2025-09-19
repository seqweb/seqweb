---
layout: default
title: Polyglot Pipeline Architecture
---
# Polyglot Box-Passing Processing Pipeline Architecture

## Overview

One function of the SeqWeb system is to convert OEIS sequence data (`.seq` files) into semantic web knowledge graphs (`.ttl` files).  Such conversions lie in the domain of SeqWeb's _Webwright_ subsystem (see [Systems Design](systems_design) for context)

The Webwright subsystem constructs the knowledge graph by orchestrating an ensemble of _Fabricators_. Each Fabricator is responsible for generating a specific portion of the knowledge graph, and implements a polyglot processing pipeline  composed of individually-implemented software _Modules_ designed to interface readily with related Modules.  Each Module may be implemented in an arbitrary language (e.g., Python, Java, Lisp, Bash — see [Rationale for Polyglot Implementation](#rationale-for-polyglot-implementation) below).

For example, a Fabricator might extract entities from selected OEIS entry text, and then build RDF triples linking the subject sequence to those entities via specific relationships. Upstream Modules in this Fabricator would handle entity extraction; downstream Modules would generate the RDF; others may support intermediate transformation, filtering, or enrichment.

<p align="center">
  <img src="../assets/drawings/pipeline.drawio.svg" width="650" alt="Pipeline" />
  <br> <em>Fabricator polyglot pipeline architecture</em>
</p>

Modules communicate via a shared abstract structure called a **box** — functionally a box is just a language-agnostic key-value map that flows through the pipeline.

<!-- ![Box glyph](../assets/drawings/box-glyph.drawio.svg) -->

<p align="center">
  <img src="../assets/drawings/box-glyph.drawio.svg" width="100" alt="Box glyph" />
  <br> <em> a box</em>
</p>

Each module is designed to function as a **plug-and-play unit** in one or more Fabricator pipelines, in which modules may be composed, replaced, tested, or reused independently, regardless of implementation language.

Modules may be composed using two different implementation mechanisms: via **native orchestration** (when the modules are implemented in the same language) or executed in isolation through **shell wrappers** that expose a standardized shell interface. This hybrid model allows clean decoupling, testability, and flexibility across execution boundaries.

---

## Key Terminology

| Term                | Meaning |
|---------------------|---------|
| **box**             | A dictionary-like structure of key-value pairs shared between modules. It serves both as the input/output of each module and as a shared **common carrier** of state across pipeline stages. |
| **inbox**           | The input `box` to a module. |
| **outbox**          | The output `box` from a module, typically the inbox plus zero or more updates to existing key-values and/or additions of new key-values. |
| **module**          | A processing unit that takes an inbox and returns an outbox. |
| **program**         | A shell-invocable wrapper around a module exposing the inbox/outbox interface over shell+JSON. |
| **shell wrapper**   | A thin entry point that translates shell input to a native-language `box`, invokes the core function, and emits JSON output. Typically this is implemented as a "main" program.|
| **core function**   | A native-language, pure function that maps a `box` (dict/map) to another `box`. |
| **destructuring interface** | A function signature that binds named keys from a box to function arguments (e.g., `*, prompt, noisy=False, **_rest`). |
| **box-then-kwargs pattern** | Pattern where a function accepts the full `box` and also unpacks it for destructuring. |
| **native composition** | Bypassing the shell wrapper by directly calling core functions within the same runtime environment. |
| **shell execution** | Cross-language or system-level execution where modules run as subprocesses using wrapper interfaces. |

---

## Implementation Patterns

### Destructuring Core (Python)

```python
def normalize_prompt(box: dict, *, prompt, noisy=False, **_rest) -> dict:
    result = prompt.strip().lower()
    if noisy:
        print(f"\n\t🧹 Normalized Prompt:\n{result}")
    return {**box, "normalized_prompt": result}
```

- Takes full box, then uses destructuring to pick out args
- Additional keys are preserved in _rest even if unused locally (to support fancy shadowing patterns)
- Returns original box with new information; outbox can override inbox simply by shadowing keys
- Enables composability and pass-through semantics

---

### Shell Wrapper (Python)

```python
def main():
    # get arguments from shell
    parser = argparse.ArgumentParser(description="Run 'Normalize Prompt' pipeline stage.")
    parser.add_argument("prompt")
    parser.add_argument("--noisy", action="store_true")
    args = parser.parse_args()
    # form the inbox for the core, call it to get the outbox, and dump it for "piping" 
    inbox = {"prompt": args.prompt, "noisy": args.noisy}
    outbox = normalize_prompt(inbox, **inbox)
    json.dump(outbox, sys.stdout)
```

---

## Fabricator Example: fabricate_response

```python
def fabricate_response(box: Dict[str, Any], *, prompt: str, noisy: bool = False, **_rest) -> Dict[str, Any]:
    """Fabricator that processes a prompt through a pipeline: normalize, generate, present."""
    initial_box = {'prompt': prompt, 'noisy': noisy, **_rest}
    # list of modules to call in pipeline
    modules = [
        normalize_prompt,   # see above example
        generate_response,  # ToDo
        present_response    # ToDo
    ]
    return run_pipeline(modules, initial_box)

def main():
    parser = argparse.ArgumentParser(description="fabricate_response - Prompt processing fabricator")
    parser.add_argument("prompt", help="The prompt to process")
    parser.add_argument("--noisy", action="store_true", help="Enable verbose output")
    args = parser.parse_args()
    
    box = {'prompt': args.prompt, 'noisy': args.noisy}
    result = fabricate_response(box, **box)
    json.dump(result, sys.stdout)
```

where the `run_pipeline` functional helper composes modules using the box-then-kwargs pattern:

```python
def run_pipeline(modules: List[Callable], initial_box: Dict) -> Dict:
    box = initial_box
    for module in modules:
        box = module(box, **box)  # Each module gets full box + destructured args
    return box
```

This fabricator demonstrates that **"Fabricators can be Modules too"** — `fabricate_response` can be used both as a standalone program and as a composable module in larger pipelines. Note: the implementation of the last two modules (`generate_response` and `present_response`) are left as exercises for the reader.

---

## Cross-Language Fabricator Execution Model

When modules are implemented in different languages and invoked as standalone programs:

- Each **program derives its `inbox` from its command-line arguments**, using the language's shell parsing conventions (e.g., `argparse` in Python, `getopts` in Bash, etc.).
- These arguments effectively form a **shell-flavored keyword map**, which becomes the initial `inbox`.
- The Fabricator then passes this inbox to the corresponding **core function** (using the destructuring pattern if supported).
- The result is a new **outbox**, which is serialized to **JSON** and written to stdout (or another standard stream or file as appropriate).
- **Optionally**, a program may support a special flag (e.g. `--inbox-json`) to, say, preload an initial inbox from a JSON object, merging it with the shell-derived args — but this is not required.
- If no such preload mechanism is used, the Fabricator can default to starting from an **empty box**, or build it solely from other command-line arguments.

This design allows for:
- Simple shell usage (`myprog --foo bar --noisy`)
- Compatibility with language-native command-line tooling
- Optional integration with more structured JSON-based pipelines when needed

### Program Reusability

By structuring Module programs to build their inbox from command-line arguments (optionally merging with a JSON seed), each Module becomes naturally reusable in multiple contexts:

- **As a stage** in a larger **polyglot pipeline**, invoked by a Fabricator.
- **As a standalone shell tool**, usable directly by developers or scripts.
- **In isolation for testing**, with inboxes constructed in code or passed via shell.

This versatility makes it easy to scale from interactive experimentation to production pipelines without changing the module’s core logic.

---

## Architectural Principles

- **Destructurable box interface** — Functions only bind what they need.
- **Non-destructive box mutation** — Modules are encouraged to preserve and extend the passing box, not replace it.
- **Interop via wrappers, speed via direct calls** — Same-language modules can call each other directly, avoiding shell overhead.
- **Soft schema contracts** — Boxes have loose structure; modules declare required keys but tolerate unknown ones.
- **Composable orchestration** — Pipelines can be expressed as ordered lists of stage functions or shell commands.
- **Complete box input** — All parameters required for a module’s operation must be present in the input box, either directly or by reference. This supports functional purity, reproducibility, and testability.
- **Box as common carrier** — The box persists across pipeline stages and may accumulate context not just for immediate use but for downstream consumers, metadata propagation, or audit trail enrichment.

---

## Validation, error handling, testing, debugging & logging

TODO: Add comprehensive section covering validation patterns, error handling strategies, testing approaches, debugging techniques, and logging standards for polyglot pipeline modules.

---

## Rationale for Polyglot Implementation

While the entire Fabricator pipelines could, in theory, be implemented in a single language, our design favors a **polyglot approach**. This allows each module to leverage the language best suited to its particular role, improving expressiveness, maintainability, and integration with existing tools.  For example:

### Java Strengths
- High-performance file I/O and stream parsing
- Strong typing and class structure for data validation
- Excellent for bulk structured transformations (e.g. parsing `.seq` files)
- Good for JVM-based deployment in stable, high-throughput environments

### Python Strengths
- Rich NLP and symbolic libraries (spaCy, NLTK, transformers, SymPy)
- Strong support for ad-hoc analysis, data munging, and prototyping
- Proven flexible power tool for orchestration, utility and glue code
- Easy to script, test, and deploy in multiple environments
- Ubiquitous community support and package ecosystem

### Lisp (Common Lisp) Strengths
- High expressiveness for symbolic pattern matching and macro-driven transformation
- Close long-term co-evolution with semantic web tech, ontologies, knowledge graphs
- Ideal for defining declarative or rule-based mappings (e.g., `.seq` to RDF idioms)
- Dynamically interactive development (REPL-driven exploration)
- Leverages prior art in neuro-symbolic AI and symbolic mathematics

### Bash Strengths
- Simple, lightweight task automation and glue across system tools
- Natural fit for file-system-level orchestration and batch job wiring
- Ubiquitous on UNIX-like systems and integrates well with Git, Make, etc.

The modular, mixed-language Webwright design pattern allows us to prototype rapidly, optimize when needed, and maintain clarity between different kinds of logic: transformation, coordination, parsing, and enrichment.
