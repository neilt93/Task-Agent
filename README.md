# Tasks

Personal task vault built on [Obsidian](https://obsidian.md) with the [Minimal](https://github.com/kepano/obsidian-minimal) theme.

## Structure

```
Home.md        — links + due/overdue tasks + recent notes + active projects
Inbox.md       — quick capture
Daily/         — one note per day
Weekly/        — one note per week (weekly reviews)
Projects/      — one note per project
Templates/     — daily, weekly & project templates
Archive/       — old stuff
```

## Plugins

Dataview, Tasks, Calendar, Periodic Notes, Obsidian Git, Style Settings.

## AI Agent Access

Full conventions are in [`CLAUDE.md`](CLAUDE.md). Quick reference for any Claude instance:

**Vault path:**
```
/Users/neiltripathi/Library/Mobile Documents/com~apple~CloudDocs/Tasks
```

**To update a project**, edit the relevant file in `Projects/`:
- `Projects/Sympli.md`
- `Projects/TAP Score.md`
- `Projects/Mirror Bot.md`
- `Projects/Crypto Safety Wallet.md`
- `Projects/Cloud GPU Automation.md`
- `Projects/OpenKBP.md`
- `Projects/Paper with Davis.md`

Add tasks, notes, or update the goal/status. Use `status: active | completed | on-hold` in frontmatter.

**To add a quick task:** append `- [ ] Task` to `Inbox.md`

**To log a job application:** append `- [x] Company - Role` to `Jobs.md`

**To create a new project:** copy the format from `Templates/Project.md` into `Projects/<Name>.md`

**When finishing work on a project**, update its file in `Projects/`:
- Check off completed tasks (`- [x]`)
- Add new tasks discovered during the session
- Add relevant notes (commands, decisions, gotchas, port numbers, etc.)
- Update `status` in frontmatter if the project is done or paused

## Setup

Open this folder as a vault in Obsidian. Enable community plugins. The Minimal theme and plugin configs are included.
