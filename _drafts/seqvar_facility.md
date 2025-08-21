# SeqWeb Dev Env Var System

This describes the design & implementation of the  _SeqWeb Dev Env Var System_ aka the `seqvar` facility, a simple key-value map for variable config and runtime information that is available and shared between all the various components of the SeqWeb system.  

In essence the `seqvar` facility provides functionality analogous to standard OS environment variables, but it is fully managed by the SeqWeb system, rather than the OS.

However unlike OS env vars, the `seqvar` bindings are persistent, remaining in place after process exit, and are ubiquitously available, rather than requiring exports or similar scope management.  

Keys and values are both strings.  When non-string values are needed the providers and consumers can simply use serialized JSON objects as the string values and do the required conversions.  Keys are assumed to be simple extended alphanumeric strings, optionally with embedded dots used to provide hierarchical structure.

Persistent storage for the `seqvar` bindings is implemented using a simple SQLite database, whose backing files are kept in the SeqWeb dev home folder.  

Standardized serialization support for ingesting or dumping the database bindings is provided using the TOML format.  This provides a user-friendly pattern for initial config files and other human-authored "independent" variable bindings that is also well-aligned for program-generated "dependent" variables.

An example might involve a config file where an author specifies something like
```toml
  [service]
host = "api.foo.com"
port = "8080"
```
These bindings are then later concatenated and cached via code like
```python
    host = seqvar-get("service.host")
    port = seqvar-get("service.port")
    url = "https://" + host + ":" + port
    seqvar-set("service.url",  url)
```

Standard wrapper functions for getting and setting `seqvar`s, loading TOML files, etc in each of the supported languages are provided by the libraries in the corresponding `seqwebcode/src/` folder.

The `seqvar` facility is established as an immediate sequel to the SeqWeb bootstrap process.  When the CLI is started it first ensures that the necessary foundation has been initialized, and then utilizes the facility to provide context for command execution.

Additional implementation specifics:

- By convention, the SQLite database file is created in $SEQWEBDEV_HOME/.state/env.sqlite. This keeps the SeqWeb system-managed state clearly separated from user-authored configuration files.

- Schema and Data Model
  - Each binding in the database has: namespace, key, value, source, timestamp.
  - Keys are unique within a namespace.
  - Values are always strings (which can be serialized JSON blobs)

```SQL
CREATE TABLE IF NOT EXISTS seqvars(
  ns   TEXT NOT NULL,
  key  TEXT NOT NULL,
  val  TEXT NOT NULL DEFAULT '',
  src  TEXT,
  ts   INTEGER NOT NULL,
  PRIMARY KEY(ns,key)
);
```

- Polyglot "shim" APIs:
  - Python: seqvar.get() / seqvar.set()
  - Java: SeqVar.get() / SeqVar.set()
  - Bash: seqvar_get / seqvar_set
  - Lisp: seqvar-get / seqvar-set

 _Note: in addition to these primitives, Lisp also supports (seqvar var) and (setf (seqvar var) val)_
