# Second Brain — Claude Code Skill

A Claude Code skill that initializes an Obsidian vault as a personalized Second Brain.

## What it does

Run once. Answer 6 questions. Get a fully configured personal knowledge base that works automatically with Claude Code in every future session.

**After init, no skill needed** — your vault's `CLAUDE.md` handles everything.

---

## The idea: why you need a second brain

> *"Information overload has become information exhaustion, taxing our mental resources and leaving us constantly anxious that we're forgetting something."*
> — Tiago Forte, *Building a Second Brain* (2022)

Your biological brain is not designed for storage — it's designed for thinking. Yet modern knowledge workers spend an average of **76 hours per year** searching for misplaced notes, files, or information. Meanwhile, **45% of people** have more than 20 browser tabs open at any given moment, and researchers at Carnegie Mellon found that people keep tabs open because of a deep fear: *"as soon as something goes out of sight, it's gone."*

A Second Brain is an external system that stores your knowledge so your mind doesn't have to. You capture ideas and information once, in a structured place, and trust that you'll find them later — freeing your working memory for actual thinking.

### The methodology behind this

The concept was popularized by **Tiago Forte** through his [Building a Second Brain](https://www.buildingasecondbrain.com/) framework, which distills decades of knowledge management research into a simple process called **CODE**:

- **Capture** — Save anything worth keeping: articles, videos, ideas, quotes
- **Organize** — Structure by actionability, not by topic
- **Distill** — Extract the essence so you can find the value later
- **Express** — Use your knowledge to create, decide, and act

The roots go deeper. In the mid-20th century, German sociologist **Niklas Luhmann** built a physical card system called *Zettelkasten* ("slip box") — 90,000 interlinked index cards that he credited as his true productivity engine. He published 50 books and 600 articles in his lifetime. Modern tools like Obsidian are essentially digital Zettelkästen.

**David Allen's** *Getting Things Done* (2001) laid the groundwork for a key principle: your mind is for having ideas, not for holding them. A trusted external system eliminates the constant background anxiety of "I might be forgetting something."

Philosopher **Andy Clark** called this *the extended mind* — the idea that when external tools are tightly integrated with how you think, they become genuine extensions of cognition, not just storage.

This skill brings all of that into Claude Code + Obsidian.

---

## Getting started

### Step 1 — Install Obsidian

[Obsidian](https://obsidian.md) is a free, local-first note-taking app that stores everything as plain markdown files.

- **Windows / Mac / Linux:** Download from [obsidian.md](https://obsidian.md)
- Create a new vault (folder) anywhere on your computer — or use an existing folder

### Step 2 — Install Claude Code

[Claude Code](https://claude.ai/code) is Anthropic's AI assistant that runs in your terminal and understands your project context.

```bash
# Install via npm
npm install -g @anthropic-ai/claude-code

# Or via Homebrew (Mac)
brew install claude-code
```

After install, authenticate:
```bash
claude login
```

You'll need an [Anthropic account](https://console.anthropic.com). Claude Code requires a paid API plan or Claude Pro subscription.

### Step 3 — Install this skill

```bash
claude plugins install gh:olexiy/second-brain
```

### Step 4 — Initialize your vault

Open a terminal inside your Obsidian vault folder, then run Claude Code:

```bash
cd /path/to/your/vault
claude
```

Then tell Claude:

```
init-second-brain
```

Claude will ask you 6 questions and set everything up.

---

## What gets created

```
vault/
├── CLAUDE.md              ← your personalized config (the key file)
├── wiki/                  ← reference knowledge, organized by domain
│   ├── hot.md             ← session context Claude reads every time
│   └── meta/
│       └── claude-memory.md
├── notes/
│   └── ideas/
│       └── inbox.md       ← quick capture for ideas and todos
├── plans/
├── prompts/
└── media/                 ← if you track movies/books/music
```

## After init — example commands

```
> /video https://youtube.com/watch?v=...    # save a YouTube video to wiki
> movie Inception                            # add to watchlist
> book Atomic Habits by James Clear         # add to reading list
> idea build a morning routine tracker      # quick capture
> bookmark https://example.com              # save a site
> what books did I want to read?            # search your vault
> do I have anything on Kubernetes?         # ask anything
```

## Features

- Detects your Obsidian vault automatically — or creates one if it doesn't exist
- Installs required tools (yt-dlp for video metadata)
- Personalizes structure and commands based on your interests and content types
- Creates `CLAUDE.md` with workflows tailored to you
- Non-destructive: never overwrites existing files
- Sets up Claude's persistent memory for your vault
- Smart search: Claude knows your structure and finds things without full-text search overhead

## Requirements

- [Obsidian](https://obsidian.md) (free)
- [Claude Code](https://claude.ai/code) (requires Anthropic account)
- Python (for yt-dlp, if you track videos)

**Installed automatically during init:**
- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) — teaches Claude proper Obsidian markdown syntax

---

*Built on the shoulders of Tiago Forte, Niklas Luhmann, David Allen, and Andy Clark.*
