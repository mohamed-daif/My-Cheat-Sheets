# VS Code Complete Cheat Sheet

## Table of Contents
- [Essential Shortcuts](#essential-shortcuts)
- [Navigation & File Management](#navigation--file-management)
- [Editing & Selection](#editing--selection)
- [Search & Replace](#search--replace)
- [Multi-Cursor & Selection](#multi-cursor--selection)
- [Debugging](#debugging)
- [Integrated Terminal](#integrated-terminal)
- [Version Control](#version-control)
- [Extensions & Customization](#extensions--customization)
- [Workspace & Settings](#workspace--settings)
- [Snippets & Emmet](#snippets--emmet)
- [Command Palette](#command-palette)
- [Performance & Troubleshooting](#performance--troubleshooting)

## Essential Shortcuts

### Global Shortcuts
```bash
Ctrl+Shift+P / Cmd+Shift+P    # Command Palette
Ctrl+` / Cmd+`                # Toggle Terminal
Ctrl+Shift+E / Cmd+Shift+E    # Explorer
Ctrl+Shift+F / Cmd+Shift+F    # Search
Ctrl+Shift+G / Cmd+Shift+G    # Source Control
Ctrl+Shift+D / Cmd+Shift+D    # Debug
Ctrl+Shift+X / Cmd+Shift+X    # Extensions
Ctrl+Shift+U / Cmd+Shift+U    # Output Panel
Ctrl+B / Cmd+B                # Toggle Sidebar
Ctrl+J / Cmd+J                # Toggle Panel
Ctrl+Shift+` / Cmd+Shift+`    # New Terminal
```

### Basic Editing
```bash
Ctrl+S / Cmd+S                # Save
Ctrl+Z / Cmd+Z                # Undo
Ctrl+Y / Cmd+Y                # Redo
Ctrl+X / Cmd+X                # Cut line
Ctrl+C / Cmd+C                # Copy line
Ctrl+V / Cmd+V                # Paste
Ctrl+Shift+K / Cmd+Shift+K    # Delete line
Ctrl+Enter / Cmd+Enter        # Insert line below
Ctrl+Shift+Enter / Cmd+Shift+Enter  # Insert line above
```

## Navigation & File Management

### File Navigation
```bash
Ctrl+P / Cmd+P                # Quick Open (Go to File)
Ctrl+Shift+E / Cmd+Shift+E    # File Explorer
Ctrl+Shift+N / Cmd+Shift+N    # New Window
Ctrl+W / Cmd+W                # Close tab
Ctrl+K Ctrl+W / Cmd+K Cmd+W   # Close all tabs
Ctrl+Tab / Ctrl+Shift+Tab     # Navigate tabs
Ctrl+1, Ctrl+2, ...           # Focus editor groups
Ctrl+K Ctrl+Arrow / Cmd+K Cmd+Arrow  # Split editor
```

### Code Navigation
```bash
Ctrl+G / Cmd+G                # Go to Line
Ctrl+Shift+O / Cmd+Shift+O    # Go to Symbol in file
Ctrl+T / Cmd+T                # Go to Symbol in workspace
F12 / Cmd+Click               # Go to Definition
Alt+F12 / Opt+F12             # Peek Definition
Ctrl+F12 / Cmd+F12            # Go to Implementation
Shift+F12 / Shift+F12         # Find References
F8 / F8                       # Go to next error
Shift+F8 / Shift+F8           # Go to previous error
Ctrl+Shift+M / Cmd+Shift+M    # Problems panel
```

### Editor Management
```bash
Ctrl+K Z / Cmd+K Z            # Zen Mode
Ctrl+B / Cmd+B                # Toggle Sidebar
Ctrl+Shift+F / Cmd+Shift+F    # Toggle Search
Ctrl+Shift+X / Cmd+Shift+X    # Toggle Extensions
Ctrl+Shift+D / Cmd+Shift+D    # Toggle Debug
Ctrl+Shift+U / Cmd+Shift+U    # Toggle Output
Ctrl+Shift+Y / Cmd+Shift+Y    # Toggle Debug Console
```

## Editing & Selection

### Text Manipulation
```bash
Ctrl+D / Cmd+D                # Add selection to next match
Ctrl+U / Cmd+U                # Undo last cursor operation
Ctrl+Shift+L / Cmd+Shift+L    # Select all occurrences
Ctrl+F2 / Cmd+F2              # Select all occurrences of word
Alt+Click / Opt+Click         # Add cursor
Ctrl+Alt+Up/Down / Cmd+Opt+Up/Down  # Add cursors above/below
Ctrl+Shift+Alt+Arrow / Cmd+Shift+Opt+Arrow  # Column selection
```

### Line Operations
```bash
Ctrl+C / Cmd+C                # Copy line (no selection)
Ctrl+X / Cmd+X                # Cut line (no selection)
Ctrl+Shift+K / Cmd+Shift+K    # Delete line
Alt+Up/Down / Opt+Up/Down     # Move line up/down
Shift+Alt+Up/Down / Shift+Opt+Up/Down  # Copy line up/down
Ctrl+Enter / Cmd+Enter        # Insert line below
Ctrl+Shift+Enter / Cmd+Shift+Enter  # Insert line above
Ctrl+Shift+\ / Cmd+Shift+\    # Jump to matching bracket
```

### Code Formatting
```bash
Shift+Alt+F / Shift+Opt+F     # Format document
Ctrl+K Ctrl+F / Cmd+K Cmd+F   # Format selection
F1 > "Format Document With"   # Choose formatter
Ctrl+K Ctrl+0 / Cmd+K Cmd+0   # Fold all regions
Ctrl+K Ctrl+J / Cmd+K Cmd+J   # Unfold all regions
Ctrl+Shift+[ / Cmd+Opt+[      # Fold region
Ctrl+Shift+] / Cmd+Opt+]      # Unfold region
```

## Search & Replace

### Basic Search
```bash
Ctrl+F / Cmd+F                # Find in file
F3 / F3                       # Find next
Shift+F3 / Shift+F3           # Find previous
Ctrl+H / Cmd+H                # Replace in file
Ctrl+Shift+F / Cmd+Shift+F    # Find in files
Ctrl+Shift+H / Cmd+Shift+H    # Replace in files
Alt+Enter / Opt+Enter         # Select all occurrences
```

### Advanced Search
```bash
# In search panel:
.*                            # Regex toggle
Aa                            # Case sensitive toggle
AB                            # Whole word toggle
* in search                   # Use wildcards
$1, $2, ...                   # Capture groups in replace
\n                            # New line in replace
```

## Multi-Cursor & Selection

### Multiple Cursors
```bash
Alt+Click / Opt+Click         # Add cursor
Ctrl+Alt+Up/Down / Cmd+Opt+Up/Down  # Add cursor above/below
Ctrl+Shift+Alt+Arrow / Cmd+Shift+Opt+Arrow  # Column selection
Ctrl+U / Cmd+U                # Undo last cursor operation
Ctrl+L / Cmd+L                # Select current line
Ctrl+Shift+L / Cmd+Shift+L    # Select all occurrences
Ctrl+F2 / Cmd+F2              # Select all occurrences of word
```

### Selection Techniques
```bash
Ctrl+D / Cmd+D                # Add next occurrence to selection
Ctrl+K Ctrl+D / Cmd+K Cmd+D   # Skip occurrence and add next
Shift+Alt+Right / Shift+Opt+Right  # Expand selection
Shift+Alt+Left / Shift+Opt+Left    # Shrink selection
Ctrl+Shift+Space / Cmd+Shift+Space  # Smart selection expand
```

## Debugging

### Debugging Shortcuts
```bash
F5 / F5                       # Start debugging
Ctrl+F5 / Cmd+F5              # Start without debugging
F9 / F9                       # Toggle breakpoint
Ctrl+Shift+F5 / Cmd+Shift+F5  # Restart debugging
Shift+F5 / Shift+F5           # Stop debugging
F10 / F10                     # Step over
F11 / F11                     # Step into
Shift+F11 / Shift+F11         # Step out
Ctrl+Shift+F9 / Cmd+Shift+F9  # Run to cursor
```

### Debug Panel
```bash
Ctrl+Shift+Y / Cmd+Shift+Y    # Debug console
Ctrl+Shift+D / Cmd+Shift+D    # Debug view
Ctrl+Shift+U / Cmd+Shift+U    # Output panel
Watch                         # Add variables to watch
Call Stack                    # View call hierarchy
Breakpoints                   # Manage breakpoints
```

## Integrated Terminal

### Terminal Operations
```bash
Ctrl+` / Cmd+`                # Toggle terminal
Ctrl+Shift+` / Cmd+Shift+`    # Create new terminal
Ctrl+Shift+C / Cmd+Shift+C    # Copy selection
Ctrl+Shift+V / Cmd+Shift+V    # Paste
Ctrl+Up/Down / Cmd+Up/Down    # Scroll terminal
Shift+PageUp/PageDown         # Scroll page
Ctrl+Home/End / Cmd+Home/End  # Scroll to top/bottom
```

### Terminal Management
```bash
Ctrl+Shift+5 / Cmd+Shift+5    # Split terminal
Ctrl+Shift+[ / Cmd+Shift+[    # Focus previous terminal
Ctrl+Shift+] / Cmd+Shift+]    # Focus next terminal
Ctrl+Shift+W / Cmd+Shift+W    # Close terminal
Ctrl+K Ctrl+U / Cmd+K Cmd+U   # Clear terminal
```

## Version Control

### Git Integration
```bash
Ctrl+Shift+G / Cmd+Shift+G    # Source Control panel
Ctrl+Shift+P Git / Cmd+Shift+P Git  # Git commands
Alt+G B / Opt+G B             # Git Blame
Alt+G L / Opt+G L             # Git Log
Alt+G O / Opt+G O             # Git Open File
Alt+G H / Opt+G H             # Git History
```

### Common Git Actions
```bash
# In Source Control panel:
+                             # Stage changes
-                             # Unstage changes
✓                             # Commit
...                           # More actions
# Through Command Palette:
Git: Commit
Git: Push
Git: Pull
Git: Fetch
Git: Checkout to
Git: Create Branch
```

## Extensions & Customization

### Extension Management
```bash
Ctrl+Shift+X / Cmd+Shift+X    # Extensions panel
@popular                       # Popular extensions
@installed                     # Installed extensions
@enabled                       # Enabled extensions
@disabled                      # Disabled extensions
```

### Essential Extensions
```bash
# Language Support:
- Python
- JavaScript/TypeScript
- Java
- C/C++
- C#
- Go
- Rust
- PHP

# Productivity:
- GitLens
- Prettier
- ESLint
- Live Server
- Thunder Client
- Docker
- Remote - SSH
- Settings Sync
```

## Workspace & Settings

### Settings Management
```bash
Ctrl+, / Cmd+,                # Open Settings
{}                            # Open settings.json
Ctrl+Shift+P > Preferences    # Various preference options
```

### Key Settings
```json
{
  "editor.fontSize": 14,
  "editor.tabSize": 2,
  "editor.wordWrap": "on",
  "files.autoSave": "afterDelay",
  "editor.minimap.enabled": true,
  "workbench.colorTheme": "Default Dark+",
  "editor.formatOnSave": true
}
```

### Workspace Features
```bash
Ctrl+K Ctrl+S / Cmd+K Cmd+S   # Keyboard shortcuts
Ctrl+K Ctrl+T / Cmd+K Cmd+T   # Color theme
Ctrl+K Ctrl+H / Cmd+K Cmd+H   # Change language mode
Ctrl+Shift+P > "Developer: Inspect Editor"  # Debug editor
```

## Snippets & Emmet

### Emmet Abbreviations
```html
!                             # HTML5 boilerplate
div.container                 # <div class="container"></div>
ul>li*3                       # <ul><li></li><li></li><li></li></ul>
div#header                    # <div id="header"></div>
p{Hello}                      # <p>Hello</p>
```

### Snippet Usage
```bash
Ctrl+Space / Ctrl+Space       # Trigger suggestions
Tab / Tab                     # Expand snippet
Ctrl+Shift+P > "Insert Snippet"  # Insert specific snippet
```

### Custom Snippets
```json
{
  "Print to console": {
    "prefix": "log",
    "body": [
      "console.log('$1');",
      "$2"
    ],
    "description": "Log output to console"
  }
}
```

## Command Palette

### Essential Commands
```bash
# File Operations:
> New File
> Open File
> Save As
> Compare Files

# Editor:
> Change Language Mode
> Format Document
> Fold All
> Unfold All

# View:
> Toggle Full Screen
> Toggle Zen Mode
> Split Editor

# Extensions:
> Show Installed Extensions
> Install Extensions

# Preferences:
> Open Settings
> Open Keyboard Shortcuts
> Color Theme
```

### Advanced Commands
```bash
> Developer: Reload Window     # Reload VS Code
> Developer: Show Runtime Info # Show performance info
> File: Compare Active File With # Compare files
> Git: Clone                   # Clone repository
> Tasks: Run Task              # Run build tasks
```

## Performance & Troubleshooting

### Performance Optimization
```bash
# Disable extensions:
Ctrl+Shift+P > "Developer: Show Running Extensions"
Ctrl+Shift+P > "Disable All Installed Extensions"

# Memory usage:
Ctrl+Shift+P > "Developer: Open Process Explorer"

# Settings for performance:
{
  "editor.minimap.enabled": false,
  "workbench.enableExperiments": false,
  "extensions.autoUpdate": false
}
```

### Troubleshooting Commands
```bash
Ctrl+Shift+P > "Developer: Toggle Developer Tools"  # Browser dev tools
Ctrl+Shift+P > "Developer: Open Logs Folder"        # Access logs
Ctrl+Shift+P > "Developer: Set Log Level"           # Change log level
```

### Useful Settings for Development
```json
{
  // Editor
  "editor.fontFamily": "'Fira Code', 'Courier New', monospace",
  "editor.fontLigatures": true,
  "editor.lineNumbers": "on",
  "editor.renderWhitespace": "boundary",
  
  // Files
  "files.exclude": {
    "**/node_modules": true,
    "**/.git": true
  },
  
  // Workbench
  "workbench.iconTheme": "vs-seti",
  "workbench.startupEditor": "newUntitledFile",
  
  // Terminal
  "terminal.integrated.shell.linux": "/bin/bash",
  "terminal.integrated.fontSize": 14
}
```

---

## Quick Reference Card

### Most Used Shortcuts
```bash
Ctrl+P / Cmd+P                # Quick Open
Ctrl+` / Cmd+`                # Terminal
Ctrl+Shift+P / Cmd+Shift+P    # Command Palette
Ctrl+S / Cmd+S                # Save
Ctrl+Z / Cmd+Z                # Undo
Ctrl+Shift+K / Cmd+Shift+K    # Delete line
Ctrl+D / Cmd+D                # Select next occurrence
F12 / F12                     # Go to definition
F5 / F5                       # Debug
Ctrl+Shift+E / Cmd+Shift+E    # File Explorer
```

### Essential Extensions
- **GitLens** - Git supercharged
- **Prettier** - Code formatter
- **ESLint** - JavaScript linter
- **Live Server** - Local development server
- **Thunder Client** - API testing
- **Docker** - Container management
- **Remote - SSH** - Remote development

### Pro Tips
1. Use `Ctrl+P > ?` to see available file commands
2. Drag files to editor area to split view
3. Use `Ctrl+K Z` for distraction-free coding
4. Configure user snippets for repetitive code patterns
5. Use multiple cursors with `Ctrl+Alt+Up/Down`
6. Set up keybindings for frequent actions
7. Use workspace settings for project-specific configurations

---

*Remember: VS Code is highly customizable. Take time to configure it to match your workflow and explore extensions that enhance your productivity!*