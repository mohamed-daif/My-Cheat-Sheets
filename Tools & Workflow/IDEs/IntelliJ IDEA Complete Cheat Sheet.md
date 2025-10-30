# IntelliJ IDEA Complete Cheat Sheet

## Table of Contents

- [Essential Shortcuts](#essential-shortcuts)
- [Navigation & Search](#navigation--search)
- [Editing & Selection](#editing--selection)
- [Refactoring](#refactoring)
- [Code Generation](#code-generation)
- [Debugging](#debugging)
- [Version Control](#version-control)
- [Build & Run](#build--run)
- [Testing](#testing)
- [Database Tools](#database-tools)
- [Plugins & Customization](#plugins--customization)
- [Project Structure](#project-structure)
- [Performance & Troubleshooting](#performance--troubleshooting)

## Essential Shortcuts

### Global Shortcuts

```bash
# Windows/Linux | macOS
Ctrl+Shift+A | Cmd+Shift+A      # Find Action
Double Shift | Double Shift     # Search Everywhere
Ctrl+E | Cmd+E                  # Recent Files
Ctrl+Shift+E | Cmd+Shift+E      # Recent Locations
Ctrl+N | Cmd+O                  # Go to Class
Ctrl+Shift+N | Cmd+Shift+O      # Go to File
Ctrl+Alt+Shift+N | Cmd+Opt+O    # Go to Symbol
Alt+Home | Opt+Home             # Navigation Bar
Ctrl+Tab | Ctrl+Tab             # Switcher
Ctrl+Alt+S | Cmd+,              # Open Settings
Ctrl+Alt+Shift+S | Cmd+;        # Project Structure
```

### Basic Operations

```bash
# Windows/Linux | macOS
Ctrl+S | Cmd+S                  # Save
Ctrl+Z | Cmd+Z                  # Undo
Ctrl+Shift+Z | Cmd+Shift+Z      # Redo
Ctrl+X | Cmd+X                  # Cut
Ctrl+C | Cmd+C                  # Copy
Ctrl+V | Cmd+V                  # Paste
Ctrl+D | Cmd+D                  # Duplicate Line
Ctrl+Y | Cmd+Delete             # Delete Line
Ctrl+Shift+J | Cmd+Shift+J      # Join Lines
Ctrl+Alt+L | Cmd+Opt+L          # Reformat Code
```

## Navigation & Search

### Code Navigation

```bash
# Windows/Linux | macOS
Ctrl+B | Cmd+B                  # Go to Declaration
Ctrl+Click | Cmd+Click          # Go to Declaration
Ctrl+Alt+B | Cmd+Opt+B          # Go to Implementation
Ctrl+U | Cmd+U                  # Go to Super Method
Ctrl+Shift+B | Cmd+Shift+B      # Go to Type Declaration
Alt+F7 | Opt+F7                 # Find Usages
Ctrl+F7 | Cmd+F7                # Find Usages in File
Ctrl+Alt+F7 | Cmd+Opt+F7        # Show Usages
```

### File & Symbol Navigation

```bash
# Windows/Linux | macOS
Ctrl+E | Cmd+E                  # Recent Files Popup
Ctrl+Shift+E | Cmd+Shift+E      # Recent Locations
Ctrl+Shift+Backspace | Cmd+Shift+Backspace  # Last Edit Location
Alt+Right/Left | Ctrl+Shift+Right/Left  # Navigate Back/Forward
F12 | F12                       # Jump to Last Tool Window
Esc | Esc                       # Return to Editor
Shift+Esc | Shift+Esc           # Hide Active Tool Window
```

### Search & Replace

```bash
# Windows/Linux | macOS
Ctrl+F | Cmd+F                  # Find in File
F3 | F3                         # Find Next
Shift+F3 | Shift+F3             # Find Previous
Ctrl+R | Cmd+R                  # Replace in File
Ctrl+Shift+F | Cmd+Shift+F      # Find in Path
Ctrl+Shift+R | Cmd+Shift+R      # Replace in Path
Ctrl+F7 | Cmd+F7                # Highlight Usages in File
Alt+J | Ctrl+G                  # Select Next Occurrence
Alt+Shift+J | Ctrl+Shift+G      # Unselect Occurrence
```

## Editing & Selection

### Text Manipulation

```bash
# Windows/Linux | macOS
Ctrl+W | Opt+Up                 # Extend Selection
Ctrl+Shift+W | Opt+Down         # Shrink Selection
Ctrl+Shift+U | Cmd+Shift+U      # Toggle Case
Ctrl+Shift+J | Cmd+Shift+J      # Join Lines
Ctrl+Alt+T | Cmd+Opt+T          # Surround With
Ctrl+Alt+Shift+T | Cmd+Opt+Shift+T  # Refactor This
Ctrl+Delete | Opt+Delete        # Delete to Word End
Ctrl+Backspace | Opt+Backspace  # Delete to Word Start
```

### Multiple Carets

```bash
# Windows/Linux | macOS
Ctrl+Click | Cmd+Click          # Add/Remove Caret
Alt+Click | Opt+Click           # Add/Remove Caret
Ctrl+Shift+Click | Cmd+Shift+Click  # Add/Remove Caret
Alt+J | Ctrl+G                  # Add Selection for Next Occurrence
Alt+Shift+J | Ctrl+Shift+G      # Unselect Occurrence
Ctrl+Alt+Shift+J | Cmd+Ctrl+G   # Select All Occurrences
```

### Code Formatting

```bash
# Windows/Linux | macOS
Ctrl+Alt+L | Cmd+Opt+L          # Reformat Code
Ctrl+Alt+Shift+L | Cmd+Opt+Shift+L  # Reformat with Dialog
Ctrl+Alt+I | Cmd+Opt+I          # Auto-Indent Lines
Tab | Tab                       # Indent Selection
Shift+Tab | Shift+Tab           # Unindent Selection
Ctrl+Shift+J | Cmd+Shift+J      # Join Lines
Ctrl+Alt+Shift+J | Cmd+Opt+Shift+J  # Join Lines with Space
```

## Refactoring

### Common Refactorings

```bash
# Windows/Linux | macOS
Shift+F6 | Shift+F6             # Rename
F6 | F6                         # Move
F5 | F5                         # Copy
Alt+Delete | Cmd+Delete         # Safe Delete
Ctrl+Alt+N | Cmd+Opt+N          # Inline
Ctrl+Alt+M | Cmd+Opt+M          # Extract Method
Ctrl+Alt+V | Cmd+Opt+V          # Extract Variable
Ctrl+Alt+F | Cmd+Opt+F          # Extract Field
Ctrl+Alt+C | Cmd+Opt+C          # Extract Constant
Ctrl+Alt+P | Cmd+Opt+P          # Extract Parameter
```

### Refactoring Dialogs

```bash
# Windows/Linux | macOS
Ctrl+Alt+Shift+T | Cmd+Opt+Shift+T  # Refactor This
Alt+Insert | Cmd+N              # Generate Code
Ctrl+O | Cmd+O                  # Override Methods
Ctrl+I | Cmd+I                  # Implement Methods
Ctrl+Alt+T | Cmd+Opt+T          # Surround With
Ctrl+Alt+Shift+J | Cmd+Opt+Shift+J  # Structural Search
```

## Code Generation

### Generate Code

```bash
# Windows/Linux | macOS
Alt+Insert | Cmd+N              # Generate Code
Ctrl+O | Cmd+O                  # Override Methods
Ctrl+I | Cmd+I                  # Implement Methods
Ctrl+Alt+T | Cmd+Opt+T          # Surround With
Ctrl+J | Cmd+J                  # Live Templates
Ctrl+Alt+J | Cmd+Opt+J          # Surround with Live Template
Ctrl+Shift+Enter | Cmd+Shift+Enter  # Complete Statement
```

### Live Templates

```bash
# Java Templates:
sout                             # System.out.println()
soutm                            # System.out.println("method")
soutp                            # System.out.println(parameters)
soutv                            # System.out.println("var = " + var)
fori                             # for loop
iter                             # for each loop
itar                             # for loop with index
ifn                              # if null
inn                              # if not null
```

### Completion

```bash
# Windows/Linux | macOS
Ctrl+Space | Ctrl+Space         # Basic Completion
Ctrl+Shift+Space | Cmd+Shift+Space  # Smart Completion
Ctrl+Shift+Enter | Cmd+Shift+Enter  # Complete Statement
Ctrl+Alt+Space | Cmd+Opt+Space  # Class Name Completion
Ctrl+Shift+Alt+T | Cmd+Shift+Opt+T  # Type Matching
Tab | Tab                       # Next Template Parameter
Shift+Tab | Shift+Tab           # Previous Template Parameter
```

## Debugging

### Debugging Shortcuts

```bash
# Windows/Linux | macOS
Shift+F9 | Ctrl+D               # Debug
F9 | Cmd+Alt+R                  # Run
Shift+F10 | Ctrl+R              # Run without Debugging
F8 | F8                         # Step Over
F7 | F7                         # Step Into
Shift+F8 | Shift+F7             # Step Out
Alt+F9 | Opt+F9                 # Run to Cursor
Alt+F8 | Opt+F8                 # Evaluate Expression
Ctrl+F8 | Cmd+F8                # Toggle Breakpoint
Ctrl+Shift+F8 | Cmd+Shift+F8    # View Breakpoints
```

### Debugging Actions

```bash
# Windows/Linux | macOS
F9 | Cmd+Alt+R                  # Resume Program
Ctrl+F2 | Cmd+F2                # Stop
Ctrl+F5 | Ctrl+Cmd+R            # Rerun
Alt+F10 | Opt+F10               # Show Execution Point
Ctrl+Alt+F10 | Cmd+Opt+F10      # Show Current Location
Alt+F5 | Opt+F5                 # Attach to Process
```

## Version Control

### Git Integration

```bash
# Windows/Linux | macOS
Ctrl+K | Cmd+K                  # Commit
Ctrl+Alt+K | Cmd+Opt+K          # Push
Ctrl+T | Cmd+T                  # Update Project
Alt+Shift+C | Opt+Shift+C       # Recent Changes
Ctrl+Alt+Z | Cmd+Opt+Z          # Revert
Ctrl+Shift+K | Cmd+Shift+K      # Push Dialog
Alt+` | Ctrl+V                  # VCS Operations Popup
```

### Git Actions

```bash
# In Commit Window:
Ctrl+K | Cmd+K                  # Commit
Ctrl+Alt+K | Cmd+Opt+K          # Commit and Push
Ctrl+Shift+K | Cmd+Shift+K      # Push

# In VCS Popup (Alt+`):
1-9                             # Quick operations
C                               # Commit
U                               # Update Project
P                               # Push
B                               # Branches
M                               # Merge
R                               # Rebase
```

## Build & Run

### Build & Run Configurations

```bash
# Windows/Linux | macOS
Shift+F10 | Ctrl+R              # Run
Shift+F9 | Ctrl+D               # Debug
Ctrl+Shift+F10 | Ctrl+Shift+R   # Run Context Configuration
Ctrl+Shift+F9 | Ctrl+Shift+D    # Debug Context Configuration
Alt+Shift+F10 | Opt+Shift+R     # Run Configuration
Alt+Shift+F9 | Opt+Shift+D      # Debug Configuration
Ctrl+F2 | Cmd+F2                # Stop
Ctrl+F5 | Ctrl+Cmd+R            # Rerun
```

### Build Operations

```bash
# Windows/Linux | macOS
Ctrl+F9 | Cmd+F9                # Build Project
Ctrl+Shift+F9 | Cmd+Shift+F9    # Compile Selected
Ctrl+Alt+Shift+F9 | Cmd+Opt+Shift+F9  # Rebuild
Ctrl+Alt+F9 | Cmd+Opt+F9        # Make Module
Shift+Alt+F9 | Opt+Shift+F9     # Run Configuration
Shift+Alt+F10 | Opt+Shift+F10   # Run Configuration
```

## Testing

### Test Shortcuts

```bash
# Windows/Linux | macOS
Ctrl+Shift+T | Cmd+Shift+T      # Create/Run Test
Ctrl+Shift+F10 | Ctrl+Shift+R   # Run Test
Ctrl+Shift+F9 | Ctrl+Shift+D    # Debug Test
Ctrl+Alt+F10 | Cmd+Opt+F10      # Run Context Configuration
Ctrl+Alt+F9 | Cmd+Opt+F9        # Debug Context Configuration
Shift+Alt+F10 | Opt+Shift+F10   # Run Configuration
Shift+Alt+F9 | Opt+Shift+F9     # Debug Configuration
```

### Test Navigation

```bash
# Windows/Linux | macOS
Ctrl+Shift+T | Cmd+Shift+T      # Go to Test
Ctrl+Shift+F10 | Ctrl+Shift+R   # Run Test in Context
Ctrl+Shift+F9 | Ctrl+Shift+D    # Debug Test in Context
Alt+Home | Opt+Home             # Navigation Bar to Test
Ctrl+E | Cmd+E                  # Recent Test Files
```

## Database Tools

### Database Operations

```bash
# Windows/Linux | macOS
Ctrl+Shift+F10 | Ctrl+Shift+R   # Run Query
Ctrl+Enter | Cmd+Enter          # Execute Query
Ctrl+Alt+Enter | Cmd+Opt+Enter  # Execute to File
Ctrl+Alt+Shift+Enter | Cmd+Opt+Shift+Enter  # Explain Plan
F4 | F4                         # View Data
Ctrl+F12 | Cmd+F12              # Jump to Table
Ctrl+Alt+B | Cmd+Opt+B          # Go to Object
```

### Database Navigation

```bash
# Windows/Linux | macOS
Ctrl+Alt+Shift+U | Cmd+Opt+Shift+U  # Database Diagram
Alt+Home | Opt+Home             # Navigation Bar to DB
Ctrl+Shift+E | Cmd+Shift+E      # Recent Database Locations
Ctrl+B | Cmd+B                  # Go to Table/View
Alt+Insert | Cmd+N              # New Database Object
```

## Plugins & Customization

### Plugin Management

```bash
# Windows/Linux | macOS
Ctrl+Alt+S | Cmd+,              # Settings
Plugins                         # Plugin Section
Marketplace                     # Browse Plugins
Installed                       # Manage Installed
Ctrl+Shift+A | Cmd+Shift+A      # Find Plugin Action
```

### Essential Plugins

```bash
# Java Development:
- Lombok
- MapStruct Support
- Spring Assistant
- Maven Helper
- Gradle View

# Web Development:
- Vue.js
- AngularJS
- React Buddy
- Thymeleaf

# Database:
- Database Navigator
- JPA Buddy

# Productivity:
- Key Promoter X
- Rainbow Brackets
- String Manipulation
- GitToolBox
```

## Project Structure

### Project Navigation

```bash
# Windows/Linux | macOS
Alt+1 | Cmd+1                   # Project View
Alt+2 | Cmd+2                   # Bookmarks
Alt+3 | Cmd+3                   # Find
Alt+4 | Cmd+4                   # Run
Alt+5 | Cmd+5                   # Debug
Alt+6 | Cmd+6                   # Problems
Alt+7 | Cmd+7                   # Structure
Alt+8 | Cmd+8                   # Services
Alt+9 | Cmd+9                   # Version Control
Alt+0 | Cmd+0                   # Messages
```

### Window Management

```bash
# Windows/Linux | macOS
Shift+Esc | Shift+Esc           # Hide Active Window
Ctrl+Shift+F12 | Cmd+Shift+F12  # Maximize Editor
Ctrl+Alt+Shift+Left/Right | Cmd+Opt+Shift+Left/Right  # Resize Tool Window
F12 | F12                       # Jump to Last Tool Window
Ctrl+` | Cmd+`                  # Quick Switch Scheme
```

## Performance & Troubleshooting

### Performance Optimization

```bash
# Windows/Linux | macOS
Ctrl+Alt+Shift+/ | Cmd+Opt+Shift+/  # Maintenance
Invalidate Caches and Restart   # Clear Caches
Help > Change Memory Settings   # Increase Heap Size
File > Invalidate Caches        # Clear Indexes
```

### Troubleshooting

```bash
# Windows/Linux | macOS
Ctrl+Alt+Shift+S | Cmd+;        # Project Structure
Ctrl+Alt+S | Cmd+,              # Settings
Help > Diagnostic Tools         # Performance Monitoring
View > Tool Windows > Problems  # Code Issues
Ctrl+Alt+Shift+A | Cmd+Opt+Shift+A  # Find Action by Name
```

### Useful Settings

```bash
# Key Settings Locations:
Editor > General > Auto Import   # Optimize imports
Editor > Code Style              # Formatting rules
Build > Compiler                 # Build configuration
Version Control > Git            # Git settings
Tools > Terminal                 # Shell configuration
Appearance & Behavior > Menus and Toolbars  # UI customization
```

---

## Quick Reference Card

### Most Used Shortcuts

```bash
# Windows/Linux | macOS
Ctrl+Shift+A | Cmd+Shift+A      # Find Action
Double Shift | Double Shift     # Search Everywhere
Ctrl+B | Cmd+B                  # Go to Declaration
Alt+Enter | Opt+Enter           # Show Context Actions
Ctrl+Alt+L | Cmd+Opt+L          # Reformat Code
Ctrl+D | Cmd+D                  # Duplicate Line
Ctrl+Y | Cmd+Delete             # Delete Line
Shift+F6 | Shift+F6             # Rename
Ctrl+Alt+M | Cmd+Opt+M          # Extract Method
Ctrl+Shift+T | Cmd+Shift+T      # Create Test
```

### Essential Plugins

- **Key Promoter X** - Learn shortcuts
- **Lombok** - Java boilerplate reduction
- **Maven Helper** - Dependency management
- **Database Navigator** - Database tools
- **GitToolBox** - Enhanced Git features
- **String Manipulation** - Text operations

### Pro Tips

1. Use **Search Everywhere (Double Shift)** for quick navigation
2. **Alt+Enter** is your best friend for quick fixes
3. Use **Structural Search and Replace** for complex refactorings
4. Configure **Live Templates** for repetitive code patterns
5. Use **Multiple Carets** for batch editing
6. Master **Refactor This (Ctrl+Alt+Shift+T)** for code improvements
7. Use **Database Tools** integrated in Ultimate edition
8. Configure **Run Configurations** for complex applications

### Java-Specific Shortcuts

```bash
# Windows/Linux | macOS
Ctrl+O | Cmd+O                  # Override Methods
Ctrl+I | Cmd+I                  # Implement Methods
Ctrl+Alt+V | Cmd+Opt+V          # Extract Variable
Ctrl+Alt+F | Cmd+Opt+F          # Extract Field
Ctrl+Alt+C | Cmd+Opt+C          # Extract Constant
Ctrl+Alt+P | Cmd+Opt+P          # Extract Parameter
Ctrl+Shift+Space | Cmd+Shift+Space  # Smart Completion
```

---

_Remember: IntelliJ IDEA is highly customizable. Take time to learn the shortcuts that match your workflow and explore the powerful refactoring tools that make Java development more efficient!_
