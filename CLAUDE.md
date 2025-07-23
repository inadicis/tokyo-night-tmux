# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Tokyo Night Tmux is a clean, dark Tmux theme inspired by Tokyo at night and adapted from the Visual Studio Code theme. It provides customizable widgets for status bars including git status, network speed, music player, battery, hostname, and more.

## Development Commands

### Code Quality and Testing
- **Linting/Formatting**: `pre-commit run --all-files` - Runs shfmt for shell script formatting and codespell for spell checking
- **Testing**: `bats test/netspeed.bats` - Runs BATS tests for network speed functionality
- **Install pre-commit hooks**: `pre-commit install`

### Shell Script Formatting
- Uses `shfmt` with specific formatting rules: 2 spaces indentation, simplification enabled, language parsing, write changes in-place

## Architecture and Code Structure

### Core Components

**Main Entry Point**: `tokyo-night.tmux`
- Main plugin script that configures tmux status bars and themes
- Sources theme configurations and sets up status line with various widgets
- Handles tmux variable parsing and widget orchestration

**Theme System**: `src/themes.sh`
- Defines three theme variants: night (default), storm, and day
- Each theme has complete color palettes for foreground, background, and accent colors
- Supports transparency mode that overrides background to "default"
- Includes GitHub-specific colors for git status widgets

**Widget Architecture**: Individual scripts in `src/` directory
- Each widget is a standalone bash script that outputs tmux-formatted strings
- Widgets are called from main script using tmux's `#()` command substitution
- Key widgets: `git-status.sh`, `wb-git-status.sh`, `netspeed.sh`, `music-tmux-statusbar.sh`, `battery-widget.sh`, `hostname-widget.sh`, `datetime-widget.sh`, `path-widget.sh`

**Number Display System**: `src/custom-number.sh`
- Converts numeric IDs to various visual formats (digital, roman, square styles)
- Supports multiple number formats: hide, none, digital, fsquare, hsquare, dsquare, roman, super, sub
- Uses Unicode characters for enhanced visual representation

**Cross-platform Compatibility**: `lib/coreutils-compat.sh`
- Ensures GNU coreutils are available on macOS systems
- Adds Homebrew-installed GNU tools to PATH when available
- Required for consistent behavior across Linux and macOS

### Configuration System

The plugin uses tmux variables (set with `@tokyo-night-tmux_*`) to configure:
- Theme selection (`@tokyo-night-tmux_theme`)
- Widget visibility (`@tokyo-night-tmux_show_*`)
- Display preferences (`@tokyo-night-tmux_*_style`)
- Network interface selection (`@tokyo-night-tmux_netspeed_iface`)

### Git Integration

Two-tier git status system:
- **Local Git Status** (`git-status.sh`): Shows local repository status
- **Web-based Git Status** (`wb-git-status.sh`): Shows GitHub/GitLab PR and issue counts using gh/glab CLI tools

### Testing Infrastructure

Uses BATS (Bash Automated Testing System):
- Test files in `test/` directory
- Mock system for testing platform-specific functionality
- Covers core utility functions like network interface detection and data formatting

## Development Guidelines

### Contributing Process
- All PRs must target the `next` branch (not `master`)
- Pre-commit checks must pass before committing
- Follow Conventional Commits specification for commit messages
- Open an issue before starting work on new features

### Code Conventions
- Shell scripts use `#!/usr/bin/env bash` shebang
- 2-space indentation enforced by shfmt
- Variables use uppercase for constants, lowercase for local variables
- Extensive use of tmux color variables for theming consistency

### Dependencies
- Bash 4.2+ required (macOS users need to install newer bash via Homebrew)
- Optional dependencies for full functionality: bc, jq, gh/glab, playerctl/nowplaying-cli
- Nerd Fonts v3+ required for icon display