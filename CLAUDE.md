# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a code productivity toolkit repository containing agentic rules and workflows designed to enhance AI assistant productivity when working with codebases. The repository focuses on providing structured approaches for common development tasks like code cleaning, committing, documentation generation, and task implementation.

## Repository Structure

- `agentic-rules/` - Contains markdown files defining specific AI assistant workflows and rules
- `claude-wrapper-usage.md` - Documentation for Claude Code terminal wrapper functionality
- `README.md` - Basic project description

## Key Workflow Files

The `agentic-rules/` directory contains workflow definitions:

- `clean.mdc` - Code formatting and quality standards for Python and JavaScript/TypeScript projects
- `commit.mdc` - Conventional commit messaging with emojis and pre-commit checks  
- `commit-fast.mdc` - Streamlined commit process with automatic message selection
- `implement-task.mdc` - Methodical task implementation approach with planning steps
- `context-prime.mdc` - Project context loading and understanding workflow
- `create-docs.mdc` - Comprehensive documentation generation templates
- `update-docs.mdc` - LLM-optimized documentation creation with file references

## Development Commands

This repository doesn't contain traditional build/test commands as it's a collection of workflow definitions rather than executable code. The files are designed to be referenced and applied to other projects.

## Agentic Rules

The following rules are imported to enhance AI assistant productivity:

@import agentic-rules/clean.mdc
@import agentic-rules/commit-fast.mdc
@import agentic-rules/commit.mdc
@import agentic-rules/context-prime.mdc
@import agentic-rules/create-docs.mdc
@import agentic-rules/five.mdc
@import agentic-rules/implement-task.mdc
@import agentic-rules/no-claude-footer.mdc
@import agentic-rules/update-docs.mdc
