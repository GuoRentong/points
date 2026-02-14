# Points Language Changelog

## 2026-02-14

### Added
- **Action Rule 5**: explicit separation of language issues from project-specific spec issues. Language issues/changelog track Points language evolution only; spec consistency issues are reported directly to the user. (Issue 10)

### Changed
- **Verify action steps 7-8**: split "Report any issues found" into two steps — step 7 for spec issues (report to user), step 8 for language observations (record to issues file). (Issue 10)
- **config.md**: relabeled "Issues Directory" → "Language Issues Directory" and "Changelog" → "Language Changelog" with scope clarification. (Issue 10)

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
- **Format Rule 10**: spec text formatting — markdown headings, bold component IDs, 3-space indentation. (Issue 5)
- **Example spec files**: Root.md, Database-1.Proxy.md, Dep-1.1.ProxyManager-2.1.SegmentWriter.md. (Issue 7)

### Changed
- **Format Rule 3**: indentation changed from 2-space to 3-space. (Issue 1)
- **Format Rule 4** (old): split into Rule 3 (indentation) and Rule 5 (IDs). (Issue 1)
- **ID scheme**: replaced 2-letter prefixes (BC, OR, PX) with full hierarchical names. (Issue 2)
- **Dependency structure**: replaced flat `deps.md` and `## Relationships` sections with formalized intra-file and inter-file dependency system. (Issue 3)
- **File structure**: changed from nested directory layout to flat layout with hierarchy in filenames. (Issue 4)
- **Root file**: renamed from `bundle-crawler.md` to `Root.md`. (Issue 4)
