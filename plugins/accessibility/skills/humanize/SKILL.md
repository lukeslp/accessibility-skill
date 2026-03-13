---
name: humanize
description: Strip robot-sounding language from user-facing content — READMEs, docs, code comments, package descriptions. Applies only to content that will be publicly visible in a repo. Skip internal files, skill definitions, config, and anything not meant for humans outside this dev environment. Also enforces the "AI" terminology ban.
---

# Humanize Skill

Make docs sound like a person wrote them. Kill the corporate robot voice, enforce terminology rules, and keep things casual.

## Scope: What to Humanize (and What Not To)

**Humanize these** — content a repo visitor or package user will actually read:
- `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`
- Inline code comments and docstrings
- Package descriptions (`pyproject.toml`, `package.json`)
- Store listings, release notes, public docs
- Any `.md` in the repo root or `docs/` that ships publicly

**Skip these** — internal files that never leave the dev environment:
- Skill definition files (`SKILL.md`, agent configs)
- Internal notes, session logs, `CLAUDE.md` files
- Test files, build artifacts, generated configs
- Anything in `.claude/`, `.codex/`, `.gemini/` dirs
- Agent cruft files (see cleanup section below)

**Rule of thumb**: if a random person cloning the repo would read it, humanize it. If only you or an agent would read it, skip it.

## Terminology Rules (MANDATORY - applies everywhere)

### The "AI" Ban
The word "AI" is **banned** from all code, docs, READMEs, package descriptions, and public content. No exceptions except third-party proper nouns.

| Never Write | Write Instead |
|-------------|---------------|
| "AI-powered" | "uses a language model to..." or just describe the feature |
| "AI-enhanced" | "LLM-enhanced" or describe what it actually does |
| "AI system" | "LLM", "language model", "model" |
| "AI capabilities" | "LLM capabilities" or be specific |
| "AI-driven" | "LLM-driven" or "model-driven" |
| "powered by AI" | "uses [Grok/Claude/Ollama/etc.]" - name the actual model |
| "artificial intelligence" | "language model" or "machine learning" as appropriate |
| "AI assistant" | just describe what it does |
| "LLM-powered" | describe what it does: "uses a language model to..." |
| "model-powered" | same — describe the actual function |
| "agent-powered" | same — "-powered" as a suffix is always banned |

**Rule**: the `-powered` suffix is banned regardless of what precedes it — "AI-powered", "LLM-powered", "model-powered", all the same problem. Describe what the thing actually does instead.

**Why**: "AI" is a vague marketing buzzword. LLM, language model, or the actual model name tells people what's really happening. Be specific.

**Exception**: Third-party product names are fine as-is (e.g., "DigitalOcean's Gradient AI Platform").

## What Gets Fixed

### Instant Kill (confidence >0.9)
- "In this [section/guide/tutorial]..." → just say the thing
- "It's important to note that..." → delete, say what matters
- "Let's explore..." → nope
- "Please note that..." → cut the hedging
- "AI-powered" / "AI-enhanced" → rewrite per terminology rules above
- Walls of bullet points → paragraphs where it reads better
- Over-structured numbered lists → flowing text

### Likely Robot (confidence 0.7-0.9)
- Overly formal transitions ("Furthermore", "Moreover")
- Redundant clarifications nobody asked for
- Generic placeholder examples
- Template-looking headers
- Passive voice where active reads better

### Maybe, Maybe Not (confidence 0.6-0.7)
- Flagged for review but not auto-fixed
- Could be legit style choices

## Detection Patterns

What gives away robot writing:

1. **Buzzwords**: "delve into", "leverage", "robust", "comprehensive", "streamline", "cutting-edge"
2. **Structure**: Everything in perfectly symmetric lists, numbered everything
3. **Meta-commentary**: "In this section we will discuss..." - just discuss it
4. **Hedging**: "Might", "perhaps", "it's worth noting", "it should be mentioned"
5. **Filler examples**: foo, bar, example1, myProject
6. **Banned terms**: "AI-powered", "AI-enhanced", "AI-driven" (see terminology rules)

## Confidence Levels

| Level | Range | What Happens |
|-------|-------|--------------|
| **High** | 0.9-1.0 | Fixed automatically with `--yes` |
| **Medium** | 0.7-0.9 | Shows suggestion, asks first |
| **Low** | 0.6-0.7 | Just reports in preview mode |

## Examples

### Before / After

```markdown
# BEFORE (robot voice)
In this guide, we'll explore how to install the application.
It's important to note that you'll need Python 3.8 or higher.
Let's dive into the AI-powered installation process.

# AFTER (human voice)
## Installation
Needs Python 3.8+.
```

```markdown
# BEFORE
This AI-powered tool leverages comprehensive machine learning
to deliver robust, cutting-edge document processing capabilities.

# AFTER
Runs your docs through an LLM to extract metadata and rename files.
```

```markdown
# BEFORE
Please note that this feature is currently experimental and
might not work in all cases. It's worth mentioning that...

# AFTER
Experimental - might not work everywhere yet.
```

## Safety

**Preview first**: `humanize README.md --preview` shows what would change without touching anything.

**Backups**: Every edit creates a `.backup` file. Undo with `mv file.md.backup file.md`.

## Supported Files

`.md`, `.html`, `.txt` - won't touch code blocks, inline code, links, or intentional formatting.

## Advanced

Skip sections with markers:
```markdown
<!-- humanize-ignore-start -->
This stays exactly as-is.
<!-- humanize-ignore-end -->
```
