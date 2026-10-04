# sp-skills

Agent skills in the open [Agent Skills](https://agentskills.io) format (`SKILL.md`). Each skill is a plain folder, so any agent that reads skills can use it. The repo is also a plugin marketplace that Claude Code and GitHub Copilot CLI can install from directly.

## Skills

| Skill | What it does |
|---|---|
| [organize-docs](plugins/organize-docs/skills/organize-docs/SKILL.md) | Applies a documentation convention to a project's Markdown: YAML frontmatter with controlled `type` and `status` vocabularies, an `index.md` per docs folder, and a `DOC_CONVENTIONS.md` copied into the project. |

## Install

Replace `<repo-url>` with this repository's URL (or `owner/sp-skills` on GitHub).

**Claude Code**

```
/plugin marketplace add <repo-url>
/plugin install organize-docs@sp-skills
```

**GitHub Copilot CLI**

```
copilot plugin marketplace add <repo-url>
copilot plugin install organize-docs@sp-skills
```

**Copilot in VS Code, the Copilot coding agent, Cursor and other agents**

```
npx skills add <repo-url>
```

Or copy the skill folder (for example `plugins/organize-docs/skills/organize-docs/`) by hand:

- into a project: `.github/skills/`, `.agents/skills/` or `.claude/skills/`
- for yourself, across projects: `~/.agents/skills/`, `~/.copilot/skills/` or `~/.claude/skills/`

## Layout

```
.claude-plugin/marketplace.json        the marketplace (read by Claude Code and Copilot CLI)
plugins/<name>/.claude-plugin/plugin.json
plugins/<name>/skills/<name>/SKILL.md  the portable skill folder
```

To add a skill: create `plugins/<name>/` with the same shape and add an entry to `marketplace.json`.

## License

[Apache License 2.0](LICENSE)
