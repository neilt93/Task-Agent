---
status: active
created: 2026-03-03
---

## Goal

Version-controlled LaTeX resume inside the Obsidian vault. Edit the `.tex` source, rebuild with one command, and get a PDF.

## Tasks
- [x] Create `Resume/resume.tex` with full content
- [x] Create `Resume/build.sh` build script
- [x] Add `Resume/*.pdf` to `.gitignore`
- [ ] Install tectonic: `winget install tectonic-typesetting.tectonic`
- [ ] Run first build and verify output matches the January 27 PDF

## Build

```bash
# From the vault root
bash Resume/build.sh

# Or directly
cd Resume && tectonic resume.tex
```

Source: [[Resume/resume.tex]]

## Milestones
| Target Date | Milestone | Status |
|---|---|---|
| 2026-03-03 | LaTeX source committed | Done |
| 2026-03-03 | First successful tectonic build | Pending |

## Log

- 2026-03-03: Created `.tex` source from existing PDF content, added build script and gitignore entry.

## Notes

- Compiler: [tectonic](https://tectonic-typesetting.github.io/) (auto-downloads packages, no TeX Live needed)
- Font: Source Sans Pro (loaded via `sourcesanspro` package)
- Build artefacts (`*.pdf`) are gitignored
