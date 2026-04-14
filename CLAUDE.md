# LLM Wiki

A personal knowledge base maintained by Claude Code.
Based on Andrej Karpathy's LLM Wiki pattern.

## Purpose

This wiki is a structured, interlinked knowledge base for planning a trip to Japan.
Claude maintains the wiki. The human curates sources, asks questions, and guides the analysis.

## Folder structure

```
raw/            -- source documents (immutable -- never modify these)
raw/assets/     -- locally downloaded images from source documents
wiki/           -- markdown pages maintained by Claude
wiki/index.md   -- table of contents for the entire wiki
wiki/log.md     -- append-only record of all operations
wiki/sources.md -- registry of all ingested source files
skills/         -- reusable workflow definitions (markdown files)
templates/      -- page templates for consistent structure
```

## Templates

Templates live in `templates/` and define the skeleton for different page types. When creating a new wiki page, pick the closest matching template and fill it in.

Available templates:

- `templates/page.md` — default wiki page template (includes YAML frontmatter)
- Add new templates as needed for specialized page types (e.g. itinerary-day, comparison-table)

## Page format

Every wiki page must include YAML frontmatter followed by the page body. The frontmatter enables Obsidian's Dataview plugin to query and filter pages.

```markdown
---
title: Page Title
tags: [japan, tokyo, food]
status: researching          # one of: researching, confirmed, booked, skipped
category: concept            # one of: concept, entity, source-summary, comparison, timeline, itinerary
sources: [source-file-1.pdf, source-file-2.md]
created: 2026-04-14
updated: 2026-04-14
---

# Page Title

**Summary**: One to two sentences describing this page.

---

Main content goes here. Use clear headings and short paragraphs.

Link to related concepts using [[wiki-links]] throughout the text.

## Related pages

- [[related-concept-1]]
- [[related-concept-2]]
```

### Status tags for trip planning

Use the `status` field in frontmatter to track where each item stands:

| Status        | Meaning                                      |
|---------------|----------------------------------------------|
| `researching` | Still gathering info, nothing decided yet     |
| `confirmed`   | Decision made, details locked in              |
| `booked`      | Reservation or ticket purchased               |
| `skipped`     | Considered but decided against                |

## Ingest workflow

When the user adds a new source to `raw/` and asks you to ingest it:

1. Read the full source document
2. Discuss key takeaways with the user before writing anything
3. Create a summary page in `wiki/` named after the source
4. Create or update concept pages for each major idea or entity
5. Add wiki-links ([[page-name]]) to connect related pages
6. Update `wiki/index.md` with new pages and one-line descriptions
7. Update `wiki/sources.md` with the new source file entry
8. Append an entry to `wiki/log.md` following the log format below

A single source may touch 10-15 wiki pages. That is normal.

## Log format

Every entry in `wiki/log.md` must use this parseable format:

```markdown
## [YYYY-MM-DD] operation | Subject

Brief description of what changed.

Pages created: page-1.md, page-2.md
Pages updated: page-3.md, page-4.md
```

Where `operation` is one of: `ingest`, `query`, `lint`, `skill`, `update`, `delete`.

This format is grep-friendly: `grep "^## \[" wiki/log.md | tail -5` gives the last 5 entries.

## Citation rules

- Every factual claim should reference its source file
- Use the format (source: filename.pdf) after the claim
- If two sources disagree, note the contradiction explicitly
- If a claim has no source, mark it as needing verification

## Conflict resolution

When two or more sources contradict each other:

1. **Note both claims** on the relevant wiki page with their respective sources
2. **Flag the contradiction** visibly using a blockquote:
   ```markdown
   > **Contradiction**: Source A says X, but Source B says Y.
   > Pending resolution — ask user or find a third source.
   ```
3. **Ask the user** which source they trust more, or whether to seek a third source
4. **Never silently pick one** — the user decides how to resolve conflicts
5. Once resolved, keep a brief note of what was overridden and why

## Question answering

When the user asks a question:

1. Read `wiki/index.md` first to find relevant pages
2. Read those pages and synthesize an answer
3. Cite specific wiki pages in your response
4. If the answer is not in the wiki, say so clearly
5. If the answer is valuable, offer to save it as a new wiki page

Good answers should be filed back into the wiki so they compound over time.

## Output formats

Not every answer needs to be a plain markdown page. Choose the format that best serves the content:

| Format            | When to use                                  | How                              |
|-------------------|----------------------------------------------|----------------------------------|
| Markdown page     | Default for concepts, entities, summaries    | Standard wiki page               |
| Comparison table  | Comparing hotels, restaurants, transit options| Markdown table in a wiki page    |
| Timeline          | Itinerary planning, historical events        | Chronological list with dates    |
| Slide deck (Marp) | Presenting a summary or briefing             | Marp-formatted markdown          |
| Chart             | Budget breakdowns, distances, statistics     | Generated via matplotlib/mermaid |
| Checklist         | Packing lists, booking tasks, to-dos         | Markdown task list `- [ ]`       |

When producing non-page output, still offer to save it as a wiki page for future reference.

## Lint

When the user asks you to lint or audit the wiki:

- Check for contradictions between pages
- Find orphan pages (no inbound links from other pages)
- Identify concepts mentioned in pages that lack their own page
- Flag claims that may be outdated based on newer sources
- Check that all pages follow the page format above (including YAML frontmatter)
- Verify all `status` fields are valid values
- Check that `wiki/sources.md` is in sync with actual raw files
- **Search the web** to fill data gaps (e.g. missing addresses, hours, prices, or outdated info) — confirm with the user before writing web-sourced data
- Report findings as a numbered list with suggested fixes

## Image handling

When source documents contain images:

1. Download images to `raw/assets/` using a descriptive filename (e.g. `tokyo-station-exterior.jpg`)
2. Reference images in wiki pages using relative paths: `![description](../raw/assets/filename.jpg)`
3. When ingesting a source with images, read the text first, then view referenced images separately for additional context
4. Note in the wiki page when an image provides information not captured in the text

## Search and tooling

At small scale (~50 pages), `wiki/index.md` is sufficient for finding relevant pages. As the wiki grows:

- Consider adding [qmd](https://github.com/tobi/qmd) for hybrid BM25/vector search over wiki pages (available as CLI and MCP server)
- Alternatively, build a simple search script — Claude can help vibe-code one when the need arises
- The index file should always be maintained regardless of other search tooling

## Skills

Skills are reusable workflows stored as markdown files in `skills/`. They let the wiki grow beyond basic ingest/query/lint by capturing specialized procedures that can be invoked by name.

### Skill file format

Each skill is a markdown file in `skills/` following this structure:

```markdown
# Skill Name

**Purpose**: What this skill does in one sentence.

**Trigger**: When to use this skill (e.g. "user says /compare", "user drops a PDF itinerary").

---

## Steps

1. Step-by-step instructions Claude should follow
2. Each step should be concrete and actionable
3. Reference other skills with [[skill-name]] if this skill builds on them

## Output

Describe what the skill produces (wiki pages, tables, reports, etc.)

## Example

A short example of invoking this skill and its expected result.
```

### Managing skills

- **Create**: When a workflow is repeated more than once or is complex enough to warrant documentation, save it as a skill. Name files lowercase with hyphens (e.g. `compare-sources.md`).
- **List**: To see available skills, read the contents of `skills/`.
- **Invoke**: The user can trigger a skill by name (e.g. "run the compare skill") or Claude may suggest a skill when it fits the user's request.
- **Update**: Skills evolve. When a workflow improves through use, update the skill file to reflect the better process.
- **Delete**: Remove skills that are no longer useful.

### Built-in skills to create on first use

When the user first requests one of these common operations and no corresponding skill file exists yet, create the skill file before executing it:

- **ingest** — the ingest workflow already described above, codified as a skill
- **query** — the question-answering workflow above, codified as a skill
- **lint** — the wiki health-check workflow above, codified as a skill
- **compare** — compare two or more sources or wiki pages side by side, produce a comparison table
- **timeline** — extract dates from sources and build a chronological timeline page
- **summary** — generate a briefing page that synthesizes the current state of the wiki on a topic

### Custom skills

The user can ask Claude to create new skills at any time. Examples:

- "Create a skill that converts wiki pages into a packing list"
- "Make a skill that finds all restaurant recommendations across sources"
- "Build a skill that generates a day-by-day itinerary from the wiki"

When creating a custom skill, follow the skill file format above and confirm the skill with the user before saving.

## Rules

- Never modify anything in the `raw/` folder (except downloading images to `raw/assets/`)
- Always update `wiki/index.md`, `wiki/sources.md`, and `wiki/log.md` after changes
- Keep page names lowercase with hyphens (e.g. `tokyo-station.md`)
- Always include YAML frontmatter on every wiki page
- Write in clear, plain language
- When uncertain about how to categorize something, ask the user
- When web-sourced data is added, mark it with (source: web, YYYY-MM-DD) and flag for verification
