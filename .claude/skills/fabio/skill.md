# FabIO Service Manager

Start, stop, restart, or check the status of FabIO services: the gateway and/or remote agents. Also syncs Python project dependencies with `project sync`.

## Usage

/fabio gateway start|stop|restart [--detach] [--log-level <LEVEL>]

/fabio agents start|stop|restart [all|<category> <agent-name> ...]
    [--detach]
    [--skip <agent>]
    [--log-level <LEVEL>]

/fabio status [gateway/agents]

/fabio project sync

## Gateway Examples

- /fabio gateway start — start the gateway in the foreground
- /fabio gateway start --detach — start the gateway detached (background, logged to file)
- /fabio gateway stop — stop the gateway
- /fabio gateway restart — stop then start the gateway (foreground)
- /fabio gateway restart --detach — stop then start the gateway detached

## Agents Examples
- /fabio agents start — show agent list and ask which to start
- /fabio agents start all — start every remote subagent (foreground, all in parallel)
- /fabio agents start all --detach — start every remote subagent detached
- /fabio agents start kb — start all KB subagents
- /fabio agents stop operations — stop all operations subagents
- /fabio agents start pipeline_operations_subagent pipeline_admin_subagent
- /fabio agents stop — show agent list and ask which to stop
- /fabio agents start all --skip pipeline_kb_subagent — start all agents except one
- /fabio agents start kb --skip pipeline_kb_subagent,mft_kb_subagent — skip multiple agents
- /fabio agents restart all — restart every remote subagent
- /fabio agents restart kb — restart all KB subagents
- /fabio agents restart all --skip pipeline_kb_subagent — restart all except one
- /fabio agents restart pipeline_operations_subagent — restart one agent

### Status Examples

- `/fabio status` — show status of gateway + all agents
- `/fabio status gateway` — show gateway status only
- `/fabio status agents` — show status of all agents

### Project Sync Example

- `/fabio project sync` — run `uv sync` in every Python project (all `pyproject.toml` directories)

## Flags

| Flag | Applies to | Description |
|---|---|---|
| `--detach` | gateway / agents `start`, `restart` | Run with `nohup`, logs to `logs/<name>.log`. Default: foreground. |
| `--skip <names>` | agents `start`, `stop`, `restart` | Comma-separated list of agent names to exclude. E.g. `--skip pipeline_kb_subagent` or `--skip pipeline_kb_subagent,mft_kb_subagent` |
| `--log-level <LEVEL>` | gateway / agents `start`, `restart` | Set `AGENT_LOG_LEVEL` (`DEBUG`, `INFO`, `WARNING`, `ERROR`). Default: `DEBUG`. E.g. `--log-level INFO` |

## Running from the Terminal

The `/fabio` command is intended for use inside an AI coding assistant (OpenCode, Claude Code). Type it directly in the chat:

```bash
/fabio status
/fabio gateway start
/fabio agents restart kb --detach
/fabio agents stop pipeline_operations_subagent
/fabio agents start all --skip pipeline_kb_subagent
/fabio project sync
```
You can also run /fabio non-interactively from the terminal using opencode run.

```bash
opencode run -m github-copilot/gpt-5-mini --command fabio "status"
opencode run -m github-copilot/gpt-5-mini --command fabio "gateway start"
opencode run -m github-copilot/gpt-5-mini --command fabio "agents restart all --detach"
opencode run -m github-copilot/gpt-5-mini --command fabio "agents start all --skip pipeline_kb_subagent"
opencode run -m github-copilot/gpt-5-mini --command fabio "agents stop all"
opencode run -m github-copilot/gpt-5-mini --command fabio "project sync"
```

Add a shell alias for convenience:

```bash
alias fabio="opencode run -m github-copilot/gpt-5-mini --command fabio"

# then:

fabio "status"
fabio "gateway restart"
fabio "agents restart kb --detach"
fabio "agents start all --skip pipeline_kb_subagent,mft_kb_subagent"
fabio "agents stop all"
fabio "project sync"
```

Or call the underlying script directly, with no AI assistant required:

```bash
bash bin/fabio.sh status
bash bin/fabio.sh gateway start
bash bin/fabio.sh agents restart kb --detach
bash bin/fabio.sh agents start all --skip pipeline_kb_subagent
bash bin/fabio.sh agents stop all
bash bin/fabio.sh project sync
```

## Foreground vs Detached

By default, `start` and `restart` run processes in the foreground — all agents are launched as parallel background jobs within the script, and the script blocks until you `Ctrl-C` (which stops all of them together).

Pass `--detach` to run with `nohup` in the background. Logs go to:

```text
logs/<name>.log
```

In both modes, output is always written to `logs/<name>.log` so startup errors are detectable regardless of how the agent was launched.

# Startup Error Detection

During the port-readiness wait window (up to 60 s), the agent’s output is scanned every 2 s for error patterns. If any are found the agent process is killed immediately and the agent is counted as an error — not a successful start.

## Patterns Detected

- Python exceptions and tracebacks (`Traceback (most recent call last)`, `Exception:`, `Error:`, `ImportError`, `ModuleNotFoundError`, ...)
- HTTP 4xx / 5xx responses (`HTTP 404`, `status: 500`, ...)
- GCS / Cloud Storage access errors (`GoogleCloudError`, `StorageError`, `GCSError`)
- IAM / permission / credential / authentication failures (`PermissionDenied`, `AccessDenied`, `Unauthorized`, `Forbidden`, `IAM.*Denied`, `CredentialError`, ...)
- Connection errors (`ConnectionRefused`, `Connection Reset`, `SocketError`, `TimeoutError`, ...)
- Structured log-level keywords (`ERROR`, `CRITICAL`, `FATAL:`)
- Common failure phrases (`Failed to`, `Unable to`, `Could not`, `Cannot`)

When an error is detected the failing agent is treated as ❌ Errors in the final summary. All other agents continue starting in parallel regardless.

# Instructions

Parse `$ARGUMENTS` as follows:

1. **Extract the service scope** — the first word:
    - `gateway`
    - `agents`
    - `status`
    - `project`

   If missing or `status`, run status for all.

2. **Extract the action** — the second word (within the scope):

    - For `gateway`:
        - `start`
        - `stop`
        - `restart`
        - `status` (default: `status`)

    - For `agents`:
        - `start`
        - `stop`
        - `restart`
        - `status` (default: `status`)

    - For `project`:
        - must be `sync`

    - For bare `status [gateway|agents]`:
        - no action needed — pass the optional filter as-is

3. **For agents, extract the target** from remaining words
   (strip `--detach`, `--skip <value>`):

    - `all` or empty → every agent in the registry
    - category keyword (`kb`, `operations`, `admin`) → agents in that category
    - one or more agent names → those specific agents
    - unrecognized → show the full agent list and ask the user to pick

4. **Detect flags** — pass through any of:
    - `--detach`
    - `--skip <names>`

5. **Execute** by running the appropriate underlying script/command.
```bash
bash bin/fabio.sh <gateway|agents> <start|stop|restart> [target] [--detach] [--skip <names>]
bash bin/fabio.sh status [gateway|agents]
bash bin/fabio.sh project sync
```

6. **Output** — print the raw script output exactly as-is. Do NOT add any summary, commentary, markdown formatting, tables, bullet points, or notes.

# Agent Categories

| Category | Agents | Port(s) |
|---|---|---|
| `kb` | `platform_common_kb_subagent`, `pipeline_kb_subagent` | `11000`, `11200` |
| `operations` | `pipeline_operations_subagent`, `kl_operations_subagent` | `13100`, `11302` |
| `admin` | `pipeline_admin_subagent`, `kl_admin_subagent` | `14100`, `11304` |

> The Angular frontend (`doc-agent-mf`) is excluded — use `npm run start` manually in `doc-agent-mf/`.