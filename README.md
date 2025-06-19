# Code Productivity Toolkit

A comprehensive collection of agentic rules and workflows designed to enhance AI assistant productivity when working with codebases. This toolkit provides structured approaches for common development tasks like code cleaning, committing, documentation generation, and methodical task implementation.

## Quick Start

Add rules to any project using the `add-rules.sh` script:

```bash
# Interactive selection (default)
./add-rules.sh

# Add specific rules
./add-rules.sh -r clean,commit,implement-task

# Add all rules
./add-rules.sh -a

# List available rules  
./add-rules.sh -l

# Different output formats
./add-rules.sh -f cursor      # .cursor/rules/ (default)
./add-rules.sh -f copilot     # .github/copilot-instructions.md
./add-rules.sh -f claude      # agentic-rules/ + @import in CLAUDE.md
./add-rules.sh -f plain       # agentic-rules/
```

## Available Rules

### Core Development Rules
- **clean** - Code formatting and quality fixes for Python/JS/TS projects using industry-standard tools
- **commit** - Conventional commits with emoji and comprehensive pre-commit checks  
- **commit-fast** - Streamlined commit process with automatic message selection
- **implement-task** - Methodical task implementation with careful planning and validation steps

### Context & Documentation Rules
- **context-prime** - Load comprehensive project context for AI assistants
- **create-docs** - Generate comprehensive documentation for components and APIs
- **update-docs** - Create LLM-optimized documentation with precise file references

### Analysis & Development Philosophy Rules
- **five** - Five Whys root cause analysis technique for problem-solving
- **minimum-viable-change** - Implement the smallest effective changes to solve problems
- **single-task-focus** - Maintain focus on one task at a time for better outcomes
- **sustainable-dev** - Sustainable development practices for long-term maintainability

### Language-Specific Rules
- **python-uv** - Modern Python package management using `uv` instead of `pip`

### Git & Version Control Rules
- **no-claude-footer** - Clean git commits without AI attribution footers

## Output Formats

The toolkit supports multiple output formats to integrate with different AI coding assistants:

- **cursor** - Individual `.md` files in `.cursor/rules/` for Cursor IDE
- **copilot** - Concatenated rules in `.github/copilot-instructions.md` for GitHub Copilot  
- **claude** - Original `.mdc` files in `agentic-rules/` with `@import` statements in `CLAUDE.md` for Claude Code
- **plain** - Original `.mdc` files in `agentic-rules/` directory (no integration)

## Repository Structure

```
code-productivity/
├── agentic-rules/           # Rule definitions in .mdc format
│   ├── clean.mdc           # Code quality and formatting
│   ├── commit.mdc          # Conventional commits with pre-commit
│   ├── commit-fast.mdc     # Streamlined commits
│   ├── context-prime.mdc   # Project context loading
│   ├── create-docs.mdc     # Documentation generation
│   ├── five.mdc            # Five Whys analysis
│   ├── implement-task.mdc  # Task implementation workflow
│   ├── minimum-viable-change.mdc  # Minimal effective changes
│   ├── no-claude-footer.mdc       # Clean git commits without AI attribution
│   ├── python-uv.mdc       # Python uv package manager
│   ├── single-task-focus.mdc      # Single task focus
│   ├── sustainable-dev.mdc        # Sustainable development
│   └── update-docs.mdc     # Documentation updates
├── add-rules.sh*           # Rule deployment script
├── CLAUDE.md              # Claude Code integration file
└── README.md              # This file
```

## How It Works

1. **Rule Definition**: Each rule is defined in a `.mdc` (Markdown Component) file containing specific workflows and instructions
2. **Deployment**: The `add-rules` script copies rules to your project in the appropriate format for your AI assistant
3. **Integration**: Rules are automatically loaded by your AI assistant when working in the project directory
4. **Execution**: AI assistants follow the structured workflows defined in the rules for consistent, high-quality results

## Benefits

- **Consistency**: Standardized workflows across all projects
- **Quality**: Industry best practices baked into every rule  
- **Efficiency**: Automated common tasks with proven approaches
- **Flexibility**: Mix and match rules based on project needs
- **Multi-Platform**: Support for major AI coding assistants

## Contributing

Rules are designed to be modular and reusable. Each rule should:
- Focus on a specific workflow or task
- Include clear step-by-step instructions
- Follow established best practices
- Be tool-agnostic when possible
- Include validation steps where appropriate