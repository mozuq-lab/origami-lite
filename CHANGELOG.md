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

## [3.2.0] - 2026-02-23

### Added

- **New Command: `/origami:review-viewpoints`** (Phase 5)
  - Reviews generated test cases against a viewpoint catalog for gap analysis
  - Compares test cases with 203 blackbox test viewpoints from TIS catalog
  - Signal system adapted for review: 🟢 covered, 🟡 uncovered but recommended, 🔴 needs confirmation
  - Generates supplementary test case proposals (STC-XXX-YY format) for uncovered viewpoints
  - Supports both standalone and task-split execution modes
  - `--app-type` option for filtering viewpoints by application type (web/mobile/api)

- **New Data File: `data/viewpoint-catalog.md`**
  - 203 blackbox test viewpoints extracted from TIS Test Viewpoint Catalog v1.6
  - 5 sections: VAL (35), WEB (96), SEC-C (27), SEC-W (25), USA (20)
  - Licensed under CC BY-SA 4.0 (Creative Commons Attribution-ShareAlike 4.0 International)

- **Phase 5 Support in `run-task`**
  - `--phase 5` executes review-viewpoints for the target feature
  - Phase range extended from 1-4 to 1-5
  - Phase 5 prerequisite check: `04_テストケース.md` + `01_機能詳細.md` must exist
  - Phase 5 is optional: omitting `--phase` still executes Phase 1-4 only (backward compatible)

### Changed

- **`verify-tasks`**: Phase 5 column added to progress table (shown as `—` when not executed)
- **`split-spec`**: Task template includes Phase 5 row marked as `(任意)`
- **`run-task`**: Phase mapping table, prerequisite table, and context separation table updated for Phase 5

### Backward Compatibility

- `--phase` omission behavior unchanged (Phase 1-4 only)
- Progress calculation remains 4-phase based (Phase 5 is optional indicator)
- Existing Phase 1-4 workflows are unaffected

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
