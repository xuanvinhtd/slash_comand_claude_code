## Claude Commands

Scopecraft includes specialized Claude commands for structured development:

### Available Commands

- `/project:01_brainstorm-feature` - Interactive ideation (Step 1)
- `/project:02_feature-proposal` - Create formal proposals (Step 2)
- `/project:03_feature-to-prd` - Expand to detailed PRDs (Step 3)
- `/project:04_feature-planning` - Break down into tasks (Step 4)
- `/project:05_implement {mode} {task-id}` - Execute with guidance
  - Modes: `typescript`, `ui`, `mcp`, `cli`, `devops`
- `/project:review` - Review project state

### Example Workflow

```bash
# Step 1: Start with an idea
/project:01_brainstorm-feature "better task filtering"

# Step 2: Create proposal
/project:02_feature-proposal

# Step 3: Expand to PRD
/project:03_feature-to-prd TASK-20250517-123456

# Step 4: Plan implementation
/project:04_feature-planning FEATURE-20250517-123456

# Execute tasks
/project:05_implement ui TASK-20250517-234567

# Automatically find and implement next task in feature
/project:implement-next FEATURE_auth
/project:implement-next  # Auto-detect from current worktree
```