# SeqWeb Project Context & Initial Directions

## Project Context

**SeqWeb** is to the OEIS as DBpedia is to Wikipedia. The project aims to convert OEIS sequence data (`.seq` files) into semantic web knowledge graphs (`.ttl` files) using a polyglot pipeline architecture.

## Repository Structure

Three repositories are involved:
- **seqwebcode** (main): Code, docs, ontology, tools - this is the primary development repository
- **seqwebdata**: Generated RDF data (.ttl files) 
- **oeisdata**: Read-only OEIS source corpus (.seq files)

## Current Development Phase

**Phase**: Rapid infrastructure and process setup for small team collaboration
**Timeline**: Pre-collaborator onboarding, building foundation for open-source development
**Primary Goal**: Development velocity - get as much in place as possible quickly

## Key Constraints & Priorities

### 1. **Speed Over Perfection**
- Move fast, don't overengineer
- Focus on getting working infrastructure in place
- Prefer simple, practical solutions over complex tooling

### 2. **Quality Foundation**
- Code, collateral, processes, and patterns set now will be the foundation for ongoing work
- Strong emphasis on code quality and maintainability
- Patterns established now will guide future development

### 3. **Team Preparation**
- Building for small team collaboration (open-source contributors)
- Need clear processes and documentation
- Infrastructure should support multiple developers

### 4. **Stakeholder Collateral**
- Generating material for potential sponsors (OEIS Foundation, Indiana University, private donors)
- Documentation and project summaries needed for external communication
- Professional presentation matters

## Technical Architecture

### Polyglot Pipeline
- Modular processing modules in multiple languages (Python, Java, Lisp, Bash)
- Communication via "box" - language-agnostic key-value map
- Plug-and-play modules with standardized interfaces
- Native orchestration + IO wrappers for cross-language execution

### Current Focus Areas
- Core pipeline architecture documentation
- Basic tooling and utilities
- Development environment setup
- Testing infrastructure

## Development Approach

### Tooling Philosophy
- **Minimal viable tooling**: Only build what's immediately needed
- **Low-maintenance solutions**: Prefer simple, reliable tools over complex automation
- **Manual over automatic**: When in doubt, choose manual processes that work reliably
- **Documentation first**: Good docs enable team collaboration and stakeholder communication

### Code Quality Standards
- Clean, readable code with clear interfaces
- Comprehensive documentation
- Testable components
- Consistent patterns across languages

## Recent Context

We were working on the `polyglot-pipeline.md` documentation and discussing low-maintenance ways to keep SVG files in sync with Draw.io drawings. The new practice is for authors and editors to be responsible for exporting up-to-date SVGs from Draw.io and keeping `.drawio` and `.drawio.svg` files together.

## Immediate Next Steps

1. Complete the Draw.io diagrams and documentation
2. Continue polishing the polyglot pipeline documentation
3. Build out basic development infrastructure
4. Prepare project collateral for stakeholders

## Communication Style

- The user is an experienced developer and tech leader
- Prefers direct, practical solutions
- Values speed but not at the expense of quality
- Appreciates context-aware suggestions
- Wants to build patterns that will scale to team collaboration

## Key Files & Locations

- Main documentation: `docs/technical/polyglot-pipeline.md`
- Drawings: `docs/drawings/` (contains .drawio and .drawio.svg files)
- Tools: `tools/` directory for utilities
- Temporary work: `tmp/` directory (gitignored)

---

*This document serves as orientation for AI assistants working on the SeqWeb project. Update as context evolves.* 