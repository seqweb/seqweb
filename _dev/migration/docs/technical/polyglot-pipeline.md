# Polyglot Box-Passing Processing Pipeline Architecture

## 🧭 Overview

*SeqWeb is to the OEIS as DBpedia is to Wikipedia.*

A primary goal of the SeqWeb system, as implemented in this `seqwebcode` repository, is to convert OEIS sequence data (`.seq` files) into semantic web knowledge graphs (`.ttl` files).

The `seqwebcode` architecture supports **polyglot pipelines** composed of modular processing **modules**, where each module may be implemented in an arbitrary language (e.g., Python, Java, Lisp, Bash — see [Rationale for Polyglot Implementation](#rationale-for-polyglot-implementation) below).

<p align="center">
  <img src="../drawings/pipeline.drawio.svg" width="650" alt="Pipeline" />
  <br> <em>polyglot pipeline</em>
</p>

These modules communicate via a shared abstract structure called a **box** — a language-agnostic key-value map that flows through the pipeline.

<!-- ![Box glyph](../drawings/box-glyph.drawio.svg) -->

<p align="center">
  <img src="../drawings/box-glyph.drawio.svg" width="100" alt="Box glyph" />
  <br> <em> a box</em>
</p>

Each module is designed as a **plug-and-play unit**, capable of being composed, replaced, tested, or reused independently, regardless of implementation language.

Modules may be composed via **native orchestration** (when implemented in the same language) or executed in isolation through **IO wrappers** that expose a standardized shell or CLI interface. This hybrid model allows clean decoupling, testability, and flexibility across execution boundaries.

---

## 📦 Key Terminology

| Term                | Meaning |
|---------------------|---------|
| **box**             | A dictionary-like structure of key-value pairs shared between modules. It serves both as the input/output of each module and as a shared **common carrier** of state across pipeline stages. |
| **inbox**           | The input `box` to a module. |
| **outbox**          | The output `box` from a module, typically the inbox plus zero or more updates. |
| **module**          | A processing unit that takes an inbox and returns an outbox. |
| **module group**    | A group of related module definitions implemented in a single language. |
| **program**         | A shell-invocable wrapper around a module exposing the inbox/outbox interface over CLI+JSON. |
| **IO wrapper**      | A thin entry point that translates CLI input to a native-language `box`, invokes the inner function, and emits JSON output. |
| **inner function**  | A native-language, pure function that maps a `box` (dict/map) to another `box`. |
| **destructuring interface** | A function signature that binds named keys from a box to function arguments (e.g., `*, prompt, noisy=False, **_rest`). |
| **box-then-kwargs pattern** | Pattern where a function accepts the full `box` and also unpacks it for destructuring. |
| **native composition** | Bypassing the IO wrapper by directly calling inner functions within the same runtime environment. |
| **shell execution** | Cross-language or system-level execution where modules run as subprocesses using wrapper interfaces. |

---

## 🔧 Implementation Patterns

### ✅ Destructuring Inner Function (Python)

```python
def normalize_prompt(box: dict, *, prompt, noisy=False, **_rest) -> dict:
    result = prompt.strip().lower()
    if noisy:
        print(f"\n\t🧹 Normalized Prompt:\n{result}")
    return {**box, "normalized_prompt": result}
```

- Uses both full box and destructuring
- Extra keys (e.g. `config`) are preserved even if unused
- Enables composability and pass-through semantics

---

### ✅ CLI Wrapper (Python)

```python
def main():
    parser = argparse.ArgumentParser(description="Run a KA pipeline stage.")
    parser.add_argument("prompt")
    parser.add_argument("--noisy", action="store_true")
    parser.add_argument("--config")

    args = parser.parse_args()
    inbox = {"prompt": args.prompt, "noisy": args.noisy, "config": args.config}
    outbox = normalize_prompt(inbox, **inbox)
    json.dump(outbox, sys.stdout)
```

---

## 🔁 Orchestration Example: Native Python

```python
from typing import Callable, Dict, List, Optional

def run_pipeline(
    stages: Optional[List[Callable]] = None, initial_box: Optional[Dict] = None
) -> Dict:
    """Run a sequence of stages on a box, using the box-then-kwargs pattern."""
    if stages is None:
        stages = []
    if initial_box is None:
        initial_box = {}

    box = initial_box
    for stage in stages:
        box = stage(box, **box)
    return box
```

- Orchestrates native Python stages
- Supports implicit pass-through of `config`, `noisy`, etc.
- Easily extended with conditional logic, tracing, or logging

---

## 🌐 Cross-Language Execution Model

When modules are implemented in different languages and invoked as standalone programs:

- Each **program derives its `inbox` from its command-line arguments**, using the language's CLI parsing conventions (e.g., `argparse` in Python, `getopts` in Bash, etc.).
- These arguments effectively form a **shell-flavored keyword map**, which becomes the initial `inbox`.
- The program then passes this inbox to the corresponding **inner function** (using the destructuring pattern if supported).
- The result is a new **outbox**, which is serialized to **JSON** and written to stdout (or another standard stream or file as appropriate).
- **Optionally**, a program may support a special flag (e.g. `--inbox-json`) to preload an initial inbox from a JSON object, merging it with the CLI-derived args — but this is not required.
- If no such preload mechanism is used, the program should default to starting from an **empty box** and building it solely from the command-line arguments.

This design allows for:
- Simple shell usage (`myprog --foo bar --noisy`)
- Compatibility with language-native command-line tooling
- Optional integration with more structured JSON-based pipelines when needed

### 🔁 Program Reusability

By structuring programs to build their inbox from command-line arguments (optionally merging with a JSON seed), each module becomes naturally reusable in multiple contexts:

- **As a stage** in a larger **polyglot pipeline**, invoked by an orchestrator.
- **As a standalone CLI tool**, usable directly by developers or scripts.
- **In isolation for testing**, with inboxes constructed in code or passed via CLI.

This versatility makes it easy to scale from interactive experimentation to production pipelines without changing the module’s core logic.

---

## 📘 Architectural Principles

- **Destructurable box interface** — Functions only bind what they need.
- **Non-destructive box mutation** — Modules are encouraged to preserve and extend the box, not replace it.
- **Interop via wrappers, speed via direct calls** — Same-language modules can call each other directly, avoiding shell overhead.
- **Soft schema contracts** — Boxes have loose structure; modules declare required keys but tolerate unknown ones.
- **Composable orchestration** — Pipelines can be expressed as ordered lists of stage functions or shell commands.
- **Complete box input** — All parameters required for a module’s operation must be present in the input box, either directly or by reference. This supports functional purity, reproducibility, and testability.
- **Box as common carrier** — The box persists across pipeline stages and may accumulate context not just for immediate use but for downstream consumers, metadata propagation, or audit trail enrichment.

---

## 🔍 Validation, error handling, testing, debugging & logging

TODO: Add comprehensive section covering validation patterns, error handling strategies, testing approaches, debugging techniques, and logging standards for polyglot pipeline modules.

---

## Rationale for Polyglot Implementation

While the entire conversion pipeline could, in theory, be implemented in a single language, our design favors a **polyglot approach**. This allows each module to leverage the language best suited to its particular role, improving expressiveness, maintainability, and integration with existing tools.

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

This modular, mixed-language design allows us to prototype rapidly, optimize when needed, and maintain clarity between different kinds of logic: transformation, coordination, parsing, and enrichment.
