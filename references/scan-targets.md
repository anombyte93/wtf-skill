# Scan Targets Reference

Quick-reference for what to scan per domain. Used by the system-scanner agent.

## Commands & Aliases
```bash
grep -n 'alias ' ~/.zshrc ~/.bashrc ~/.zsh_aliases 2>/dev/null
grep -n 'function \|() {' ~/.zshrc ~/.bashrc 2>/dev/null
grep -n '^source \|^\. ' ~/.zshrc ~/.bashrc 2>/dev/null  # follow the chain
ls ~/bin/ | head -50
for f in ~/bin/*; do head -3 "$f" 2>/dev/null; echo "---"; done
```

## Configs
```bash
ls ~/.claude/settings*.json
ls ~/.claude/skills/
ls ~/.claude/hooks/ 2>/dev/null
ls ~/.claude/plugins/cache/claude-plugins-official/ 2>/dev/null
find . -name 'CLAUDE.md' -maxdepth 3 2>/dev/null
cat ~/.claude/projects/*/memory/MEMORY.md 2>/dev/null
find . -name '*.json' -maxdepth 2 -path '*/config/*' 2>/dev/null
```

## Running Systems
```bash
tmux list-sessions -F '#{session_name} #{session_created} #{session_attached}' 2>/dev/null
systemctl --user list-units --state=active --no-pager 2>/dev/null | head -20
ps aux | grep -E 'claude|node|python|gemini|codex' | grep -v grep
ls ${XDG_RUNTIME_DIR:-/tmp}/*.sock 2>/dev/null
```

## Network & Services
```bash
tailscale status --json 2>/dev/null | jq '.Peer | keys' 2>/dev/null
grep 'Host ' ~/.ssh/config 2>/dev/null
ss -tlnp 2>/dev/null | head -20
docker ps --format 'table {{.Names}}\t{{.Status}}' 2>/dev/null | head -10
```

## Cross-References
After collecting inventory, find hidden connections:
```bash
# For each script/alias name found, check what else references it
grep -r "script_name" ~/bin/ ~/.zshrc ~/.claude/ 2>/dev/null
# For each config file, check what reads it
grep -r "config_filename" ~/bin/ ~/.claude/ 2>/dev/null
# Check for shadowed commands
type -a command_name 2>/dev/null  # shows if alias shadows script
```
