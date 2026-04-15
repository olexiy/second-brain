---
name: init-second-brain
description: Initialize a personalized Obsidian-based Second Brain vault — folder structure, CLAUDE.md config, and automated knowledge workflows. Use when the user wants to set up a second brain, initialize an Obsidian vault for Claude Code, configure personal knowledge management, or mentions "init-second-brain". After init, the vault works automatically through CLAUDE.md every session without invoking this skill again.
---

# Init Second Brain

This skill runs **once** to set up an Obsidian vault as a personal Second Brain. After init, everything is driven by the generated `CLAUDE.md` — no skill needed in future sessions.

---

## Phase 1 — Vault Detection

Check the current working directory for `.obsidian/`. If found, confirm with the user that this is the right vault.

If `.obsidian/` is **not found**, offer to create a new vault here:
- "I don't see an Obsidian vault in this folder. I can create one here, or you can point me to an existing vault."
- Options: (a) create vault here, (b) provide path to existing vault
- If user chooses (a): create `.obsidian/` directory with a minimal `app.json` so Obsidian recognizes it as a vault:
  ```bash
  mkdir -p .obsidian
  echo '{}' > .obsidian/app.json
  ```
  Then confirm: "Vault created. You can open this folder in Obsidian later — it will be recognized automatically."
- If user provides a path: `cd` there and continue.
- Do not proceed until vault location is confirmed.

If `CLAUDE.md` already exists in the vault, show its first 10 lines and ask:
- "A CLAUDE.md already exists. Do you want to (a) reinitialize from scratch, (b) upgrade (add missing pieces only), or (c) cancel?"
- Upgrade mode = only create missing files, never overwrite.

Detect OS:
- Windows: `python`, `pip`, `winget` or `pip install yt-dlp`
- Mac: `python3`, `pip3`, `brew install yt-dlp`
- Linux: `python3`, `pip3`, `pip3 install yt-dlp`

---

## Phase 2 — Software Check

Check silently (don't alarm the user). Report at the end:

```bash
# Check yt-dlp
yt-dlp --version 2>/dev/null || echo "missing"

# Check Python (Windows uses 'python', Mac/Linux 'python3')
python --version 2>/dev/null || python3 --version 2>/dev/null || echo "missing"

# Check curl
curl --version 2>/dev/null | head -1 || echo "missing"
```

If yt-dlp is missing and user selected video tracking → offer to install:
- Windows: `pip install yt-dlp`
- Mac: `brew install yt-dlp` (or `pip3 install yt-dlp`)
- Linux: `pip3 install yt-dlp`

---

## Phase 3 — Interview

Ask all questions in **one message**, numbered. Wait for answers before proceeding.

Respond in whatever language the user writes. Generate vault content in English.

```
Hi! I'll set up your Second Brain in a few steps. Please answer these questions:

1. What's your name? (used to personalize your config)

2. What's your role / main occupation?
   (e.g. "Software engineer, Munich", "Student, Berlin", "Freelance designer")

3. Which knowledge domains interest you? (pick all that apply)
   tech · finance · health · science · design · business · creative · language-learning · other: ___

4. What content do you want to track? (pick all that apply)
   📹 YouTube videos    📰 Web articles    📚 Books
   🎬 Movies/series     🎵 Music           🔖 Bookmarks
   💼 Work notes        🔬 Research papers

5. Language for your notes:
   (a) English — recommended, best for searchability
   (b) My native language: ___
   (c) Both (English for wiki/knowledge, native for personal notes)

6. One optional question — do you want a brief daily/weekly review workflow?
   (a) Yes — add a /review command that checks for stale notes
   (b) No thanks
```

Store answers internally as variables for the next phases.

---

## Phase 4 — Folder Structure

Create the folder structure below. **Never overwrite existing files** — check before creating, log "skipped (exists)" vs "created".

### Always create

```
wiki/
  index.md           ← links all wiki sections
  hot.md             ← session context file (read: templates/hot.md)
  meta/
    index.md
    claude-memory.md ← persistent rules backup (read: templates/claude-memory.md)
notes/
  index.md
  ideas/
    index.md
    inbox.md         ← quick capture for ideas and todos
plans/
  index.md
prompts/
  index.md
  coding/
    index.md
  writing/
    index.md
  research/
    index.md
```

### Conditional folders

| User selected           | Create                               |
|------------------------|--------------------------------------|
| YouTube videos         | `wiki/{each domain}/` + index.md in each; `wiki/{domain}/channels.md` |
| Web articles           | `notes/articles/` + index.md         |
| Bookmarks              | `notes/bookmarks/` + index.md        |
| Books                  | `media/books.md`                     |
| Movies / TV series     | `media/movies.md`, `media/tv-series.md` |
| Music                  | `media/music.md`                     |
| Any media selected     | `media/index.md`                     |
| Work notes             | `work/` + `work/index.md`            |
| Research papers        | `notes/research/` + index.md        |

Create domain folders under `wiki/` for every domain the user selected. Example: user selected "tech, finance, health" → create `wiki/tech/`, `wiki/finance/`, `wiki/health/` each with `index.md`.

### File templates for generated files

**`wiki/hot.md`** — session context:
```markdown
---
tags: [meta, session-context]
---
# Hot Context

*~300 words of current session context. Claude reads this at start, updates at end.*

## Current focus
<!-- What am I working on right now? -->

## Recent decisions
<!-- Key decisions made in last few sessions -->

## Open threads
<!-- Things in progress, not forgotten -->
```

**`wiki/meta/claude-memory.md`** — persistent rules:
```markdown
---
tags: [meta, memory]
---
# Claude Memory

Persistent workflow rules and notes that complement CLAUDE.md.

## Workflow rules
<!-- Rules discovered through use — e.g. "always check for duplicates before saving" -->

## User profile notes
<!-- Extra context about how this person thinks and works -->

## Platform notes
<!-- OS-specific workarounds discovered in use -->
```

**`notes/ideas/inbox.md`** — capture inbox:
```markdown
---
tags: [inbox]
---
# Ideas & Todos Inbox

Quick capture. Review and organize periodically.

<!-- Claude appends here with: idea [text] or todo [text] commands -->
```

**`media/*.md`** — media trackers start with:
```markdown
---
tags: [media, {type}]
---
# {Type} Tracker

<!-- Claude appends validated entries here -->
```

**`wiki/{domain}/channels.md`** — creator profiles:
```markdown
---
tags: [channels, {domain}]
---
# {Domain} Channels & Creators

<!-- Claude appends creator profiles here -->
```

**Generic `index.md`** for all folders:
```markdown
---
tags: [index]
---
# {Folder Name}

| File | Description |
|------|-------------|
| ...  | ...         |

*Update this index when adding or removing notes.*
```

---

## Phase 5 — Generate CLAUDE.md

Generate `CLAUDE.md` in the vault root. Use the template below, substituting `{{VARIABLES}}` with actual answers. Only include sections marked `[conditional]` if the user selected the relevant content type.

```markdown
# {{USER_NAME}}'s Second Brain

## Owner
{{USER_ROLE}}

## Language Policy
- ALL vault content: English only (notes, frontmatter, filenames, indexes)
- {{CHAT_LANGUAGE_LINE}}
- Technical terms: keep English, never translate
{{IF_CODE_NOTES}}- Code comments in work projects: {{CODE_COMMENT_LANG}}{{/IF}}

## Installed Skills
kepano/obsidian-skills — load on-demand before operations:
- /obsidian-markdown → before creating/editing .md files
- /obsidian-bases → before working with .base files
- /json-canvas → before working with .canvas files
- /obsidian-cli → before using CLI commands
{{IF_WEB_CONTENT}}- /defuddle → before saving web content to vault{{/IF}}

## Custom Meta-Fields
Beyond standard Obsidian properties, every document adds:
```yaml
last_reviewed: YYYY-MM-DD    # when actuality was last verified
review_interval: 30d         # how often to re-check (7d/14d/30d/90d/never)
freshness: current           # current | stale | archived | draft
```
Rules:
- Any edit → update `updated` (standard Obsidian property)
- Actuality check → update `last_reviewed`
- last_reviewed + review_interval < today → set freshness: stale

## Context Management
- This file: permanent config only. Keep concise, avoid bloat.
- wiki/hot.md: session context (~300 words). Read first, update at end.
- wiki/meta/claude-memory.md: persistent workflow rules and user profile backup.

## Vault Conventions
- Every folder has index.md linking its children
- Atomic notes: one topic = one file
- Update parent index.md when adding/removing notes
{{IF_WIKI}}- Wiki hierarchy: wiki/{domain}/{tools|concepts|workflows}/{file}.md{{/IF}}

{{IF_VIDEO_OR_ARTICLE}}
## URL Saving Workflow
Before saving any URL (video, channel, article):
1. grep vault for the URL/handle — if found, stop immediately, show existing location
2. Determine type: video → fetch oEmbed + yt-dlp; channel → WebSearch/WebFetch to understand content
3. Categorise by video/channel title content, NOT by user's message context
4. Find or create matching wiki file → append entry
{{/IF}}

{{IF_MEDIA_OR_BOOKMARK}}
## Media & Bookmarks Validation Workflow
Before saving any movie/series/book/music entry or bookmark:
1. grep vault — if found, stop immediately, show existing location
{{IF_MOVIE_SERIES}}2. Movie/Series: `WebSearch "<title> site:imdb.com OR site:themoviedb.org"` → validate spelling, get original title, year, director/creator, genres, runtime/seasons count{{/IF}}
{{IF_BOOK}}3. Book: `WebSearch "<title> <author> site:goodreads.com OR site:openlibrary.org"` → validate, get author, year, pages, genre{{/IF}}
{{IF_MUSIC}}4. Music: WebSearch to validate artist/album spelling, get year and genre{{/IF}}
{{IF_BOOKMARK}}5. Bookmark: use /defuddle or WebFetch to understand the site → determine category → save to `notes/bookmarks/{category}.md`{{/IF}}
6. If title/name had typos → show the correction, confirm before saving
7. Append entry to matching file; create genre/category section if it doesn't exist yet

### Media entry formats
```
{{IF_MOVIE_SERIES}}Movie/Series: ### [Original Title](opt-watch-link) #to-watch
              Director · Year · Runtime · Genres
              One-line description.
{{/IF}}
{{IF_BOOK}}Book:         ### [Title](opt-link) #to-read
              Author · Year · ~N pages · Genre
              One-line description.
{{/IF}}
{{IF_MUSIC}}Music:        ### [Artist — Album or Track](opt-link) #to-listen
              Genre · Year
              One-line note.
{{/IF}}
{{IF_BOOKMARK}}Bookmark:     ### [Site Name](url)
              Category · What the site offers (1-2 sentences).
{{/IF}}
Idea/Todo:    ### Title #idea or #todo
              YYYY-MM-DD
              Brief context.
```
{{/IF}}

{{IF_VIDEO}}
## Tools

### YouTube metadata
```bash
# Title (handles any Unicode):
curl -s "https://www.youtube.com/oembed?url=URL&format=json"

# Duration / views / date:
{{PYTHON_CMD}} -c "
import json,sys,subprocess
r=subprocess.run(['yt-dlp','-j','--skip-download',__import__('sys').argv[1]],capture_output=True,text=True)
raw=r.stdout
lines=[l for l in raw.splitlines() if l.startswith('{')]
if not lines: print('duration: n/a\nupload_date: n/a\nview_count: n/a'); exit()
d=json.loads(lines[0])
for k in ['upload_date','duration','view_count']:
    print(f'{k}: {str(d.get(k,\"n/a\")).encode(\"ascii\",\"replace\").decode(\"ascii\")}')" "URL"
# If yt-dlp fails → gracefully prints n/a, never crashes
```
{{/IF}}

## Custom Commands
{{COMMANDS_LIST}}
Note: these are chat commands to Claude, not slash commands. Use without leading slash except for /prompt /plan /review /status.

## Vault Search

When the user asks a question about their saved content ("what books did I want to read?", "do I have anything on CapCut?"), use this three-tier strategy — start with the cheapest option that can answer the question.

**Tier 1 — Structural lookup** (content type → known file, no search needed):

| Question type | Go directly to |
|--------------|----------------|
| books to read | `media/books.md` → show `#to-read` entries |
| movies/series | `media/movies.md` or `media/tv-series.md` → `#to-watch` |
| music | `media/music.md` → `#to-listen` |
| ideas/todos | `notes/ideas/inbox.md` |
| channels by domain | `wiki/{domain}/channels.md` |
| bookmarks by category | `notes/bookmarks/{category}.md` |
| articles | `notes/articles/` → list files or grep |

**Tier 2 — Targeted grep** (keyword, known zone):

```bash
# Videos/notes on a specific tool or topic
grep -ril "CapCut" wiki/

# Bookmarks about a topic
grep -i "design" notes/bookmarks/

# Any note mentioning a person or project
grep -ril "Kubernetes" wiki/ notes/
```

**Tier 3 — Vault-wide grep** (when unsure of location):

```bash
grep -ril "keyword" . --include="*.md" | grep -v ".obsidian"
```

Then read the top matches to extract relevant context.

**When to use Obsidian CLI:** only for complex queries involving multiple properties simultaneously (e.g., `freshness: current` AND `tags: kubernetes`) or for backlink traversal. For most natural-language questions, Tier 1–2 is faster and uses less context.

**Answer format:** be concise and direct. For list questions ("what books?"), show a formatted list with key metadata. Don't dump the whole file — extract and summarize.

## Session-End Review
At the end of each session, briefly analyze:
- What friction occurred and why
- What decisions were made that should become workflow rules
- Update memory and this file if warranted

## Feedback
I welcome constructive criticism. Point out problems freely.
```

### Variable substitution guide

| Variable | Source |
|----------|--------|
| `{{USER_NAME}}` | Answer to Q1 |
| `{{USER_ROLE}}` | Answer to Q2 |
| `{{CHAT_LANGUAGE_LINE}}` | If native lang ≠ English: "User chats in {lang} — respond in their language, write vault content in English". If English: "Respond in English." |
| `{{IF_CODE_NOTES}}` | If work notes selected |
| `{{IF_WEB_CONTENT}}` | If videos or articles selected |
| `{{IF_VIDEO}}` | If YouTube videos selected |
| `{{IF_MEDIA_OR_BOOKMARK}}` | If any of: movies, books, music, bookmarks selected |
| `{{PYTHON_CMD}}` | "python" on Windows, "python3" on Mac/Linux |
| `{{COMMANDS_LIST}}` | See below |

### Commands list — include only relevant commands

```
{{IF_VIDEO}}- /video [URL] — save YouTube video → wiki/{domain}/tools/{file}.md or workflows/{file}.md{{/IF}}
{{IF_VIDEO}}- /channel [URL] — save creator profile → wiki/{domain}/channels.md{{/IF}}
{{IF_ARTICLE}}- /article [URL] — save web article (uses /defuddle) → notes/articles/{{/IF}}
{{IF_MOVIE_SERIES}}- movie [title] [opt: watch-link] — validate via IMDB, save → media/movies.md{{/IF}}
{{IF_MOVIE_SERIES}}- series [title] [opt: watch-link] — validate via IMDB, save → media/tv-series.md{{/IF}}
{{IF_BOOK}}- book [title by author] — validate via Goodreads, save → media/books.md{{/IF}}
{{IF_MUSIC}}- music [artist/album/track] [opt: link] — validate, save → media/music.md{{/IF}}
{{IF_BOOKMARK}}- bookmark [URL] — fetch site info, save → notes/bookmarks/{category}.md{{/IF}}
- idea [text] — quick capture → notes/ideas/inbox.md (tag: #idea)
- todo [text] — quick capture → notes/ideas/inbox.md (tag: #todo)
- /prompt [topic] — create/find in prompts/
- /plan [desc] — create in plans/
{{IF_REVIEW}}- /review — find stale docs, orphans, update indexes{{/IF}}
- /status — vault stats (file count, recent changes, stale notes)
```

---

## Phase 6 — Memory System

Initialize Claude's persistent memory for this vault. The memory path is based on the vault's absolute path.

1. Find or create the memory directory:
   - Windows: `C:\Users\{username}\.claude\projects\{encoded-vault-path}\memory\`
   - Mac/Linux: `~/.claude/projects/{encoded-vault-path}/memory/`
   - The encoded path replaces path separators with `-` (e.g. `C:\Users\alice\vault` → `C--Users-alice-vault`)

2. Create `MEMORY.md` (the index):
```markdown
# Memory Index

- [User Profile](user_profile.md) — {USER_NAME}, {USER_ROLE_SHORT}
- [Vault Project](project_vault.md) — Second Brain vault structure and conventions
{{IF_VIDEO}}- [Video Saving Workflow](feedback_video_workflow.md) — how to save and categorize videos{{/IF}}
```

3. Create `user_profile.md`:
```markdown
---
name: User Profile
description: {USER_NAME}'s role, interests, and working style
type: user
---

{USER_NAME}. {USER_ROLE}.
Domains of interest: {DOMAINS_LIST}.
Content tracked: {CONTENT_LIST}.
Notes language: {NOTES_LANGUAGE}.
```

4. Create `project_vault.md`:
```markdown
---
name: Vault Project
description: Second Brain vault structure, skills, and conventions
type: project
---

Obsidian vault initialized with init-second-brain skill.
Structure: wiki/ (knowledge), notes/ (personal), media/ (tracking), plans/, prompts/{IF_WORK}, work/{/IF}.
Domains: {DOMAINS_LIST}.

**Why:** Personal knowledge management and offloading browser tabs.
**How to apply:** Follow CLAUDE.md commands and conventions for all vault operations.
```

---

## Phase 7 — Final Summary

Show a clean summary after everything is done:

```
✓ Second Brain initialized!

Vault: {VAULT_PATH}
Owner: {USER_NAME} — {USER_ROLE}

Created:
  wiki/                  knowledge base
  {CONDITIONAL_FOLDERS}
  notes/                 personal notes + ideas inbox
  plans/                 project planning
  prompts/               AI prompt library
  CLAUDE.md              your personalized config ← the key file

Software:
  yt-dlp: {installed/already present/skipped}
  Python: {version/missing}

Your vault is ready. From now on, just open Claude Code in this folder.
Claude will read CLAUDE.md automatically and know exactly how to help you.

Commands available: {COMMANDS_SUMMARY}

Next steps:
  1. Open this folder in Obsidian
  2. Open a terminal here and run: claude
  3. Try: idea first thought in my second brain
```

If any software is missing that was needed, show install instructions clearly at the end.

---

## Notes for Claude

- Be encouraging but not verbose — the user is setting up something new and might be a bit overwhelmed.
- If the user makes a mistake in their answers (e.g., picks "English" but then chats in German), adapt CLAUDE.md to match their actual language.
- The goal is zero friction after init. Every question asked now saves ten questions later.
- If the user already has some folders (e.g., a `notes/` folder), skip creating them but check if they're missing `index.md`.
- After creating CLAUDE.md, suggest the user review it and say "looks good" or point out anything to change.
