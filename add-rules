#!/usr/bin/env bash

# add-rules - Add agentic rules to a project
# Inspired by https://github.com/steipete/agent-rules

set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
RULES_DIR="$SCRIPT_DIR/agentic-rules"

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Print colored output
print_info() { echo -e "${BLUE}ℹ${NC} $1"; }
print_success() { echo -e "${GREEN}✓${NC} $1"; }
print_warning() { echo -e "${YELLOW}⚠${NC} $1"; }
print_error() { echo -e "${RED}✗${NC} $1"; }

# Available rules with descriptions
declare -A RULES_DESC=(
    ["clean"]="Code formatting and quality fixes for Python/JS/TS projects"
    ["commit"]="Conventional commits with emoji and pre-commit checks"
    ["commit-fast"]="Streamlined commit process with automatic message selection"
    ["context-prime"]="Load comprehensive project context for AI assistants"
    ["create-docs"]="Generate comprehensive documentation for components"
    ["implement-task"]="Methodical task implementation with careful planning"
    ["update-docs"]="Create LLM-optimized documentation with file references"
)

usage() {
    cat << EOF
Usage: $0 [OPTIONS] [TARGET_DIR]

Add agentic rules to a project directory.

OPTIONS:
    -h, --help          Show this help message
    -l, --list          List available rules
    -a, --all           Add all available rules
    -r, --rules RULES   Comma-separated list of rules to add
    -i, --interactive   Interactive rule selection (default)
    -f, --format FORMAT Output format: cursor|copilot|claude|plain (default: cursor)
    --dry-run          Show what would be copied without actually copying

TARGET_DIR:
    Directory to add rules to (default: current directory)

EXAMPLES:
    $0                           # Interactive selection in current directory
    $0 -a /path/to/project       # Add all rules to project
    $0 -r clean,commit ~/project # Add specific rules
    $0 -l                        # List available rules
    $0 --format copilot          # Use GitHub Copilot format (.github/copilot-instructions.md)
    $0 --format claude           # Use Claude Code format with @import in CLAUDE.md

FORMATS:
    cursor   - Create .cursor/rules/ directory with individual .md files
    copilot  - Create .github/copilot-instructions.md with concatenated rules
    claude   - Create agentic-rules/ directory and add @import statements to CLAUDE.md
    plain    - Create agentic-rules/ directory with .mdc files (original format)
EOF
}

list_rules() {
    print_info "Available agentic rules:"
    echo
    for rule in $(ls "$RULES_DIR"/*.mdc | xargs -n1 basename | sed 's/.mdc$//' | sort); do
        printf "  %-15s %s\n" "$rule" "${RULES_DESC[$rule]:-No description available}"
    done
}

interactive_selection() {
    local selected_rules=()
    
    print_info "Select rules to add (use space to toggle, enter to confirm):"
    echo
    
    local rules=($(ls "$RULES_DIR"/*.mdc | xargs -n1 basename | sed 's/.mdc$//' | sort))
    local selected=()
    
    # Initialize selection array
    for rule in "${rules[@]}"; do
        selected+=(false)
    done
    
    local current=0
    
    while true; do
        clear
        print_info "Select agentic rules to add:"
        echo
        
        for i in "${!rules[@]}"; do
            local prefix="  "
            if [ $i -eq $current ]; then
                prefix="> "
            fi
            
            local checkbox="[ ]"
            if [ "${selected[$i]}" = "true" ]; then
                checkbox="[x]"
            fi
            
            printf "%s%s %-15s %s\n" "$prefix" "$checkbox" "${rules[$i]}" "${RULES_DESC[${rules[$i]}]:-}"
        done
        
        echo
        echo "Controls: ↑/↓ navigate, SPACE toggle, ENTER confirm, q quit"
        
        read -rsn1 key
        case "$key" in
            $'\x1b') # Arrow keys
                read -rsn2 -t 0.1 key
                case "$key" in
                    '[A') # Up
                        ((current > 0)) && ((current--))
                        ;;
                    '[B') # Down  
                        ((current < ${#rules[@]} - 1)) && ((current++))
                        ;;
                esac
                ;;
            ' ') # Space - toggle selection
                if [ "${selected[$current]}" = "true" ]; then
                    selected[$current]=false
                else
                    selected[$current]=true
                fi
                ;;
            $'\n'|$'\r') # Enter - confirm
                break
                ;;
            'q'|'Q') # Quit
                print_info "Cancelled."
                exit 0
                ;;
        esac
    done
    
    # Build selected rules array
    for i in "${!rules[@]}"; do
        if [ "${selected[$i]}" = "true" ]; then
            selected_rules+=("${rules[$i]}")
        fi
    done
    
    if [ ${#selected_rules[@]} -eq 0 ]; then
        print_warning "No rules selected."
        exit 0
    fi
    
    echo "${selected_rules[@]}"
}

validate_rules() {
    local rules_list="$1"
    local invalid_rules=()
    
    IFS=',' read -ra RULES_ARRAY <<< "$rules_list"
    for rule in "${RULES_ARRAY[@]}"; do
        rule=$(echo "$rule" | xargs) # trim whitespace
        if [[ ! -f "$RULES_DIR/$rule.mdc" ]]; then
            invalid_rules+=("$rule")
        fi
    done
    
    if [ ${#invalid_rules[@]} -gt 0 ]; then
        print_error "Invalid rules: ${invalid_rules[*]}"
        print_info "Run '$0 -l' to see available rules"
        exit 1
    fi
}

# Check if file exists and compare contents
file_needs_update() {
    local src_file="$1"
    local dest_file="$2"
    
    # If destination doesn't exist, it needs to be created
    if [ ! -f "$dest_file" ]; then
        return 0
    fi
    
    # Compare file contents
    if ! cmp -s "$src_file" "$dest_file"; then
        return 0  # Files differ, update needed
    fi
    
    return 1  # Files are identical, no update needed
}

copy_rules() {
    local rules_list="$1"
    local target_dir="$2"
    local format="$3"
    local dry_run="$4"
    
    IFS=',' read -ra RULES_ARRAY <<< "$rules_list"
    
    case "$format" in
        "cursor")
            local dest_dir="$target_dir/.cursor/rules"
            if [ "$dry_run" = "false" ]; then
                mkdir -p "$dest_dir"
            fi
            
            for rule in "${RULES_ARRAY[@]}"; do
                rule=$(echo "$rule" | xargs) # trim whitespace
                local src_file="$RULES_DIR/$rule.mdc"
                local dest_file="$dest_dir/$rule.md"
                
                if file_needs_update "$src_file" "$dest_file"; then
                    if [ "$dry_run" = "true" ]; then
                        if [ -f "$dest_file" ]; then
                            print_info "Would update: $src_file -> $dest_file"
                        else
                            print_info "Would copy: $src_file -> $dest_file"
                        fi
                    else
                        cp "$src_file" "$dest_file"
                        if [ -f "$dest_file" ]; then
                            print_success "Updated rule: $rule -> .cursor/rules/$rule.md"
                        else
                            print_success "Added rule: $rule -> .cursor/rules/$rule.md"
                        fi
                    fi
                else
                    if [ "$dry_run" = "true" ]; then
                        print_info "Would skip (unchanged): $rule"
                    else
                        print_warning "Skipped (unchanged): $rule"
                    fi
                fi
            done
            ;;
            
        "copilot")
            local dest_file="$target_dir/.github/copilot-instructions.md"
            local needs_rebuild=false
            
            # Check if any rules need updating for copilot format
            for rule in "${RULES_ARRAY[@]}"; do
                rule=$(echo "$rule" | xargs) # trim whitespace
                local src_file="$RULES_DIR/$rule.mdc"
                
                # For copilot format, we need to check if the rule content exists in the combined file
                if [ ! -f "$dest_file" ] || ! grep -q "## $(basename "$rule" .mdc | tr '[:lower:]' '[:upper:]' | tr '-' ' ')" "$dest_file"; then
                    needs_rebuild=true
                    break
                fi
                
                # Extract rule content from combined file and compare with source
                local rule_header="## $(basename "$rule" .mdc | tr '[:lower:]' '[:upper:]' | tr '-' ' ')"
                if grep -q "$rule_header" "$dest_file"; then
                    # Extract the rule section and compare (this is a simplified check)
                    local temp_file=$(mktemp)
                    sed -n "/^$rule_header$/,/^---$/p" "$dest_file" | head -n -2 | tail -n +3 > "$temp_file"
                    if ! cmp -s "$src_file" "$temp_file"; then
                        needs_rebuild=true
                        rm -f "$temp_file"
                        break
                    fi
                    rm -f "$temp_file"
                fi
            done
            
            if [ "$needs_rebuild" = "true" ]; then
                if [ "$dry_run" = "true" ]; then
                    if [ -f "$dest_file" ]; then
                        print_info "Would update: $dest_file"
                    else
                        print_info "Would create: $dest_file"
                    fi
                else
                    mkdir -p "$(dirname "$dest_file")"
                    echo "# GitHub Copilot Instructions" > "$dest_file"
                    echo "" >> "$dest_file"
                    echo "This file contains agentic rules for GitHub Copilot." >> "$dest_file"
                    echo "" >> "$dest_file"
                    
                    for rule in "${RULES_ARRAY[@]}"; do
                        rule=$(echo "$rule" | xargs) # trim whitespace
                        local src_file="$RULES_DIR/$rule.mdc"
                        
                        echo "## $(basename "$rule" .mdc | tr '[:lower:]' '[:upper:]' | tr '-' ' ')" >> "$dest_file"
                        echo "" >> "$dest_file"
                        cat "$src_file" >> "$dest_file"
                        echo "" >> "$dest_file"
                        echo "---" >> "$dest_file"
                        echo "" >> "$dest_file"
                    done
                    
                    print_success "Updated copilot instructions: .github/copilot-instructions.md"
                fi
            else
                if [ "$dry_run" = "true" ]; then
                    print_info "Would skip (unchanged): copilot instructions"
                else
                    print_warning "Skipped (unchanged): copilot instructions"
                fi
            fi
            ;;
            
        "claude")
            local dest_dir="$target_dir/agentic-rules"
            local claude_file="$target_dir/CLAUDE.md"
            
            if [ "$dry_run" = "false" ]; then
                mkdir -p "$dest_dir"
            fi
            
            # Copy .mdc files first
            for rule in "${RULES_ARRAY[@]}"; do
                rule=$(echo "$rule" | xargs) # trim whitespace
                local src_file="$RULES_DIR/$rule.mdc"
                local dest_file="$dest_dir/$rule.mdc"
                
                if file_needs_update "$src_file" "$dest_file"; then
                    if [ "$dry_run" = "true" ]; then
                        if [ -f "$dest_file" ]; then
                            print_info "Would update: $src_file -> $dest_file"
                        else
                            print_info "Would copy: $src_file -> $dest_file"
                        fi
                    else
                        cp "$src_file" "$dest_file"
                        if [ -f "$dest_file" ]; then
                            print_success "Updated rule: $rule -> agentic-rules/$rule.mdc"
                        else
                            print_success "Added rule: $rule -> agentic-rules/$rule.mdc"
                        fi
                    fi
                else
                    if [ "$dry_run" = "true" ]; then
                        print_info "Would skip (unchanged): $rule"
                    else
                        print_warning "Skipped (unchanged): $rule"
                    fi
                fi
            done
            
            # Handle CLAUDE.md file
            if [ "$dry_run" = "true" ]; then
                if [ -f "$claude_file" ]; then
                    print_info "Would add @import statements to existing CLAUDE.md"
                else
                    print_info "Would create CLAUDE.md with @import statements"
                fi
            else
                # Check if CLAUDE.md exists
                if [ ! -f "$claude_file" ]; then
                    # Create basic CLAUDE.md structure
                    cat > "$claude_file" << 'EOF'
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Agentic Rules

The following rules are imported to enhance AI assistant productivity:

EOF
                    print_success "Created CLAUDE.md"
                fi
                
                # Add @import statements for each rule
                local import_section_exists=false
                if grep -q "## Agentic Rules" "$claude_file"; then
                    import_section_exists=true
                fi
                
                if [ "$import_section_exists" = "false" ]; then
                    echo "" >> "$claude_file"
                    echo "## Agentic Rules" >> "$claude_file"
                    echo "" >> "$claude_file"
                    echo "The following rules are imported to enhance AI assistant productivity:" >> "$claude_file"
                    echo "" >> "$claude_file"
                fi
                
                # Check which imports already exist to avoid duplicates
                for rule in "${RULES_ARRAY[@]}"; do
                    rule=$(echo "$rule" | xargs) # trim whitespace
                    local import_line="@import agentic-rules/$rule.mdc"
                    
                    if ! grep -q "$import_line" "$claude_file"; then
                        echo "$import_line" >> "$claude_file"
                        print_success "Added @import for $rule to CLAUDE.md"
                    else
                        print_warning "@import for $rule already exists in CLAUDE.md"
                    fi
                done
            fi
            ;;
            
        "plain")
            local dest_dir="$target_dir/agentic-rules"
            if [ "$dry_run" = "false" ]; then
                mkdir -p "$dest_dir"
            fi
            
            for rule in "${RULES_ARRAY[@]}"; do
                rule=$(echo "$rule" | xargs) # trim whitespace
                local src_file="$RULES_DIR/$rule.mdc"
                local dest_file="$dest_dir/$rule.mdc"
                
                if file_needs_update "$src_file" "$dest_file"; then
                    if [ "$dry_run" = "true" ]; then
                        if [ -f "$dest_file" ]; then
                            print_info "Would update: $src_file -> $dest_file"
                        else
                            print_info "Would copy: $src_file -> $dest_file"
                        fi
                    else
                        cp "$src_file" "$dest_file"
                        if [ -f "$dest_file" ]; then
                            print_success "Updated rule: $rule -> agentic-rules/$rule.mdc"
                        else
                            print_success "Added rule: $rule -> agentic-rules/$rule.mdc"
                        fi
                    fi
                else
                    if [ "$dry_run" = "true" ]; then
                        print_info "Would skip (unchanged): $rule"
                    else
                        print_warning "Skipped (unchanged): $rule"
                    fi
                fi
            done
            ;;
    esac
}

# Parse command line arguments
INTERACTIVE=true
LIST_ONLY=false
ADD_ALL=false
RULES_LIST=""
FORMAT="cursor"
DRY_RUN=false
TARGET_DIR="."

while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            exit 0
            ;;
        -l|--list)
            LIST_ONLY=true
            shift
            ;;
        -a|--all)
            ADD_ALL=true
            INTERACTIVE=false
            shift
            ;;
        -r|--rules)
            RULES_LIST="$2"
            INTERACTIVE=false
            shift 2
            ;;
        -i|--interactive)
            INTERACTIVE=true
            shift
            ;;
        -f|--format)
            FORMAT="$2"
            shift 2
            ;;
        --dry-run)
            DRY_RUN=true
            shift
            ;;
        -*)
            print_error "Unknown option: $1"
            usage
            exit 1
            ;;
        *)
            TARGET_DIR="$1"
            shift
            ;;
    esac
done

# Validate format
if [[ ! "$FORMAT" =~ ^(cursor|copilot|claude|plain)$ ]]; then
    print_error "Invalid format: $FORMAT. Must be cursor, copilot, claude, or plain."
    exit 1
fi

# Validate target directory
if [[ ! -d "$TARGET_DIR" ]]; then
    print_error "Target directory does not exist: $TARGET_DIR"
    exit 1
fi

# Handle list-only mode
if [ "$LIST_ONLY" = true ]; then
    list_rules
    exit 0
fi

# Handle add-all mode
if [ "$ADD_ALL" = true ]; then
    RULES_LIST=$(ls "$RULES_DIR"/*.mdc | xargs -n1 basename | sed 's/.mdc$//' | paste -sd,)
fi

# Handle interactive mode
if [ "$INTERACTIVE" = true ] && [ -z "$RULES_LIST" ]; then
    if command -v tput >/dev/null 2>&1; then
        RULES_LIST=$(interactive_selection | tr ' ' ',')
        clear
    else
        print_warning "Interactive mode requires 'tput' command. Falling back to list mode."
        list_rules
        echo
        read -p "Enter rules to add (comma-separated): " RULES_LIST
    fi
fi

# Validate rules
if [ -z "$RULES_LIST" ]; then
    print_error "No rules specified. Use -l to list available rules."
    exit 1
fi

validate_rules "$RULES_LIST"

# Show summary
if [ "$DRY_RUN" = true ]; then
    print_info "DRY RUN - No files will be modified"
fi

print_info "Target directory: $TARGET_DIR"
print_info "Format: $FORMAT"
print_info "Rules to add: $(echo "$RULES_LIST" | tr ',' ' ')"
echo

# Copy rules
copy_rules "$RULES_LIST" "$TARGET_DIR" "$FORMAT" "$DRY_RUN"

echo
if [ "$DRY_RUN" = false ]; then
    print_success "Successfully added agentic rules to $TARGET_DIR"
    
    case "$FORMAT" in
        "cursor")
            print_info "Rules added to .cursor/rules/ directory for Cursor IDE"
            ;;
        "copilot")
            print_info "Rules added to .github/copilot-instructions.md for GitHub Copilot"
            ;;
        "claude")
            print_info "Rules added to agentic-rules/ directory with @import statements in CLAUDE.md"
            ;;
        "plain")
            print_info "Rules added to agentic-rules/ directory in original format"
            ;;
    esac
else
    print_info "Dry run completed. Use without --dry-run to actually copy files."
fi