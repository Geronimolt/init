# init

Install instructions for four skills/plugins used in a Hermes Agent setup. All four expose `SKILL.md`, so they also work in other agents that load skills from a directory.

## What's here

| Name | Type | Upstream | Pin |
|---|---|---|---|
| attention-kind | skill | https://github.com/alexgreensh/attention-span (`skills/attention-kind/SKILL.md`) | `main` |
| Clever | skill | https://github.com/Geronimolt/Clever (`skills/clever/SKILL.md`) | `master` |
| ponytail | plugin, 6 skills | https://github.com/DietrichGebert/ponytail | `c982cd411abb53323c4baa1baa3c2f020b8d0b08` |
| superpowers | plugin, 15 skills | https://github.com/obra/superpowers | `22f0b463e7a5e579904dd6211a520321035526f7` |

Upstream licenses apply to attention-kind, ponytail, and superpowers.

## Hermes Agent

```bash
# Plugins
hermes plugins install DietrichGebert/ponytail --enable
hermes plugins install obra/superpowers --enable

# Skill from URL
hermes skills install https://raw.githubusercontent.com/alexgreensh/attention-span/main/skills/attention-kind/SKILL.md --yes
hermes skills install https://raw.githubusercontent.com/Geronimolt/Clever/master/skills/clever/SKILL.md --yes

# Auto-load at session start
hermes config set skills.auto_load '["attention-kind","Clever","ponytail:ponytail","superpowers:using-superpowers"]'
```

Verify: `hermes config get skills.auto_load` lists all four. Plugin skills are namespaced (`ponytail:`, `superpowers:`) and must be written that way in `auto_load`.

`obra/superpowers` is not in the Hermes catalog. The installer reports a **CAUTION** scan verdict (mostly `rm -rf` examples in docs, `printenv` and path traversal patterns in tests). Review it before installing; `--force` is the only way past a blocked verdict and should be a deliberate choice.

## Other agents (Claude Code, Codex, OpenCode, etc.)

Every component is a plain skill directory, so copying is enough. Set `SKILLS_DIR` to your agent's skills folder.

```bash
git clone https://github.com/alexgreensh/attention-span.git /tmp/attention-span
git clone https://github.com/Geronimolt/Clever.git /tmp/clever
git clone https://github.com/DietrichGebert/ponytail.git /tmp/ponytail
git clone https://github.com/obra/superpowers.git /tmp/superpowers

# Claude Code: ~/.claude/skills | Codex: ~/.codex/skills | OpenCode: ~/.config/opencode/skills
SKILLS_DIR="$HOME/.claude/skills"
mkdir -p "$SKILLS_DIR"

cp -R /tmp/attention-span/skills/attention-kind "$SKILLS_DIR/"
cp -R /tmp/clever/skills/clever "$SKILLS_DIR/"
cp -R /tmp/ponytail/skills/ponytail* "$SKILLS_DIR/"
cp -R /tmp/superpowers/skills/* "$SKILLS_DIR/"
```

Pin a plugin to the commit in the table above with `git -C <dir> checkout <pin>` if you need reproducibility.

Name collisions: superpowers ships `systematic-debugging`, `test-driven-development`, `requesting-code-review`, and other names that some agents bundle. Decide which copy wins before copying.

For agents without a config file, put the load instruction in `AGENTS.md`, `CLAUDE.md`, or the equivalent project instruction file:

```text
Always load these skills at session start: attention-kind, Clever, ponytail:ponytail, superpowers:using-superpowers.
```

The `owner:skill` spelling is Hermes-specific; for other agents use the directory names (`attention-kind`, `clever`, `ponytail-*`, and the superpowers entry point `using-superpowers`).
