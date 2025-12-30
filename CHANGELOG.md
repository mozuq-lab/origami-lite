# Changelog

All notable changes to Origami Lite will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-12-14

### Added

- Initial release of Origami Lite Plugin
- **4 Commands**:
  - `/origami:extract-features` - Extract features from specifications
  - `/origami:behavior-checklist` - Generate Must/Never behavior specifications
  - `/origami:boundary-analysis` - Analyze boundary values
  - `/origami:generate-cases` - Generate test cases in Given/When/Then format
- **Signal System** (Traffic Light):
  - 🟢 Green: High confidence (explicitly stated in specification)
  - 🟡 Yellow: Medium confidence (derived from industry standards/best practices)
  - 🔴 Red: Requires confirmation (speculation)
- **Flexible Input Support**:
  - Markdown files (.md)
  - Text files (.txt)
  - URLs
- **Workflow Support**:
  - Recommended workflow (Phase 1 → Phase 1.5 → Phase 2 → Phase 3)
  - Standalone execution for each command
- **Output Formats**:
  - Feature list with signal indicators
  - Must/Never behavior checklist
  - Boundary value analysis table
  - Test cases in Given/When/Then format with priority levels

### Documentation

- README.md with installation and usage instructions
- MIT License
- Command specifications with detailed templates

## [Unreleased]

### Planned

- URL fetch support improvements
- Additional test case formats
- Integration with test management tools
- Command naming improvements (see docs/design/command-naming-proposal.md)

## [1.2.0] - 2025-12-31

### Added

- **Output Directory Isolation**: Each specification file now outputs to its own directory
  - Output path: `docs/origami/{spec-name}/`
  - Prevents output mixing when processing multiple specifications
  - Easier progress tracking per specification

- **Directory Overwrite Confirmation**: `plan-tasks` now shows confirmation prompt when output directory exists
  - Options: "Overwrite" or "Cancel"
  - Uses AskUserQuestion for user interaction

- **New Command Arguments**:
  - `--output`: Specify output directory for all 4 base commands
  - `--target`: Specify target function ID (F-XXX) when called via `run-task`

### Changed

- `plan-tasks`: Records output directory in task-list.md (`出力先:` field)
- `run-task`: Dynamically reads output directory from task-list.md
- `verify-tasks`: Now requires specification name or directory path as argument
  - Example: `/origami:verify-tasks ecommerce-spec`
  - Example: `/origami:verify-tasks docs/origami/ecommerce-spec/`

### Documentation

- Updated CLAUDE.md with output isolation details
- Updated README.md with new directory structure

## [1.1.0] - 2025-12-31

### Added

- **Task Splitting Commands**: New commands to handle large specifications by splitting into smaller tasks
  - `/origami:plan-tasks` - Extract features and generate task list
  - `/origami:run-task` - Execute specified task (calls 4 base commands sequentially)
  - `/origami:verify-tasks` - Display task completion status and progress report

- **Task-based Workflow**:
  - Prevents context bloat when processing large specifications
  - Incremental output: each task appends to output files
  - Progress tracking with checkboxes in task-list.md

### Documentation

- Added task splitting workflow to README.md
- Added task splitting commands to CLAUDE.md
