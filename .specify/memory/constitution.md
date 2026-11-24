<!--
  Sync Impact Report
  ==================
  Version change: (template) → 1.0.0
  Modified principles: N/A (initial creation - all 5 new)
  Added sections: Core Principles (5 principles), Technology Stack, Development Workflow, Governance
  Removed sections: N/A
  Templates requiring updates:
    ✅ .specify/templates/plan-template.md (Constitution Check section is already generic)
    ✅ .specify/templates/spec-template.md (no principle-specific references)
    ✅ .specify/templates/tasks-template.md (task categorization is generic)
    ✅ .specify/templates/checklist-template.md (no principle-specific references)
  Commands reviewed:
    ✅ .opencode/commands/speckit.constitution.md (no outdated agent-specific names)
    ⚠ .specify/templates/commands/ — does not exist; no updates needed
  Follow-up TODOs: None
-->

# StudyCards CLI Constitution

## Core Principles

### I. Simplicity First (NON-NEGOTIABLE)

- MUST start with the simplest solution that works (YAGNI)
- Each dependency MUST earn its place — justify every addition
- MUST reject speculative complexity, optional abstractions, and
  premature optimization
- A simpler solution is ALWAYS preferred over a clever one
- Rationale: Over-engineering is the primary risk for a small CLI tool;
  simplicity preserves maintainability and onboarding speed

### II. Markdown-Driven Data

- All flashcards MUST be stored as plain Markdown files
- File format MUST be human-readable and editable in any text editor
- MUST NOT use binary formats, proprietary databases, or lock-in storage
- Data directory MUST be git-friendly (plain text = clean diffs)
- Rationale: Markdown is the interchange format; keeps study data
  portable, transparent, and version-controllable

### III. Terminal-First UX

- CLI MUST be the primary (and only) interaction model
- Command structure MUST be consistent, predictable, and discoverable
  (e.g., `study-cards <command> [options]`)
- Meaningful output: color-coded feedback, progress indicators for
  sessions, errors go to stderr
- MUST support `--json` flag for machine-readable output alongside
  human-friendly default
- Rationale: Terminal UX must be deliberate and polished — not an
  afterthought — to make study sessions productive

### IV. Test-First (NON-NEGOTIABLE)

- Tests MUST be written before implementation code (Red-Green-Refactor)
- Tests MUST be runnable without network or external services
- Unit tests MUST cover business logic in isolation
- Integration tests MUST cover CLI command end-to-end
- Testing ease is a design goal: if something is hard to test,
  the design MUST be simplified
- Rationale: TDD ensures correctness and keeps the codebase
  refactorable; small CLI projects benefit enormously from test safety

### V. Modular Separation

- Business logic MUST be separated from CLI presentation layer
- Core library MUST be independently usable without the CLI wrapper
- Clear interfaces between modules: parser ↔ engine ↔ storage ↔ CLI
- Each module MUST have a single, well-defined responsibility
- Rationale: Separation enables testing business logic without CLI
  overhead, allows reuse, and keeps each file small and focused

## Technology Stack

- **Runtime**: Node.js 18+ (current LTS)
- **Language**: TypeScript with strict mode enabled
- **Testing**: Vitest (fast, TypeScript-native)
- **CLI parsing**: `commander` or `cac` (lightweight, well-maintained)
- **Markdown parsing**: `marked` or `mdast` ecosystem
- **Formatting**: Prettier
- **No ORM or database driver** — data is filesystem Markdown

## Development Workflow

- TDD cycle MUST be followed: write failing test → implement → refactor
- TypeScript strict mode MUST be enabled with no unchecked side effects
- ESLint + Prettier MUST be configured and pass before commits
- Tests MUST pass before any commit or PR
- Conventional commits format: `type(scope): message`
  (`feat`, `fix`, `chore`, `docs`, `test`, `refactor`)
- Complexity must be justified when deviating from Simplicity First
  principle — document rationale inline

## Governance

- This constitution supersedes all other project guidelines
- **Versioning**: Semantic Versioning (MAJOR.MINOR.PATCH)
  - MAJOR: Backward-incompatible principle changes or removals
  - MINOR: New principle/section or materially expanded guidance
  - PATCH: Clarifications, wording refinements, typo fixes
- **Amendments**: MUST be documented with rationale in a PR; must
  include a Sync Impact Report for all affected templates
- **Compliance**: All PRs and code reviews MUST verify constitution
  compliance; violations must be justified in Complexity Tracking
- **Review**: Constitution should be reviewed quarterly for relevance

**Version**: 1.0.0 | **Ratified**: 2026-06-14 | **Last Amended**: 2026-06-14
