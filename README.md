# Second Brain — Claude Code Skill

A Claude Code skill that initializes an Obsidian vault as a personalized Second Brain.

## What it does

Run once. Answer 6 questions. Get a fully configured personal knowledge base that works automatically with Claude Code in every future session.

**After init, no skill needed** — your vault's `CLAUDE.md` handles everything.

## Features

- Detects your Obsidian vault automatically
- Installs required tools (yt-dlp for video metadata)
- Personalizes structure based on your interests and content types
- Creates `CLAUDE.md` with commands tailored to you
- Non-destructive: never overwrites existing files
- Sets up Claude's persistent memory for your vault

## Install

```bash
claude plugins install gh:olexiy/second-brain
```

## Usage

Open a terminal inside your Obsidian vault, then:

```
claude
> init-second-brain
```

Or just tell Claude: **"set up my second brain"**

## What gets created

```
vault/
├── CLAUDE.md              ← your personalized config (the main artifact)
├── wiki/                  ← reference knowledge
│   ├── hot.md             ← session context
│   └── meta/
│       └── claude-memory.md
├── notes/
│   └── ideas/
│       └── inbox.md       ← quick capture
├── plans/
├── prompts/
└── media/                 ← if you track movies/books/music
```

## After init — example commands

```
> /video https://youtube.com/watch?v=...    # save a YouTube video
> movie Inception                            # add to watchlist
> book Atomic Habits by James Clear         # add to reading list
> idea build a morning routine tracker      # quick capture
> bookmark https://example.com              # save a site
```

## Philosophy

Your browser has 47 tabs open. Your brain is full. Claude Code helps you close them — saving knowledge where you can actually find it later, in a structure that grows with you.

## Requirements

- [Obsidian](https://obsidian.md) (free)
- [Claude Code](https://claude.ai/code)
- Python (for yt-dlp, if you track videos)
- kepano/obsidian-skills plugin (recommended)
