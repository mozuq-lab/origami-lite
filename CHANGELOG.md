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
