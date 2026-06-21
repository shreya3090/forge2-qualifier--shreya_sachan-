# OpenClaw React Board

A multi-project Kanban board featuring an integrated file browser and context storage, designed specifically for Clawdbot/OpenClaw agent workflows.

## Features

- Multi-Project Support – Manage multiple projects, each with its own Kanban board.
- Kanban Board – Four columns: To Do, In Progress, Review, and Done.
- File Browser – Integrated file explorer with syntax highlighting.
- Context Storage – Centralized storage for workspace configuration files (AGENTS.md, SOUL.md, etc.).
- Activity Log – Chronological view of all project activities.
- Agent Status Tracking – Tracks Available/Busy status based on active tasks.
- Dark Theme – GitHub-inspired user interface.
- Markdown Support – Preview and view Markdown files directly within the application.

## Quick Start

### Installation

```bash
git clone https://github.com/AlexPEClub/openclaw_react_board.git
cd openclaw_react_board
npm install
npm start
```

The board will then run on ...: http://localhost:3000

### Clawdbot/openclaw Agent Installation

Give this prompt to your agent:

```
cd ~/.openclaw/workspace
git clone https://github.com/AlexPEClub/openclaw_react_board.git kanban
cd kanban && npm install && ./update-projects.js
OPENCLAW_WORKSPACE=$(cd .. && pwd) npm start
```

You can find detailed setup prompts in `SETUP_PROMPT.md`.

### Docker

```bash
# Mit Docker Compose
docker-compose up

# Oder direkt mit Docker
docker build -t openclaw-kanban .
docker run -p 3000:3000 -v $(pwd)/data:/app/data openclaw-kanban
```

## Configuration

### Environment Variables

```bash
PORT=3000                                    # Server Port (default: 3000)
OPENCLAW_WORKSPACE=/data/.openclaw/workspace # Context-Files Pfad (default: /data/.openclaw/workspace)
```

### Context Files

The server loads the following workspace files from the path configured via OPENCLAW_WORKSPACE:

| File           | Description              |
| -------------- | ------------------------ |
| `MEMORY.md`    | Long-term memory & notes |
| `AGENTS.md`    | Agent configuration      |
| `SOUL.md`      | Personality & behavior   |
| `USER.md`      | User information         |
| `TOOLS.md`     | Tool documentation       |
| `IDENTITY.md`  | Identity                 |
| `HEARTBEAT.md` | Periodic tasks           |


If the default path is not suitable, it can be overridden using an environment variable:

```bash
OPENCLAW_WORKSPACE=/custom/path PORT=3000 node app.js
```

## Project-Structure

### Expected Workspace Structure

```
~/.openclaw/workspace/       # Default OpenClaw workspace
├── kanban/                  # The Kanban board
├── projects/                # Your projects (optional)
│   ├── my-project/
│   │   ├── features/        # Feature specifications
│   │   ├── docs/            # Documentation
│   │   └── src/             # Source code
│   └── another-project/
├── AGENTS.md                # Bootstrap file (automatically injected)
├── SOUL.md                  # Bootstrap file (automatically injected)
├── MEMORY.md                # Long-term memory
├── IDENTITY.md              # Bootstrap file (automatically injected)
├── USER.md                  # Bootstrap file (automatically injected)
├── HEARTBEAT.md             # Bootstrap file (automatically injected)
└── TOOLS.md                 # Bootstrap file (automatically injected)
```

### Data Files

- `tasks.json` - All Projects and Tasks
- `activity.json` - Activity Log
- `agent-status.json` - Agent-Status (Available/Busy)

### Project-Schema (tasks.json)

```json
{
  "id": "proj-xxx",
  "name": "Project Name",
  "description": "Description",
  "projectPath": "/home/node/clawd/projects/my-project",
  "tasks": [
    {
      "id": "PROJ-1",
      "title": "Feature Name",
      "featureFile": "PROJ-1-feature-name.md",
      "status": "todo|in-progress|review|done",
      "priority": "high|medium|low"
    }
  ]
}
```

**Important:** projectPath must always be an absolute path for the File Browser to work correctly.

### Link Feature Specifications

Store feature specifications in the project's features/ directory and link them to the task using the featureFile field.

```
/projects/my-project/features/PROJ-1-user-auth.md
```

Naming Convention: PROJ-{number}-{feature-name}.md

## API Endpoints

```bash
Projects
GET    /api/projects              # Retrieve all projects
POST   /api/projects              # Create a new project
GET    /api/projects/:id          # Retrieve a single project

Tasks
POST   /api/projects/:id/tasks    # Add a task
PUT    /api/tasks/:id             # Update a task (e.g., change status)

Context & Files
GET    /api/context-files         # List context files
GET    /api/files/:projectId/*    # File Browser API

Activity
GET    /api/activity              # Activity Log
```

### Examples

```bash
Create a Project with projectPath
curl -X POST http://localhost:3000/api/projects \
  -H "Content-Type: application/json" \
  -d '{
    "name": "My Project",
    "description": "Project description",
    "projectPath": "/home/node/clawd/projects/my-project"
  }'

Change a Task Status
curl -X PUT http://localhost:3000/api/tasks/PROJ-1 \
  -H "Content-Type: application/json" \
  -d '{"status": "in-progress"}'

Create a Task with a Feature File
curl -X POST http://localhost:3000/api/projects/{projectId}/tasks \
  -H "Content-Type: application/json" \
  -d '{
    "title": "User Authentication",
    "featureFile": "PROJ-1-user-auth.md",
    "status": "todo",
    "priority": "high"
  }'
```

## Troubleshooting

**Server not starting?**
```bash
# Check if port 3000 is in use
lsof -i :3000

# Terminate the process if the port is occupied
kill -9 <PID>
```

**File Browser not showing any files?**
- Verify the `projectPath` value in `tasks.json` — it must be an absolute path.
- Run `update-projects.js` to add or correct project paths after the fact.

**Context Files Missing?**
- Ensure the files are located in the path specified by `OPENCLAW_WORKSPACE`.
- Check by running: `curl http://localhost:3000/api/context-files`

**Status not updating?**
- Ensure that `agent-status.json` is located in the `kanban` folder and has write permissions.

## Contributing

Contributions are welcome! See CONTRIBUTING.md for details.

## License

MIT License — see LICENSE.

---

Developed for the OpenClaw community.
