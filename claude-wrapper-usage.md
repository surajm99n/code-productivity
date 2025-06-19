# Claude Code Wrapper Usage

This script provides enhanced terminal management for Claude Code sessions, similar to the ZSH version from steipete.me but adapted for Bash with Oh-My-Bash.

## Features

- **Smart Terminal Titles**: Automatically sets terminal titles to show current directory + "Claude" status
- **Project Detection**: Enhanced version detects project type (Python, Node.js, Rust, Go, Git)
- **Background Title Management**: Prevents Claude from changing terminal titles unexpectedly
- **Clean Exit Handling**: Properly restores terminal state when Claude exits

## Available Commands

### Basic Usage
```bash
# Start Claude with default dangerous permissions skip
cly

# Start Claude with custom arguments
cly --help
cly --model claude-3-5-sonnet-20241022
```

### Enhanced Project-Aware Version
```bash
# Start Claude with project type detection
clyp

# Shows terminal title like:
# "Claude: kenai (Python)" - for Python projects
# "Claude: my-app (Node.js)" - for Node.js projects
# "Claude: my-project (Git)" - for Git repositories
```

### Quick Aliases
```bash
# Short aliases for convenience
cc          # Same as 'cly'
ccp         # Same as 'clyp' (project-aware)
```

## How It Works

1. **Terminal Title Management**: Sets title to "Claude: [directory-name]" when Claude starts
2. **Background Process**: Continuously resets title to prevent Claude from changing it
3. **Clean Exit**: Properly kills background processes and restores original title
4. **Project Detection**: Looks for common project files to add context to titles

## Installation

The script is already installed and will be loaded automatically when you start a new bash session.

To reload without restarting terminal:
```bash
source ~/.bashrc
```

## Troubleshooting

If terminal titles aren't working:
1. Check if your terminal emulator supports ANSI escape sequences
2. Ensure the script is properly sourced in ~/.bashrc
3. Try manually: `source ~/.config/bash/claude-wrapper.sh`

## Customization

Edit `~/.config/bash/claude-wrapper.sh` to:
- Add more project type detection
- Change title formats
- Modify default Claude arguments
- Add additional aliases