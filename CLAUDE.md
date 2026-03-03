# Task Vault

**Absolute path:** `/Users/neiltripathi/Library/Mobile Documents/com~apple~CloudDocs/Tasks`

Obsidian vault for personal productivity. Minimal theme, daily-note-driven.

## Structure

```
Home.md          — dashboard with links + dataview queries
Inbox.md         — quick task capture
Jobs.md          — weekly job application tracker (target: 50/week)
Daily/           — daily notes (YYYY-MM-DD.md)
Projects/        — one note per project
Templates/       — Daily.md, Project.md
Archive/         — completed/old stuff
```

## How to add tasks

Add to `Inbox.md` as checkbox lines:
```
- [ ] Task description
```

## How to add a project

Create `Projects/<Name>.md` with this format:
```
---
status: active
---

## Goal


## Tasks
- [ ]

## Notes

```

## How to log a job application

Add a checked task line to `Jobs.md` under the `## This Week` section:
```
- [x] Company - Role
```
The dataview counter at the top updates automatically.

## After working on a project

Update the project's file in `Projects/` before ending the session:
- Check off completed tasks (`- [x]`)
- Add new tasks discovered during the session
- Add relevant notes (commands, decisions, gotchas, port numbers, etc.)
- Update `status` in frontmatter if the project is done or paused

## Conventions

- No numbered folder prefixes — keep names plain
- Frontmatter: `status` field for projects (active/completed/on-hold)
- Daily notes created from `Templates/Daily.md` via Periodic Notes plugin
- Don't edit Templates/ unless changing the format for future notes
- Keep it simple — add structure only when needed
