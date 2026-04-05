# Phase: SCAN — Find Everything

## The One Rule

**Scan wide, then scan deep. The stuff they forgot about is more valuable than the stuff they know.**

## What To Scan

Dispatch an Explore agent (subagent_type=Explore, thoroughness="very thorough") with the domains identified in triage. Give it this target list for relevant domains:

### Domain: Commands & Aliases
- Shell rc files: ~/.zshrc, ~/.bashrc, ~/.zsh_aliases, ~/.bash_aliases
- Sourced files: grep for `source` or `.` in rc files, follow the chain
- ~/bin/ scripts: read first 3-5 lines of each for purpose comments
- Shell functions: grep for `function ` and `() {` in rc files

### Domain: Configs & State
- ~/.claude/ — settings, skills, hooks, plugins, MCP configs
- Project CLAUDE.md files in current directory tree
- MEMORY.md and memory files
- Any JSON/YAML config in the project directory
- switcher-backends.json, machines.json, launcher-config.json

### Domain: Running Systems
- tmux sessions: names, what's in each pane (capture-pane)
- systemd user services: systemctl --user list-units --state=active
- Background processes: ps aux for AI-related processes
- MCP servers: what's loaded in current session
- Unix sockets: ls $XDG_RUNTIME_DIR/*.sock

### Domain: Projects & Code
- Git repos in working directory: remotes, recent commits
- Package files: package.json, pyproject.toml, Cargo.toml
- Symlinks in ~/bin/: where they point (readlink -f)
- Cross-references: grep for mentions of discovered tools in other tools

### Domain: Network & Services
- Tailscale status: tailscale status
- SSH config: ~/.ssh/config hosts
- Listening ports: ss -tlnp
- Docker containers if running

## Agent Contract

```
AGENT: system-scanner (Explore)
INPUT:  List of domains to scan (from triage), machine context
OUTPUT: Raw inventory — for each item found:
        - name
        - type (alias/script/config/service/session/process)
        - location (file path or process)
        - one-liner purpose (from comments, naming, or content)
        - connections (what it references, what references it)
VERIFY: Inventory has entries. Each domain requested has results.
FAIL:   If agent times out, fall back to inline scanning of top-priority domain only.
```

## Cross-Reference Pass

After the scan returns, grep for cross-references:
- For each script/config found, search for its name in other files
- For each alias found, check if it shadows a ~/bin/ script
- For each config file, check what reads it
- Build a "references" list per item — this reveals hidden connections

## Evidence Gate

**Gate: Scan inventory exists with items from every requested domain. Cross-references identified.**
