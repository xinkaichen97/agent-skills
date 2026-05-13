# Agent Skills

A collection of custom skills for Claude Code and other AI coding agents, created by me.

## Skills

### [ml-interview-prep](skills/ml-interview-prep/SKILL.md)

End-to-end AI/ML interview preparation for practitioners. Given a job description and your background, it produces a gap analysis, personalized study plan, theory Q&A bank, hands-on coding exercises, mock interviews, system design walkthroughs, and optional shareable artifacts (Streamlit dashboard, Jupyter notebook, or one-page summary).

Best for: preparing for MLE, Research Scientist, or Applied Scientist roles at any level (IC3 through Staff/Principal).

### [website-design](skills/website-design/SKILL.md)

Design and style guidelines for personal portfolio websites. Covers layout, typography, color theming, scroll animations, and common pitfalls — for two stacks: **Bootstrap 3 (static HTML/CSS)** and **Next.js + Tailwind + TypeScript**.

Best for: building or overhauling a personal site, with specific rules for GitHub Pages deployment.

### More to come!

---

## Repo structure

```
agent-skills/
├── .claude-plugin/
│   ├── marketplace.json   # marketplace metadata and plugin registration
│   └── plugin.json        # declares which skill paths to load
├── skills/
│   ├── ml-interview-prep/
│   │   └── SKILL.md
│   └── website-design/
│       └── SKILL.md
└── README.md
```

The `.claude-plugin/` directory is what Claude Code uses to recognize this repo as an installable plugin. Without it, Claude Code treats the repo as a plain GitHub repo and won't surface skills in the `/` picker.

---

## Installing in Claude Code

### Option 1: Install as a plugin (recommended)

This makes the skills appear in the `/` slash command picker under `xinkai-skills:`.

**Step 1** — Register the marketplace in `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "xinkai-skills": {
      "source": {
        "source": "github",
        "repo": "xinkaichen97/agent-skills"
      }
    }
  },
  "enabledPlugins": {
    "xinkai-skills@xinkai-skills": true
  }
}
```

**Step 2** — Restart Claude Code. The skills will appear as:

```
/xinkai-skills:ml-interview-prep
/xinkai-skills:website-design
```

### Option 2: Copy SKILL.md files manually

If you just want to use the skill content without the plugin system:

```bash
# Global (all projects)
mkdir -p ~/.claude/skills
cp skills/ml-interview-prep/SKILL.md ~/.claude/skills/ml-interview-prep.md
cp skills/website-design/SKILL.md ~/.claude/skills/website-design.md
```

Then invoke by typing the skill name as a slash command (no namespace prefix):

```
/ml-interview-prep
/website-design
```

---

## Adding a new skill

1. Create a directory under `skills/`:

```bash
mkdir skills/my-new-skill
```

2. Add a `SKILL.md` with YAML frontmatter:

```markdown
---
name: my-new-skill
description: One sentence describing when to use this skill.
---

# Instructions for Claude...
```

3. Register it in `.claude-plugin/plugin.json`:

```json
{
  "skills": [
    "./skills/ml-interview-prep",
    "./skills/website-design",
    "./skills/my-new-skill"
  ]
}
```

4. Commit and push. Claude Code will pick it up on next restart.

---

## Using these skills in other AI coding agents

The skill files are plain markdown — they work as system prompt additions in any agent that accepts custom instructions.

**Cursor / Windsurf:** paste the skill content into your project's `.cursorrules` or the agent's system prompt field.

**GitHub Copilot Chat:** add the skill content to a `copilot-instructions.md` file in `.github/`.

**OpenAI Assistants / custom agents:** include the skill content in the system message when creating the assistant or starting a thread.

**Claude API:** prepend the skill content to the `system` parameter of your API call.
