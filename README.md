# code-productivity

A collection of agentic rules and workflows designed to enhance AI assistant productivity when working with codebases.

## Quick Start

Add rules to any project using the `add-rules` script:

```bash
# Interactive selection (default)
./add-rules

# Add specific rules
./add-rules -r clean,commit,implement-task

# Add all rules
./add-rules -a

# List available rules  
./add-rules -l

# Different output formats
./add-rules -f cursor      # .cursor/rules/ (default)
./add-rules -f copilot     # .github/copilot-instructions.md
./add-rules -f claude      # agentic-rules/ + @import in CLAUDE.md
./add-rules -f plain       # agentic-rules/
```

## Available Rules

- **clean** - Code formatting and quality fixes for Python/JS/TS projects
- **commit** - Conventional commits with emoji and pre-commit checks  
- **commit-fast** - Streamlined commit process with automatic message selection
- **context-prime** - Load comprehensive project context for AI assistants
- **create-docs** - Generate comprehensive documentation for components
- **implement-task** - Methodical task implementation with careful planning
- **update-docs** - Create LLM-optimized documentation with file references

## Output Formats

- **cursor** - Individual `.md` files in `.cursor/rules/` for Cursor IDE
- **copilot** - Concatenated rules in `.github/copilot-instructions.md` for GitHub Copilot
- **claude** - Original `.mdc` files in `agentic-rules/` with `@import` statements in `CLAUDE.md` for Claude Code
- **plain** - Original `.mdc` files in `agentic-rules/` directory