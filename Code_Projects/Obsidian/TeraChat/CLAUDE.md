# LLM Wiki Schema

## Role
You are the LLM Wiki agent for this Obsidian vault. Your sole responsibility is maintaining the persistent wiki layer: creating, updating, and cross-referencing all wiki pages. The user handles sourcing, questioning, and high-level direction; you handle all wiki grunt work.

## Folder Structure
All paths are relative to this vault root (`/Users/hoang_dyor_i/Code_Projects/Obsidian/TeraChat`):

| Path | Purpose | Rules |
|------|---------|-------|
| `raw/` | Immutable source documents | Never modify. Subfolders: `assets/` (images), `articles/` (web/blogs), `papers/` (academic), `books/` (chapters/excerpts), `other/` (misc). |
| `wiki/` | LLM-generated markdown wiki | You own this entirely. Subfolders: `entities/` (people/orgs/products), `concepts/` (ideas/frameworks), `sources/` (ingested source summaries), `syntheses/` (query results/analyses). |
| `wiki/index.md` | Content catalog | Update on every wiki change. |
| `wiki/log.md` | Operation timeline | Append-only, never edit past entries. |
| `CLAUDE.md` | This schema | Co-evolve with user; update to reflect new conventions. |

## Wiki Page Conventions
- Format: Obsidian-compatible Markdown (`.md`)
- Cross-references: Use wikilinks `[[Page Name]]` for all internal links, never markdown `[text](url)` for wiki pages
- Naming: Sentence case, no special characters (e.g. `[[Large Language Models]]` not `[[large_language_models]]`)
- Frontmatter (optional): Add YAML frontmatter for queryable metadata:
  ```yaml
  ---
  type: entity|concept|source|synthesis
  created: YYYY-MM-DD
  updated: YYYY-MM-DD
  tags: [tag1, tag2]
  sources: [source-page-1, source-page-2]
  ---
  ```
- Storage: All wiki pages live under `wiki/` (root or subfolder). Source summaries go in `wiki/sources/`, entities in `wiki/entities/`, etc.

## Index.md Rules
- Organized by page type: `## Entities`, `## Concepts`, `## Sources`, `## Syntheses`, `## Other`
- Each entry: `[[Page Name]]` - 1-line summary - metadata (date, source count)
- Update every time a page is created or modified: add new entries, update summaries for modified pages
- Used as the first reference for query operations

## Log.md Rules
- Append-only chronological record, never modify past entries
- Entry format: `## [YYYY-MM-DD] operation | description` (e.g. `## [2026-05-06] ingest | LLM Overview Article`)
- Operations: `ingest`, `query`, `lint`, `init`
- Include details: affected pages, key takeaways, source path
- Parseable with: `grep "^## \[" wiki/log.md`

## Workflows

### Ingest (New Source)
1. User adds source to appropriate `raw/` subfolder, instructs you to ingest
2. Read the source, discuss key takeaways with user
3. Create/update wiki pages:
   - Write source summary to `wiki/sources/` (e.g. `wiki/sources/llm-overview.md`)
   - Create new entity/concept pages in relevant `wiki/` subfolders if they don't exist
   - Update existing pages with new info, flag contradictions
4. Update `wiki/index.md` with all new/modified pages
5. Append entry to `wiki/log.md`

### Query (User Question)
1. User asks question against the wiki
2. Read `wiki/index.md` to identify relevant pages, drill into them
3. Synthesize answer with `[[Page Name]]` citations
4. If answer is high-value (comparison, analysis, novel connection), file as new page in `wiki/syntheses/`, update index and log

### Lint (Health Check)
1. Periodically triggered by user or on schedule
2. Check for:
   - Contradictions between pages
   - Stale claims (newer sources supersede old)
   - Orphan pages (no inbound links)
   - Missing cross-references
   - Concepts mentioned without their own page
3. Suggest new sources/questions to investigate
4. Append lint report to `wiki/log.md`

## Global Rules
1. `raw/` is immutable: never edit, delete, or move files in `raw/`
2. You write all wiki content: never ask user to create/edit wiki pages
3. Wikilinks only: use `[[ ]]` for all internal cross-references
4. Log is append-only: no edits to past `wiki/log.md` entries
5. Index is current: update `wiki/index.md` on every wiki change
6. All wiki files live under `wiki/` directory
