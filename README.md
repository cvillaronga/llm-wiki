# LLM Wiki

A personal knowledge base maintained by AI, based on [Andrej Karpathy's LLM Wiki pattern](https://github.com/karpathy/llm-wiki).

Instead of re-deriving answers from raw documents on every question (like RAG), the LLM **incrementally builds and maintains a persistent wiki** — a structured, interlinked collection of markdown pages that compounds knowledge over time.

You never write the wiki yourself. The LLM does all the summarizing, cross-referencing, filing, and bookkeeping. You curate sources, ask questions, and guide the analysis.

## Getting started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (or another LLM agent that reads a schema file)
- [Obsidian](https://obsidian.md/) (recommended for browsing the wiki)

### Setup

```bash
git clone https://github.com/your-username/llm-wiki.git
cd llm-wiki
```

Open the folder in Claude Code. The `CLAUDE.md` file tells the agent how to operate.

### Folder structure

```
raw/            — source documents (immutable, never modified by the LLM)
raw/assets/     — locally downloaded images from sources
wiki/           — markdown pages maintained by the LLM
wiki/index.md   — table of contents
wiki/log.md     — append-only operation log
wiki/sources.md — registry of all ingested sources
skills/         — reusable workflow definitions
templates/      — page templates for consistent structure
```

## Usage

### Ingest a source

Drop a file into `raw/` and tell the agent:

> "Ingest raw/article-about-tokyo.md"

The agent will read it, discuss key takeaways with you, then create or update wiki pages, cross-references, the index, source registry, and log.

### Ask a question

> "What are the best neighborhoods to stay in Tokyo?"

The agent searches the wiki, synthesizes an answer with citations, and offers to save it as a new page.

### Lint the wiki

> "Lint the wiki"

The agent audits for contradictions, orphan pages, missing concepts, outdated claims, format issues, and data gaps it can fill via web search.

### Use skills

Skills are reusable workflows stored in `skills/`. Invoke them by name:

> "Run the compare skill on tokyo-hotels.md and osaka-hotels.md"

> "Create a skill that builds a packing list from the wiki"

Built-in skills (created on first use): `ingest`, `query`, `lint`, `compare`, `timeline`, `summary`.

### Output formats

The agent can produce different formats depending on the question:

- **Markdown pages** — concepts, entities, summaries
- **Comparison tables** — hotels, restaurants, transit options
- **Timelines** — itineraries, chronological events
- **Checklists** — packing lists, booking tasks
- **Slide decks** — Marp-formatted presentations
- **Charts** — budgets, distances, statistics

### Track progress with status tags

Every wiki page has a `status` field in its YAML frontmatter:

| Status | Meaning |
|---|---|
| `researching` | Still gathering info |
| `confirmed` | Decision made |
| `booked` | Reservation purchased |
| `skipped` | Decided against |

Use Obsidian's [Dataview](https://github.com/blacksmithgu/obsidian-dataview) plugin to query pages by status, tags, or category.

## Customization

The `CLAUDE.md` file is the schema that controls how the LLM operates. Edit it to:

- Change the wiki's purpose (trip planning, research, book notes, etc.)
- Add new page categories or status values
- Define new skills or modify existing workflows
- Adjust citation rules or output format preferences

You and the LLM co-evolve this schema over time.

## Why this works

LLMs eliminate the maintenance burden that kills personal wikis. They don't get bored, don't forget to update cross-references, and can touch 15 files in one pass. The human's job is to curate sources, direct the analysis, and think about what it all means. The LLM handles everything else.

## Author

**Carlos Villaronga**

## License

MIT License

Copyright (c) 2026 Carlos Villaronga

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
