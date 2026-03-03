# Task-Agent — Obsidian Vault

This is a personal task vault built on Obsidian. It is used across both Mac and Windows machines, synced via Git.

## Structure

- `Home.md` — Dashboard with due/overdue tasks, recent daily notes, active projects, and navigation links
- `Inbox.md` — Quick capture for new tasks and ideas
- `Daily/` — One note per day, filename format: `YYYY-MM-DD.md`
- `Weekly/` — One note per week, filename format: `YYYY-[W]ww.md` (e.g., `2026-W09.md`)
- `Projects/` — One note per project, created from `Templates/Project.md`
- `Templates/` — Templates for daily notes, weekly reviews, and projects
- `Archive/` — Completed/old items

## Daily Notes

- Always create daily notes in `Daily/` with the format `YYYY-MM-DD.md`
- Use the structure from `Templates/Daily.md` (date header, Tasks, Notes, Journal sections)
- Check if today's daily note exists before creating a new one

## Weekly Notes

- Create weekly review notes in `Weekly/` with the format `YYYY-[W]ww.md`
- Use the structure from `Templates/Weekly.md` (Review, Plan, Notes sections)
- Weekly notes are created via the Calendar sidebar (click a week number) or Periodic Notes plugin

## Tasks

- Tasks use Obsidian Tasks plugin syntax: `- [ ] Task name`
- Due dates use the format: `📅 YYYY-MM-DD`
- Quick-capture tasks go in `Inbox.md`
- Daily tasks go in the day's note under `## Tasks`

## Projects

- Create project notes in `Projects/` using the `Templates/Project.md` structure
- Projects have frontmatter with `status: active` (or `on-hold`, `completed`) and `created: YYYY-MM-DD`
- Each project has Goal, Tasks, Milestones, Log, and Notes sections

## Config Paths

- Daily notes folder: `Daily/`, template: `Templates/Daily`
- Weekly notes folder: `Weekly/`, template: `Templates/Weekly`
- Templates folder: `Templates/`

## Plugins

Dataview, Tasks, Calendar, Periodic Notes, Obsidian Git, Style Settings.
Plugin files (main.js, styles.css) are tracked in git so clones work out of the box.

## Git Workflow

- Commit and push changes after making updates to keep Mac and Windows in sync
- Pull before making changes to avoid conflicts
- The Obsidian Git plugin handles auto-sync when the vault is open
