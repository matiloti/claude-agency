# Project Onboard

> Owner: Orchestrator | Version: 1.2 | Last Updated: 2026-01-27

Onboard a new project to the agency system by creating the standard directory structure and configuration files.

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| project_name | Yes | Name of the project (e.g., "spend-manager") |
| project_path | Yes | Path to the project folder under `projects/` |
| backend_folder | No | Name of backend subfolder (default: `{project_name}-backend`) |
| frontend_folder | No | Name of frontend subfolder (default: `{project_name}-frontend`) |

## Preconditions

- [ ] Project folder exists in `projects/` directory
- [ ] Project has at least one repository subfolder (backend and/or frontend)
- [ ] No existing CLAUDE.md in the project root (or user confirms overwrite)

## Exit States

- **Success**: All directories and files created, project ready for ideation
- **Failure**: Project folder doesn't exist or has invalid structure
- **Partial**: N/A (onboarding is atomic)

---

## What Gets Created

### 1. Project Configuration

**File**: `{project_path}/CLAUDE.md`

Contains:
- Reference to base orchestrator config (`../../CLAUDE.base.md`)
- Project overview (placeholder for user to fill)
- Tech stack table (placeholder for user to fill)
- Repository structure documentation
- Path resolution for shared resources
- Project-specific rules section
- Agent selection table (placeholder for user to fill)

### 2. Knowledge Base

**Directory**: `{project_path}/knowledge/`

Files:
- `README.md` - Knowledge base index with directory structure, ownership, naming conventions
- `project-status.md` - Project status tracker (orchestrator-owned)

The README documents all subdirectories that will be created as the project evolves:
- `ideas/`, `personas/`, `user-stories/`, `features/` (Ideator)
- `architecture/` with `decisions/`, `api/`, `database/`, `components/`, `plans/` (Architect)
- `frontend/` with `components/`, `design-system/`, `status/` (Frontend Developer)
- `backend/` with `test-reports/`, `status/`, `api-docs/` (Backend Developer)
- `infrastructure/` with `decisions/`, `status/` (Infrastructure Engineer)
- `qa/` with `test-plans/`, `test-cases/`, `bugs/`, `reports/` (QA Tester)
- `reviews/` with agent subdirectories (Judge)
- `handoffs/`, `blockers/`, `questions/`, `retrospectives/`, `backlog/`
- `kanban/` with `backlog.md`, `todo.md`, `ongoing.md`, `done.md` (Orchestrator)

### 3. Kanban Board

**Directory**: `{project_path}/knowledge/kanban/`

Files:
- `backlog.md` - Ideas and future work (not yet prioritized)
- `todo.md` - Prioritized tasks ready to start
- `ongoing.md` - Currently being worked on
- `done.md` - Completed tasks

Each file is initialized with a header template. See CLAUDE.base.md "Task Kanban Board" section for task entry format and rules.

### 4. Operational Directories

**Directories** (with `.gitkeep`):
- `{project_path}/sessions/` - Session logs organized by date
- `{project_path}/feedbacks/` - Feedback processing pipeline
- `{project_path}/snapshots/` - System state snapshots for rollback

### 5. Project-Specific Ideator

**Files** (created via agent-specificator):
- `../../.claude/agents/{project_name}-product-ideator.md` - Domain-specialized ideator agent
- `../../system/templates/prompts/{project_name}-product-ideator.md` - Corresponding prompt template

The ideator is customized with:
- Domain-specific knowledge and principles
- Target users relevant to the project domain
- Persona traits mapped to design implications

---

## Workflow Steps

### Step 1: Validate Project Structure

Check that:
1. Project folder exists at the specified path
2. At least one repository subfolder exists (backend or frontend)
3. Each repository subfolder has a `.git` directory

```bash
# Validation commands
ls -la {project_path}
ls -la {project_path}/{backend_folder}/.git 2>/dev/null
ls -la {project_path}/{frontend_folder}/.git 2>/dev/null
```

### Step 2: Check for Existing Configuration

If `{project_path}/CLAUDE.md` exists:
- Warn the user
- Ask for confirmation before overwriting
- If declined, abort onboarding

### Step 3: Create CLAUDE.md

Create the project orchestrator configuration file using the template below.

**Template**:

```markdown
# {Project Name} - Orchestrator

**IMPORTANT**: Before proceeding, read the base orchestrator configuration from `../../CLAUDE.base.md`. All rules, workflows, and agent definitions in that file apply to this project.

---

# Project Context

## Project Overview

<!-- TODO: Add project description -->
{Brief description of the project}

## Tech Stack

<!-- TODO: Fill in the actual tech stack -->
| Layer | Technology |
|-------|-----------|
| Mobile Frontend | <!-- e.g., React Native, Swift, etc. --> |
| Backend | <!-- e.g., Kotlin Spring Boot, Node.js, etc. --> |
| Database | <!-- e.g., PostgreSQL, MongoDB, etc. --> |
| Testing (Backend) | <!-- e.g., JUnit, Jest, etc. --> |
| Testing (Frontend) | <!-- e.g., React Testing Library, XCTest, etc. --> |

## Repository Structure

The `{backend_folder}/` and `{frontend_folder}/` directories are separate git repositories (each has its own `.git`). All git operations (commit, push) must be run from within the respective subdirectory.

**IMPORTANT: Working Directory Awareness** — This project is divided into `{frontend_folder}/` and `{backend_folder}/` folders, each with its own git repository. You must always be aware of your current working directory. When running commands (git, npm, gradle, etc.), ensure you are in the correct subdirectory. Use absolute paths or explicitly `cd` into the appropriate folder before executing commands. Never run git operations from the project root — always run them from within the respective `{frontend_folder}/` or `{backend_folder}/` subdirectory.

---

# Path Resolution for Shared Resources

Shared resources are located at the agency root:
- Agent specs: `../../.claude/agents/`
- Prompt templates: `../../system/templates/prompts/`
- Skills: `../../.claude/skills/`
- System orchestration docs: `../../system/orchestration/`
- Cross-project learnings: `../../system/common-issues.md`, `../../system/best-practices.md`

Project-specific resources:
- Knowledge: `knowledge/`
- Sessions: `sessions/`
- Feedbacks: `feedbacks/`
- Snapshots: `snapshots/`

---

# Project-Specific Rules

<!-- TODO: Add project-specific rules as they emerge -->
1. **TBD**: Add rules as the project develops.

---

# Agent Selection

<!-- TODO: Select tech-specific agents based on the chosen tech stack -->
For this project, use these tech-specific agents from the agency:

| Role | Agent File |
|------|------------|
| Architect | <!-- e.g., kotlin-spring-postgres-architect.md --> |
| Backend Developer | <!-- e.g., kotlin-spring-boot-backend-developer.md --> |
| Frontend Developer | <!-- e.g., react-native-expo-frontend-developer.md --> |
| UX Designer | <!-- e.g., ios-ux-designer.md --> |
| Judge | <!-- e.g., kotlin-spring-react-native-judge.md --> |

| Ideator | <!-- e.g., {project}-product-ideator.md --> |

Generic agents (qa-tester, documentation, retrospective, ci-agent) are used as-is.
```

### Step 4: Create Knowledge Base

Create `{project_path}/knowledge/README.md` with the standard knowledge index template.

Create `{project_path}/knowledge/project-status.md` with initial status:

```markdown
# Project Status

<!-- Last Updated: {YYYY-MM-DD} -->
<!-- Status: Current -->
<!-- Author: orchestrator -->

## Current Phase: Project Setup

The project has been onboarded to the agency system. Ready to begin ideation phase.

## Recent Changes

### {YYYY-MM-DD}: Project Onboarding (COMPLETE)
- **Type**: Project setup
- **Status**: COMPLETE
- **Changes**:
  - Created project CLAUDE.md with orchestrator configuration
  - Initialized knowledge base structure
  - Created sessions/, feedbacks/, snapshots/ directories
  - Created project-specific ideator: `{project_name}-product-ideator.md`
  - Project ready for ideation phase

## Completed Phases

### 0. Project Setup (Complete)
- Project onboarded to agency system
- Directory structure initialized
- Ready for first feature workflow

## Next Steps

1. **Fill in CLAUDE.md**: Define tech stack and select appropriate agents (ideator already configured)
2. **Begin Ideation**: Run `{project_name}-product-ideator` to define personas, user stories, and features
3. **Architecture**: After ideation approval, architect designs the system
4. **Implementation**: Backend and frontend development following the standard workflow

## Repository Commits

### {backend_folder}
- (No commits yet - project just onboarded)

### {frontend_folder}
- (No commits yet - project just onboarded)

## Remaining Work

- [ ] Define tech stack in CLAUDE.md
- [ ] Select tech-specific agents (ideator already done)
- [ ] Complete ideation phase with {project_name}-product-ideator
- [ ] Design architecture
- [ ] Implement MVP features
```

### Step 5: Create Kanban Board

Create `{project_path}/knowledge/kanban/` directory with the following files:

**backlog.md**:
```markdown
# Kanban: Backlog

Tasks in this file are ideas and future work that have not yet been prioritized.

---

<!-- Tasks will be added here as they are identified -->
```

**todo.md**:
```markdown
# Kanban: To Do

Tasks in this file are prioritized and ready to be started.

---

<!-- Tasks will be moved here from backlog when prioritized -->
```

**ongoing.md**:
```markdown
# Kanban: Ongoing

Tasks in this file are currently being worked on by an agent.

---

<!-- Tasks will be moved here from todo when an agent is spawned -->
```

**done.md**:
```markdown
# Kanban: Done

Tasks in this file have been completed.

---

<!-- Tasks will be moved here from ongoing when completed -->
```

### Step 6: Create Operational Directories

Create the following with `.gitkeep` files:
- `{project_path}/sessions/.gitkeep`
- `{project_path}/feedbacks/.gitkeep`
- `{project_path}/snapshots/.gitkeep`

### Step 7: Create Project-Specific Ideator

Spawn an **agent-specificator** subagent to create a domain-specific ideator for this project.

**Subagent prompt**:

```markdown
## Task: Create Project-Specific Ideator

Create a new ideator agent specialized for the {project_name} domain.

### Reference
- Read existing ideator examples: `../../.claude/agents/flashcards-product-ideator.md`, `../../.claude/agents/fitness-product-ideator.md`, `../../.claude/agents/spend-manager-product-ideator.md`
- Read the agent-specificator spec for quality guidelines: `../../.claude/agents/agent-specificator.md`

### Project Context
- **Project Name**: {project_name}
- **Project Description**: {project_description from CLAUDE.md or user input}

### Requirements
1. **Analyze the domain**: Identify key principles, target users, and persona traits relevant to {project_name}
2. **Create agent spec**: Write to `../../.claude/agents/{project_name}-product-ideator.md`
   - Role specialized for the domain
   - Domain-specific knowledge section with key principles
   - Target users relevant to this domain
   - Persona interpretation guide with domain-specific traits
   - Standard ideator responsibilities and output format
3. **Create prompt template**: Write to `../../system/templates/prompts/{project_name}-product-ideator.md`
   - Standard ideator template structure
   - Project-specific session log naming

### Output Format
Report what was created and key domain decisions made.
```

**After subagent completes**:
- Verify both files were created
- Update the project's CLAUDE.md Agent Selection table to reference the new ideator

### Step 8: Report Completion

Output a summary:

```markdown
## Project Onboarding Complete

**Project**: {project_name}
**Location**: {project_path}

### Created Files
- `CLAUDE.md` - Project orchestrator configuration
- `knowledge/README.md` - Knowledge base index
- `knowledge/project-status.md` - Project status tracker
- `knowledge/kanban/backlog.md` - Kanban backlog
- `knowledge/kanban/todo.md` - Kanban to do
- `knowledge/kanban/ongoing.md` - Kanban ongoing
- `knowledge/kanban/done.md` - Kanban done
- `sessions/.gitkeep` - Session logs directory
- `feedbacks/.gitkeep` - Feedbacks directory
- `snapshots/.gitkeep` - Snapshots directory
- `../../.claude/agents/{project_name}-product-ideator.md` - Domain-specific ideator
- `../../system/templates/prompts/{project_name}-product-ideator.md` - Ideator prompt template

### Next Steps
1. Edit `CLAUDE.md` to fill in:
   - Project description
   - Tech stack
   - Agent selection (ideator already configured)
2. Begin ideation phase with `{project_name}-product-ideator`
```

---

## Examples

### Basic Usage

```
User: Onboard the spend-manager project
```

Execute with:
- `project_name`: "spend-manager"
- `project_path`: "projects/spend-manager"
- `backend_folder`: "spend-manager-backend"
- `frontend_folder`: "spend-manager-frontend"

### Custom Folder Names

```
User: Onboard my-app project with "api" and "web" folders
```

Execute with:
- `project_name`: "my-app"
- `project_path`: "projects/my-app"
- `backend_folder`: "api"
- `frontend_folder`: "web"

---

## Related Documentation

- Base orchestrator config: `CLAUDE.base.md`
- Reference project: `projects/flashcards-app/`
- System orchestration: `system/orchestration/`
- Session log format: `system/formats/session-log.md`
- Continuous improvement: `system/orchestration/continuous-improvement.md`
