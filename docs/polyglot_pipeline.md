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
| **outbox**          | The output `box` from a module, typically the inbox plus zero or more updates to existing key-values and/or additional new key-values. |
| **module**          | A processing unit that takes an inbox and returns an outbox. |
| **program**         | A shell-invocable wrapper around a module exposing the inbox/outbox interface over shell+JSON. |
| **shell wrapper**   | A thin entry point that translates shell input to a native-language `box`, invokes the core function, and emits JSON output. |
| **core function**   | A native-language, pure function that maps a `box` (dict/map) to another `box`. |
| **destructuring interface** | A function signature that binds named keys from a box to function arguments (e.g., `*, prompt, noisy=False, **_rest`). |
| **box-then-kwargs pattern** | Pattern where a function accepts the full `box` and also unpacks it for destructuring. |
| **native composition** | Bypassing the shell wrapper by directly calling core functions within the same runtime environment. |
| **shell execution** | Cross-language or system-level execution where modules run as subprocesses using wrapper interfaces. |

---

## Wrapper Contract

The `main()` function in each module serves as a **shell wrapper** with a strict contract:

**Input Contract:**
- Parse command-line arguments into a standardized "inbox" structure
- Convert shell arguments to appropriate data types for the core function
- Handle common flags like `--noisy`, `--help`, etc.

**Output Contract:**
- Serialize the "outbox" as JSON to stdout
- Ensure JSON output is consumable by other pipeline modules
- Maintain language-agnostic compatibility

**Core Function Contract:**
- The `main()` wrapper must call the core function with the exact same semantics
- No modification of core function behavior
- No additional functionality beyond argument marshalling and JSON output

**Anti-Patterns to Avoid:**
- Adding test functionality to `main()` wrappers
- Modifying core function semantics in the wrapper
- Using `main()` for standalone tools or debugging
- Adding functionality that breaks JSON output compatibility

The wrapper exists solely to enable shell-based composition across languages. All other functionality should be implemented separately.

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
- Returns original box with new information; outbox can override inbox values simply by supplying shadowing key values.
- Enables composability and pass-through semantics

---

### Destructuring Core (Java)

```java
public static Map<String, Object> normalizePrompt(Map<String, Object> box, 
                                                 String prompt, 
                                                 boolean noisy) {
    String result = prompt.strip().toLowerCase();
    if (noisy) {
        System.out.println("\n\t🧹 Normalized Prompt:\n" + result);
    }
    
    // Create new map with original box contents plus new key
    Map<String, Object> outbox = new HashMap<>(box);
    outbox.put("normalized_prompt", result);
    return outbox;
}
```

**Key Differences from Python:**
- **No `*` destructuring**: Java doesn't support Python's `*` syntax, so we use explicit parameters
- **No `**_rest`**: Additional keys are preserved automatically in the `HashMap` copy
- **Explicit typing**: Java requires explicit type declarations for parameters and return values
- **Manual copying**: We explicitly create a new `HashMap` with the original box contents
- **Method signature**: Uses standard Java method naming conventions

**Usage Pattern:**
```java
// Extract known parameters from box
String prompt = (String) box.get("prompt");
boolean noisy = (Boolean) box.getOrDefault("noisy", false);

// Call core function
Map<String, Object> outbox = normalizePrompt(box, prompt, noisy);
```

---

### Shell Wrapper (Python)

```python
def main():
    # Define argument specifications for this module
    argument_definitions = [
        ('prompt', str, 'The prompt to normalize', True),
        ('noisy', bool, 'Enable verbose output', False)
    ]
    
    # Build inbox from stdin + CLI args using shared utility
    inbox = build_inbox_from_args(argument_definitions)
    
    # Call core function with identical semantics
    outbox = normalize_prompt(inbox, **inbox)
    
    # Emit JSON output for pipeline consumption
    json.dump(outbox, sys.stdout)
```

**Key Points:**
- Uses shared `build_inbox_from_args()` utility for consistent behavior
- Minimal boilerplate: just define arguments and call core function
- Supports both manual CLI usage and programmatic JSON input
- JSON output is consumable by other pipeline modules

---

### Shell Wrapper (Java)

```java
public static void main(String[] args) {
    // Define argument specifications for this module
    List<ArgumentDefinition> argumentDefinitions = Arrays.asList(
        new ArgumentDefinition("prompt", String.class, "The prompt to normalize", true),
        new ArgumentDefinition("noisy", Boolean.class, "Enable verbose output", false)
    );
    
    // Build inbox from stdin + CLI args using shared utility
    Map<String, Object> inbox = Wrapper.buildInboxFromArgs(argumentDefinitions);
    
    // Extract parameters for core function call
    String prompt = (String) inbox.get("prompt");
    boolean noisy = (Boolean) inbox.getOrDefault("noisy", false);
    
    // Call core function with identical semantics
    Map<String, Object> outbox = normalizePrompt(inbox, prompt, noisy);
    
    // Emit JSON output for pipeline consumption
    System.out.println(toJson(outbox));
}
```

**Key Points:**
- Uses shared `Wrapper.buildInboxFromArgs()` utility for consistent behavior
- Minimal boilerplate: just define arguments and call core function
- Supports both manual CLI usage and programmatic JSON input
- JSON output is consumable by other pipeline modules

---

## Fabricator Example: fabricate_response

### Fabricator (Python)

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
    # Define argument specifications for this fabricator
    argument_definitions = [
        ('prompt', str, 'The prompt to process', True),
        ('noisy', bool, 'Enable verbose output', False)
    ]
    
    # Build inbox from stdin + CLI args using shared utility
    inbox = build_inbox_from_args(argument_definitions)
    
    # Call core function with identical semantics
    outbox = fabricate_response(inbox, **inbox)
    
    # Emit JSON output for pipeline consumption
    json.dump(outbox, sys.stdout)
```

where the `run_pipeline` functional helper composes modules using the box-then-kwargs pattern:

```python
def run_pipeline(modules: List[Callable], initial_box: Dict) -> Dict:
    box = initial_box
    for module in modules:
        box = module(box, **box)  # Each module gets full box + destructured args
    return box
```

### Fabricator (Java)

```java
public static Map<String, Object> fabricateResponse(Map<String, Object> box, String prompt, boolean noisy) {
    // Fabricator that processes a prompt through a pipeline: normalize, generate, present
    Map<String, Object> initialBox = new HashMap<>(box);
    initialBox.put("prompt", prompt);
    initialBox.put("noisy", noisy);
    
    // List of modules to call in pipeline
    List<Function<Map<String, Object>, Map<String, Object>>> modules = Arrays.asList(
        FabricateResponse::normalizePrompt,   // see above example
        FabricateResponse::generateResponse,  // ToDo
        FabricateResponse::presentResponse    // ToDo
    );
    
    return runPipeline(modules, initialBox);
}

public static void main(String[] args) {
    // Define argument specifications for this fabricator
    List<ArgumentDefinition> argumentDefinitions = Arrays.asList(
        new ArgumentDefinition("prompt", String.class, "The prompt to process", true),
        new ArgumentDefinition("noisy", Boolean.class, "Enable verbose output", false)
    );
    
    // Build inbox from stdin + CLI args using shared utility
    Map<String, Object> inbox = Wrapper.buildInboxFromArgs(argumentDefinitions);
    
    // Extract parameters for core function call
    String prompt = (String) inbox.get("prompt");
    boolean noisy = (Boolean) inbox.getOrDefault("noisy", false);
    
    // Call core function with identical semantics
    Map<String, Object> outbox = fabricateResponse(inbox, prompt, noisy);
    
    // Emit JSON output for pipeline consumption
    System.out.println(toJson(outbox));
}
```

where the `runPipeline` functional helper composes modules using the box-then-kwargs pattern:

```java
public static Map<String, Object> runPipeline(
    List<Function<Map<String, Object>, Map<String, Object>>> modules, 
    Map<String, Object> initialBox) {
    Map<String, Object> box = new HashMap<>(initialBox);
    for (Function<Map<String, Object>, Map<String, Object>> module : modules) {
        box = module.apply(box);  // Each module gets full box + destructured args
    }
    return box;
}
```

These fabricators demonstrate that _Fabricators can be Modules_ too — `fabricateResponse` can be used both as a standalone program and as a composable module in larger pipelines. Note: the implementation of the last two modules (`generateResponse` and `presentResponse`) are left as exercises for the reader.

---

## Cross-Language Execution Model

When modules are implemented in different languages and invoked as standalone programs:

- Each **program builds its `inbox` from both stdin (JSON) and command-line arguments** using the shared `build_inbox_from_args` utility.
- **Stdin takes precedence** as the primary data source, with CLI arguments providing overrides and additional parameters.
- The **core function** receives the complete inbox using destructuring patterns (e.g., `*, arg, **_rest` in Python).
- The result is a new **outbox**, which is serialized to **JSON** and written to stdout for pipeline consumption.
- **Empty stdin defaults to an empty dict**, ensuring modules work both in pipelines and standalone contexts.

This unified design enables:
- **Pipeline composition** via JSON stdin/stdout (`echo '{"prompt":"test"}' | myprog`)
- **Direct shell usage** with CLI arguments (`myprog --prompt test --noisy`)
- **Hybrid usage** combining both approaches (`echo '{"base":"data"}' | myprog --override value`)
- **Cross-language compatibility** through standardized JSON interfaces

### Multi-Language Module Example

A true polyglot pipeline can mix languages at the module level. Here's a Python core module that calls a Java wrapper:

```python
def polyglot_normalize_prompt(box: Dict[str, Any], *, prompt: str, noisy: bool = False, **_rest) -> Dict[str, Any]:
    """Python core that delegates to Java normalizePrompt wrapper."""
    import subprocess
    import json
    
    # Assumption: Java wrapper is compiled and available in PATH as 'normalize-prompt-java'
    # In practice, this would be configured via seqvar or build system
    java_wrapper_cmd = "normalize-prompt-java"
    
    # Prepare input for Java wrapper
    java_input = {
        "prompt": prompt,
        "noisy": noisy,
        **_rest
    }
    
    try:
        # Call Java wrapper with JSON input via stdin
        result = subprocess.run(
            [java_wrapper_cmd],
            input=json.dumps(java_input),
            text=True,
            capture_output=True,
            check=True
        )
        
        # Parse JSON output from Java wrapper
        java_output = json.loads(result.stdout)
        
        if noisy:
            print(f"Java wrapper returned: {len(java_output)} keys")
        
        return java_output
        
    except subprocess.CalledProcessError as e:
        if noisy:
            print(f"Java wrapper failed: {e.stderr}")
        raise RuntimeError(f"Java normalizePrompt failed: {e}") from e
    except json.JSONDecodeError as e:
        if noisy:
            print(f"Invalid JSON from Java wrapper: {result.stdout}")
        raise RuntimeError(f"Java normalizePrompt returned invalid JSON: {e}") from e

def main():
    # Define argument specifications for this polyglot module
    argument_definitions = [
        ('prompt', str, 'The prompt to normalize', True),
        ('noisy', bool, 'Enable verbose output', False)
    ]
    
    # Build inbox from stdin + CLI args using shared utility
    inbox = build_inbox_from_args(argument_definitions)
    
    # Call core function with identical semantics
    outbox = polyglot_normalize_prompt(inbox, **inbox)
    
    # Emit JSON output for pipeline consumption
    json.dump(outbox, sys.stdout)
```

This demonstrates how a Python module can seamlessly delegate to a Java implementation while maintaining the same wrapper contract. The Java wrapper (`normalize-prompt-java`) would be a compiled version of the Java `normalizePrompt` example shown earlier.

**Key assumptions made:**
- Java wrapper is compiled and available in PATH
- Java wrapper follows the same stdin/stdout JSON contract
- Error handling distinguishes between Java execution failures and JSON parsing issues
- The `subprocess` approach works across platforms (alternative: use JNI or language-specific interop)

### Program Reusability

By structuring Module programs to use the unified `build_inbox_from_args` pattern, each Module becomes naturally reusable in multiple contexts:

- **As a pipeline stage** — invoked by Fabricators with JSON stdin/stdout
- **As a standalone tool** — usable directly with CLI arguments (`myprog --arg value`)
- **As a hybrid component** — combining JSON input with CLI overrides
- **For testing** — via dedicated test files that validate both wrapper contract and core functionality

**Wrapper Contract Requirements**:
- The `main()` wrapper must follow the standardized pattern: stdin + CLI → inbox → core function → outbox → JSON stdout
- No test functionality, debugging code, or standalone tool features in `main()`
- All such features belong in separate test files or utility modules
- Core functions must use destructuring patterns for clean parameter binding

This separation ensures wrappers maintain their contract while enabling comprehensive testing and flexible usage patterns.

---

## Architectural Principles

- **Unified wrapper contract** — All modules follow the same stdin + CLI → inbox → core function → outbox → JSON stdout pattern via `build_inbox_from_args`.
- **Destructurable box interface** — Core functions only bind what they need using language-specific destructuring patterns.
- **Non-destructive box mutation** — Modules preserve and extend the passing box, not replace it.
- **Interop via wrappers, speed via direct calls** — Same-language modules can call each other directly, avoiding shell overhead.
- **Soft schema contracts** — Boxes have loose structure; modules declare required keys but tolerate unknown ones.
- **Composable orchestration** — Pipelines can be expressed as ordered lists of stage functions or shell commands.
- **Complete box input** — All parameters required for a module's operation must be present in the input box, either directly or by reference. This supports functional purity, reproducibility, and testability.
- **Box as common carrier** — The box persists across pipeline stages and may accumulate context not just for immediate use but for downstream consumers, metadata propagation, or audit trail enrichment.
- **Cross-language consistency** — The same wrapper contract works identically across all supported languages (Python, Java, etc.).

---

## Validation, error handling, testing, debugging & logging

### Testing Strategy

- **Unit Tests**: Test core functions directly with various input boxes using destructuring patterns
- **Wrapper Tests**: Validate `main()` wrappers follow the unified contract (stdin + CLI → JSON stdout)
- **Contract Tests**: Verify `build_inbox_from_args` behavior with different input combinations
- **Integration Tests**: Test modules as part of fabricator pipelines and cross-language calls
- **Test Organization**: Use dedicated test files following established mirror patterns (`test_*.py`)

### Error Handling

- Core functions should raise appropriate exceptions for invalid inputs
- Wrappers should catch exceptions and emit error information in JSON output
- Pipeline orchestration should handle module failures gracefully

### Debugging

- Use `--noisy` flags for verbose output during development
- Test individual modules in isolation before pipeline integration
- Validate JSON output structure at each pipeline stage

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
