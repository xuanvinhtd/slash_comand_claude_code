# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is Scopecraft, a structured development workflow system that provides Claude commands for organized software development. The system implements a multi-dimensional organizational structure with phases, areas, features, and tasks to manage development work efficiently.

## Available Commands

### Core Development Commands
These are slash commands that execute specialized Claude prompts:

- `/project:01_brainstorm-feature {idea}` - Interactive ideation and requirements analysis (Step 1)
- `/project:02_feature-proposal` - Create formal feature proposals (Step 2) 
- `/project:03_feature-to-prd` - Expand proposals to detailed PRDs (Step 3)
- `/project:04_feature-planning` - Break features down into implementation tasks (Step 4)
- `/project:05_implement {mode} {task-id}` - Execute tasks with guidance
  - Modes: `typescript`, `ui`, `mcp`, `cli`, `devops`
- `/project:implement-next {feature-id}` - Auto-find and implement next task in feature
- `/project:review` - Review project state

### Support Commands
- `/project:analyze-issue` - Analyze and break down issues
- `/project:bug-fix` - Structured bug fixing approach
- `/project:code-review` - Code review assistance
- `/project:refactor-code` - Refactoring guidance
- `/project:commit` - Generate commit messages
- `/project:create-pr` - Create pull requests

## Architecture Overview

### Organizational Structure
The system uses a 4-level hierarchy:

1. **Project Level**: Phases (releases, initiatives, milestones like `v1`, `v1.1`, `backlog`)
2. **Delivery Level**: Features (user capabilities grouped under `FEATURE_` prefixes)
3. **System Level**: Areas (functional domains: `cli`, `mcp`, `ui`, `core`, `docs`, `devops`, `orchestrator`)
4. **Work Level**: Tasks (atomic units with statuses and sequential todo lists)

### File Organization
```
.tasks/
├── backlog/              # Future work
├── v1/                   # Current release  
├── v1.1/                 # Next release
└── {phase}/
    ├── FEATURE_{name}/   # Feature groupings
    │   ├── _overview.md  # Feature scope for this phase
    │   └── TASK-*.md     # Individual tasks
    └── AREA_{domain}/    # Area-specific tasks
        └── TASK-*.md
```

### Key Concepts
- **Phases**: Project milestones that organize work by release/initiative
- **Features**: User-facing capabilities that span multiple areas  
- **Areas**: Technical domains (`cli`, `mcp`, `ui`, `core`, `docs`, `devops`, `orchestrator`)
- **Tasks**: Atomic work units with sequential todo lists, serving as both work units and communication logs
- **Statuses**: Task lifecycle stages (`planning`, `implementation`, `testing`, `review`, `deployment`, `done`, `maintenance`)

### Task Structure
Tasks contain sequential todo lists for AI execution:
```markdown
## Todo List
- [ ] 1. Design authentication schema
- [ ] 2. Implement database models  
- [ ] 3. Create API endpoints
- [ ] 4. Write tests
```

## Development Workflow

### Standard Feature Development
1. Start with `/project:01_brainstorm-feature` for ideation
2. Use `/project:02_feature-proposal` to formalize requirements
3. Expand with `/project:03_feature-to-prd` for detailed specifications
4. Plan implementation with `/project:04_feature-planning`
5. Execute with `/project:05_implement {mode} {task-id}`

### Task Execution
- Tasks are sized to fit within single AI sessions
- Todo lists provide structure for complex work
- Tasks serve dual purpose as work units and persistent logs
- Use appropriate area assignment based on technical domain

### Areas and Responsibilities
- **`cli`**: Command-line interface functionality
- **`mcp`**: MCP server and AI integration  
- **`ui`**: Web interface (React components)
- **`core`**: Core task management logic
- **`docs`**: Documentation and guides
- **`devops`**: Build, deployment, testing infrastructure
- **`orchestrator`**: Work coordination and parallel task management

## Command Resources

The system includes extensive planning templates and guides:
- Brainstorming guides in `/docs/command-resources/planning-templates/`
- Implementation mode guides in `/docs/command-resources/implement-modes/`
- Organizational structure guide in `/docs/organizational-structure-guide.md`

## Important Notes

- Features and areas duplicate across phases in the file system
- Each phase maintains its own `_overview.md` files
- Tasks use timestamp-based IDs: `TASK-YYYYMMDD-HHMMSS`
- The orchestrator area enables parallel work coordination
- All commands are designed for critical thinking and challenging assumptions
- Focus on WHAT needs to be solved rather than HOW to implement it during planning phases