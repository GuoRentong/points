# Points Language Changelog

## 2026-02-20

### Changed
- **Spec directory**: renamed conventional spec directory from `points/` to `spec/` in config.md template, SKILL.md description and file structure, workflow.md Phase 1 scope. (Issue 15)

## 2026-02-16

### Changed
- **Format Rules → Spec Rules**: renamed section to reflect that most rules define spec structure, not formatting. (Issue 14)
- **Action Rules → workflow.md**: merged Action Rules into workflow.md; rules 1-2 already covered by workflow; rules 3-5 (language evolution tracking) added as "When the Points language itself changes" section; SKILL.md now references workflow.md instead. (Issue 14)
- **Spec Rule 4**: parent points that expand into child files must not reference implementation details; full evolving detail lives exclusively in the child file; points without child files carry full detail as normal. (Issue 13)

## 2026-02-15

### Added
- **Verify action `--local` mode**: scoped verification using Code Mapping to check only changed spec points against their source files and parent/child consistency, instead of full-spec verification. (Issue 11)

### Changed
- **Workflow gate**: split into Phase 1 (spec-only edits) and Phase 2 (implementation after confirmation) with a HARD STOP requiring AskUserQuestion between them; Phase 1 restricts edits to `points/` files only; expanded rationalization deny list. (Issue 12)

## 2026-02-14

### Added
- **Action Rule 5**: explicit separation of language issues from project-specific spec issues. Language issues/changelog track Points language evolution only; spec consistency issues are reported directly to the user. (Issue 10)
- **Format Rule 10**: bidirectional code traceability — spec-side `## Code Mapping` section per topic spec file (component → file paths + grep keywords) and code-side `# Points Spec References:` comment header per source file (`[FullID, ...] reason`). (Issue 1)
- **Format Rule 11**: proposals separation — spec files contain only implemented ground truth; aspirational designs live in `proposals/<TopicID>.md` files under the spec directory. (Issue 2)

### Changed
- **Format Rule 8**: clarified "independent" for multi-party dependency grouping — two pairs are independent if removing either doesn't affect the other; shared mechanism → group, coincidental common party → split. (Issue 3)
- **Verify action steps 7-8**: split "Report any issues found" into two steps — step 7 for spec issues (report to user), step 8 for language observations (record to issues file). (Issue 10)
- **config.md**: relabeled "Issues Directory" → "Language Issues Directory" and "Changelog" → "Language Changelog" with scope clarification. (Issue 10)
- **Format Rule 10 → 12**: old "Spec text format" rule renumbered to 12 (was 11, then 12 after proposals rule inserted). (Issues 1, 2)
- **Actions updated**: `add` (step 9), `refine` (step 8), `evolve` (steps 2-3, 7), `sync` (steps 2-3, 5), `verify` (step 7) now reference code traceability (Format Rule 10). (Issue 1)

## 2026-02-13

### Added
- **Action Rules 1–4**: spec-first workflow, backward-tracing, issue logging, changelog maintenance. (Issue 6)
- **Example section**: running "database" example with proxy/WAL/storage components.
- **Format Rule 2**: two types of spec files (topic specs and inter-file dependency specs).
- **Format Rule 5**: hierarchical ID scheme `<ProjectName>-<TopicNumber>.<TopicName>-<ComponentNumber>.<ComponentName>` with shortened prefixes within files. (Issue 2)
- **Format Rule 6**: cross-references require full IDs, distinguished from dependencies. (Issue 3)
- **Format Rule 7**: intra-file dependencies with two-level structure and globally unique dependency IDs. (Issue 3)
- **Format Rule 8**: inter-file dependencies in separate `Dep-` prefixed spec files. (Issue 3)
- **Format Rule 9**: spec file naming — `Root.md`, topic full ID filenames, `Dep-` prefix for dependency files. (Issue 4)
- **Format Rule 11** (originally 10): spec text formatting — markdown headings, bold component IDs, 3-space indentation. (Issue 5)
- **Example spec files**: Root.md, Database-1.Proxy.md, Dep-1.1.ProxyManager-2.1.SegmentWriter.md. (Issue 7)

### Changed
- **Format Rule 3**: indentation changed from 2-space to 3-space. (Issue 1)
- **Format Rule 4** (old): split into Rule 3 (indentation) and Rule 5 (IDs). (Issue 1)
- **ID scheme**: replaced 2-letter prefixes (BC, OR, PX) with full hierarchical names. (Issue 2)
- **Dependency structure**: replaced flat `deps.md` and `## Relationships` sections with formalized intra-file and inter-file dependency system. (Issue 3)
- **File structure**: changed from nested directory layout to flat layout with hierarchy in filenames. (Issue 4)
- **Root file**: renamed from `bundle-crawler.md` to `Root.md`. (Issue 4)
