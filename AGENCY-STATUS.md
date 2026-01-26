# Claude Agency - Project Status Dashboard

Quick index of all projects managed by this agency.

---

## Active Projects

| Project | Status | Tech Stack | CLAUDE.md |
|---------|--------|------------|-----------|
| Flashcards App | Active | Kotlin Spring Boot + React Native/Expo | `projects/flashcards-app/CLAUDE.md` |

---

## Project Summaries

### Flashcards App

**Description**: A simple but modern flashcard iOS app for studying.

**Tech Stack**:
- Backend: Kotlin, Spring Boot, PostgreSQL
- Frontend: React Native, Expo, NativeWind (TailwindCSS)

**Status**: Active development

**Repositories**:
- `projects/flashcards-app/flashcards-backend/` (Kotlin Spring Boot)
- `projects/flashcards-app/flashcards-frontend/` (React Native)

---

## Upcoming Projects

| Project | Status | Tech Stack | Notes |
|---------|--------|------------|-------|
| AI Fitness Coach | Planned | TBD | Located at `/Users/matias/Projects/AI-Fitness-coach/` |
| Time Tracking App | Planned | TBD | Backend + Frontend repos separate |

---

## Agency Structure

```
claude-agency/
├── CLAUDE.base.md          # Shared orchestration rules
├── AGENCY-STATUS.md        # This file
├── .claude/
│   ├── agents/             # Tech-specific and generic agent specs
│   └── skills/             # Shared skills
├── prompt-templates/       # Shared prompt templates
├── system/                 # Cross-project learnings
│   ├── common-issues.md
│   ├── best-practices.md
│   ├── orchestration/
│   ├── feedbacks/
│   └── cross-project-analysis/
├── snapshots/              # GITIGNORED - local backups
└── projects/               # GITIGNORED - project working directories
    └── flashcards-app/
```

---

## Quick Links

- Base configuration: `CLAUDE.base.md`
- Agent specs: `.claude/agents/`
- Prompt templates: `prompt-templates/`
- Cross-project learnings: `system/`
