# agent-skills

A collection of custom skills for Claude Code and other AI coding agents.

## Skills

### [ml-interview-prep](ml-interview-prep/SKILL.md)

End-to-end AI/ML interview preparation for practitioners. Given a job description and your background, it produces a gap analysis, personalized study plan, theory Q&A bank, hands-on coding exercises, mock interviews, system design walkthroughs, and optional shareable artifacts (Streamlit dashboard, Jupyter notebook, or one-page summary).

Best for: preparing for MLE, Research Scientist, or Applied Scientist roles at any level (IC3 through Staff/Principal).

### [website-design](website-design/SKILL.md)

Design and style guidelines for personal portfolio websites. Covers layout, typography, color theming, scroll animations, and common pitfalls — for two stacks: **Bootstrap 3 (static HTML/CSS)** and **Next.js + Tailwind + TypeScript**.

Best for: building or overhauling a personal site, with specific rules for GitHub Pages deployment.

---

## How to use skills in Claude Code

Skills are markdown files that tell Claude how to approach a specific task. Claude Code picks them up automatically from two locations:

| Location | Scope |
|---|---|
| `~/.claude/skills/` | Available in every project |
| `.claude/skills/` | Available only in the current project |

### Installation

Copy the skill file into your skills directory:

```bash
# Global (all projects)
cp ml-interview-prep/SKILL.md ~/.claude/skills/ml-interview-prep.md
cp website-design/SKILL.md ~/.claude/skills/website-design.md

# Or project-local
mkdir -p .claude/skills
cp ml-interview-prep/SKILL.md .claude/skills/ml-interview-prep.md
```

### Invoking a skill

Type the skill name as a slash command in the Claude Code chat:

```
/ml-interview-prep
/website-design
```

Claude will load the skill and follow its instructions for the rest of the conversation. You can pass context inline:

```
/ml-interview-prep I have a Staff MLE interview at a search company next week
/website-design update my portfolio site to use a terracotta accent color
```

### Skill file format

Each skill is a `.md` file with a YAML frontmatter block:

```markdown
---
name: my-skill
description: One sentence describing when to use this skill.
---

# Instructions for Claude...
```

The `description` field is shown in the skill picker and used by Claude to decide when to auto-trigger the skill.

---

## Using these skills in other AI coding agents

The skill files are plain markdown — they work as system prompt additions or context files in any agent that accepts custom instructions.

**Cursor / Windsurf:** paste the skill content into your project's `.cursorrules` or the agent's system prompt field.

**GitHub Copilot Chat:** add the skill content to a `copilot-instructions.md` file in `.github/`.

**OpenAI Assistants / custom agents:** include the skill content in the system message when creating the assistant or starting a thread.

**Claude API:** prepend the skill content to the `system` parameter of your API call.
