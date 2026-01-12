# Changelog

All notable changes to Origami Lite will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Documentation

- Added custom rules documentation to README.md and CLAUDE.md
  - Rule loading paths: `docs/rule` and `docs/rule/origami`
  - Usage examples for project-specific rules

### Planned

- URL fetch support improvements
- Additional test case formats
- Integration with test management tools

## [3.1.0] - 2025-12-31

### Added

- **Phase-based Execution**: New `--phase` option for `run-task` command
  - `--phase 1`: Execute extract-features only
  - `--phase 2`: Execute generate-checklist only
  - `--phase 3`: Execute analyze-boundaries only
  - `--phase 4`: Execute generate-cases only
  - Omitting `--phase` executes all phases sequentially (backward compatible)

- **Phase Progress Tracking**: `verify-tasks` now displays phase-level progress
  - Progress calculation: `completed phases / (tasks × 4)`
  - Phase status indicators: ⏳ pending, 🔄 in progress, ✅ completed, ⚠️ needs review
  - Next action suggestions based on current progress

- **Inter-phase Input Restriction**: Each phase only reads the previous phase's output
  - Phase 1 (extract-features): Reads specification (target feature section only)
  - Phase 2 (generate-checklist): Reads `01_機能詳細.md` only
  - Phase 3 (analyze-boundaries): Reads `02_動作仕様.md` only
  - Phase 4 (generate-cases): Reads `01` + `02` + `03` (3 files)
  - Maintains consistent context size regardless of specification size

### Changed

- **task-list.md Format**: New format with phase progress table per task
  - Each task section includes phase status table
  - Status symbols: ⏳ (pending), 🔄 (in progress), ✅ (completed), ⚠️ (needs review)
  - Output file path recorded per phase

- **verify-tasks Output**: Phase-based progress report
  - Phase progress table showing all tasks × phases
  - Overall progress percentage based on completed phases
  - Next action recommendations

### Breaking Changes

- task-list.md format changed (incompatible with v3.0.x format)
- verify-tasks output format changed

### Documentation

- Added v3.1.0 Breaking Change sections to all command files
- Updated input restriction documentation for all base commands

## [3.0.0] - 2025-12-31

### Added

- **Feature-based Directory Output**: Task split execution now outputs to feature-specific directories
  - Output path: `{spec-name}/F-XXX_{feature-name}/`
  - Each feature gets its own directory with 4 files
  - Prevents context bloat by isolating feature data

- **Input File Restriction**: Task split commands now only read from their own feature directory
  - `generate-checklist`: reads `01_機能詳細.md` only
  - `analyze-boundaries`: reads `02_動作仕様.md` only
  - `generate-cases`: reads `01_機能詳細.md`, `02_動作仕様.md`, `03_境界値分析.md`
  - Prevents cross-feature context pollution

- **Conditional Output Filenames**: Output filename changes based on execution context
  - Standalone: `01_機能一覧.md`, `02_動作仕様一覧.md`, etc.
  - Task split: `01_機能詳細.md`, `02_動作仕様.md`, etc.

### Changed

- `split-spec`: Now extracts feature logical names for directory naming
- `run-task`: Creates `F-XXX_{feature-name}/` directory before executing commands
- `verify-tasks`: Now verifies feature directory structure
- All 4 base commands: Added `--target` option support for single-feature execution

### Documentation

- Updated CLAUDE.md with feature directory structure
- Updated README.md with new workflow examples

## [2.0.0] - 2025-12-31

### Breaking Changes

- **Command Directory Structure Changed**: `commands/origami/` → `commands/`
  - Command files moved to `commands/` root directory
  - Fixes double prefix issue (`/origami:origami:` → `/origami:`)

- **Command Naming Convention**: Commands renamed to follow "verb-noun" format
  - `/origami:behavior-checklist` → `/origami:generate-checklist`
  - `/origami:boundary-analysis` → `/origami:analyze-boundaries`
  - `/origami:plan-tasks` → `/origami:split-spec`

### Changed

- Command files renamed to match new naming convention:
  - `behavior-checklist.md` → `generate-checklist.md`
  - `boundary-analysis.md` → `analyze-boundaries.md`
  - `plan-tasks.md` → `split-spec.md`
- Command files moved from `commands/origami/` to `commands/`
- Internal references updated in all command files
- Documentation updated (CLAUDE.md, README.md)

## [1.2.0] - 2025-12-31

### Added

- **Output Directory Isolation**: Each specification file now outputs to its own directory
  - Output path: `docs/origami/{spec-name}/`
  - Prevents output mixing when processing multiple specifications
  - Easier progress tracking per specification

- **Directory Overwrite Confirmation**: `split-spec` now shows confirmation prompt when output directory exists
  - Options: "Overwrite" or "Cancel"
  - Uses AskUserQuestion for user interaction

- **New Command Arguments**:
  - `--output`: Specify output directory for all 4 base commands
  - `--target`: Specify target function ID (F-XXX) when called via `run-task`

### Changed

- `split-spec`: Records output directory in task-list.md (`出力先:` field)
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
  - `/origami:split-spec` - Extract features and generate task list
  - `/origami:run-task` - Execute specified task (calls 4 base commands sequentially)
  - `/origami:verify-tasks` - Display task completion status and progress report

- **Task-based Workflow**:
  - Prevents context bloat when processing large specifications
  - Incremental output: each task appends to output files
  - Progress tracking with checkboxes in task-list.md

### Documentation

- Added task splitting workflow to README.md
- Added task splitting commands to CLAUDE.md

## [1.0.0] - 2025-12-14

### Added

- Initial release of Origami Lite Plugin
- **4 Commands**:
  - `/origami:extract-features` - Extract features from specifications
  - `/origami:generate-checklist` - Generate Must/Never behavior specifications
  - `/origami:analyze-boundaries` - Analyze boundary values
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
