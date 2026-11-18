# dotfiles

Personal dotfiles managed with [chezmoi](https://www.chezmoi.io/), plus configuration for the agent tools I use day to day.

## What it configures

| Path | Contents |
| --- | --- |
| `dot_zshrc` | Zsh with Oh My Zsh, the `powerlevel10k` theme, `zsh-syntax-highlighting` and `zsh-autosuggestions`; `eza` aliased to `ls`; Linuxbrew, Bun, OpenCode and `~/.local/bin` on `PATH` |
| `dot_config/opencode/` | OpenCode configuration: agent definitions (`Planner`, `Builder`, `Reviewer`), commands, skills, local model providers, MCP servers and shell permission prompts |
| `dot_config/private_openchamber/` | OpenChamber preferences and an age-encrypted settings template |
| `dot_pi/agent/` | Settings for the `pi` agent, including its model packages and skills |

The `dot_`/`private_` prefixes and `.tmpl` suffix are chezmoi's naming convention: they map to `~/.zshrc`, `~/.config/...` and so on.

## Getting started

```bash
chezmoi init --apply https://github.com/smuglilgoat/dotfiles.git
```

Prerequisites implied by the files: Zsh, Oh My Zsh, Powerlevel10k, `eza`, and Linuxbrew for the Homebrew `shellenv` line. The `private_openchamber` template is age-encrypted; applying it requires the matching age key.

## Notes

- `dot_config/opencode/AGENTS.md` holds the working agreement shared by the agents; agent-specific instructions live in `dot_config/opencode/agents/`.
- `dot_config/opencode/skills/` contains the skills those agents load, including project-specific ones.
