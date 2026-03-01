# Task-Agent — Obsidian Vault

This is a personal task vault built on Obsidian. It is used across both Mac and Windows machines, synced via Git.

## Structure

- `Home.md` — Dashboard with due tasks, recent daily notes, and navigation links
- `Inbox.md` — Quick capture for new tasks and ideas
- `Daily/` — One note per day, filename format: `YYYY-MM-DD.md`
- `Projects/` — One note per project, created from `Templates/Project.md`
- `Templates/` — Templates for daily notes and projects
- `Archive/` — Completed/old items

## Daily Notes

- Always create daily notes in `Daily/` with the format `YYYY-MM-DD.md`
- Use the structure from `Templates/Daily.md` (Tasks, Notes, Journal sections)
- Check if today's daily note exists before creating a new one

## Tasks

- Tasks use Obsidian Tasks plugin syntax: `- [ ] Task name`
- Due dates use the format: `📅 YYYY-MM-DD`
- Quick-capture tasks go in `Inbox.md`
- Daily tasks go in the day's note under `## Tasks`

## Projects

- Create project notes in `Projects/` using the `Templates/Project.md` structure
- Projects have frontmatter with `status: active` (or `on-hold`, `completed`)
- Each project has Goal, Tasks, and Notes sections

## Plugins

Dataview, Tasks, Calendar, Periodic Notes, Obsidian Git, Style Settings.
Plugin files (main.js, styles.css) are tracked in git so clones work out of the box.

## Git Workflow

- Commit and push changes after making updates to keep Mac and Windows in sync
- Pull before making changes to avoid conflicts
- The Obsidian Git plugin handles auto-sync when the vault is open
