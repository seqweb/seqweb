# Development Notes

## Project Context
**Mission**: *SeqWeb is to the OEIS as DBpedia is to Wikipedia* - creating a semantic web knowledge graph from OEIS sequence data.

**Core Workflow**: Convert OEIS .seq files → RDF .ttl files (1:1 correspondence)

## Repository Structure

- **oeisdata** (read-only): Source .seq files from OEIS
- **seqwebdata**: Generated .ttl files (1:1 with .seq files)
- **seqwebcode**: Everything else (code, docs, ontology, etc.)

## Key Technical Decisions

- **Languages**: Java, Python, Common Lisp, Bash
- **Data Format**: RDF/Turtle (.ttl) for semantic web compatibility
- **Ontology**: Custom SeqWeb ontology for OEIS and mathematical sequence concepts
- **Architecture**: Pipeline approach (parse → transform → validate → output)

## Development Priorities

1. Establish ontology and data models
2. Build .seq file parser
3. Create RDF conversion pipeline
4. Implement validation and quality checks
5. Develop web services/APIs

## Notes

- OEIS data is authoritative source (read-only)
- Need to handle mathematical notation and concepts
- Consider performance for large-scale conversion
- Maintain provenance and attribution to OEIS 