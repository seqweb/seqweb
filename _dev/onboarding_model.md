# SeqWeb Developer Onboarding Model

## Overview
A lightweight, progressive onboarding approach that starts with documentation and gradually adds development capabilities.

## Onboarding Flow

### Phase 0: Discovery & Orientation
- Developer consults seqweb.org website
- Finds contributor documentation and onboarding guide
- Follows initial setup instructions

### Phase 1: Lightweight Start (`seqweb` repo)
**Initial Repository**: `seqweb` (documentation-focused)
- **Rationale**:
  - Most generic and discoverable
  - Minimal environmental impact (primarily documentation)
  - Lightest footprint for initial exploration
- **Activities**:
  - Clone `seqweb` repository
  - Review project structure and documentation
  - Understand project scope and goals

### Phase 2: Planning & Configuration
- **Environment Planning**:
  - Identify locations for additional repos
  - Plan system installations (triple store, Jekyll, etc.)
  - Configure development tools (Cursor, PyCharm, etc.)
- **Personalization**:
  - Set up SeqWeb-specific tooling preferences
  - Configure IDE settings and extensions

### Phase 3: Development Setup (`seqwebcode` + data repos)
- **Core Development**:
  - Clone `seqwebcode` repository (contains CLI tools, core codebase)
  - Perform development-oriented configuration
  - Set up build environments and dependencies
- **Data Access**:
  - Clone `oeisdata` repository (read-only source data)
  - Clone `seqwebdata` repository (generated RDF data)
- **Optional**: Clone `.github` repository (org-level development, admin-only)

## Key Principles
1. **Progressive Complexity**: Start simple, add capabilities incrementally
2. **Minimal Initial Impact**: First repo has light footprint
3. **Self-Directed**: Documentation guides the process
4. **Flexible**: Developers can customize their setup path

## Benefits
- Reduces initial barrier to entry
- Allows exploration before heavy investment
- Supports different developer backgrounds and preferences
- Maintains discoverability and accessibility

## Analysis & Suggestions

### Potential Issues & Improvements

**1. Repository Dependencies**
- **Issue**: `seqwebcode` contains CLI tools that might be needed for basic operations
- **Suggestion**: Consider creating a minimal "starter kit" in `seqweb` that includes essential tools

**2. Documentation Location**
- **Issue**: Onboarding docs are in `seqweb` but dev tools are in `seqwebcode`
- **Suggestion**: Create clear cross-references and ensure `seqweb` docs can guide users to `seqwebcode` tools

**3. Environment Consistency**
- **Issue**: Different developers might set up environments differently
- **Suggestion**: Provide environment templates and validation scripts

## Implementation/Migration Plan

### Phase 1: Documentation & Structure
1. **Create onboarding documentation** in `seqweb/docs/`
   - Step-by-step guide
   - Environment setup instructions
   - Troubleshooting section

2. **Update `seqweb` README** to be contributor-friendly
   - Clear "Getting Started" section
   - Links to detailed onboarding docs

3. **Create cross-repository navigation**
   - Clear paths from `seqweb` → `seqwebcode`
   - Consistent documentation structure

### Phase 2: Tooling & Automation
1. **Create onboarding scripts** in `seqwebcode/tools/`
   - Environment validation
   - Repository cloning automation
   - Configuration setup

2. **Develop CLI tools** for common onboarding tasks
   - `seqweb setup` command
   - `seqweb validate` for environment checks

### Phase 3: Validation & Refinement
1. **Test onboarding flow** with new contributors
2. **Gather feedback** and iterate
3. **Document common issues** and solutions

## Next Steps
1. **Immediate**: Create basic onboarding documentation in `seqweb/docs/`
2. **Short-term**: Update README files to reflect new flow
3. **Medium-term**: Develop onboarding automation tools
4. **Long-term**: Establish contributor feedback loop 