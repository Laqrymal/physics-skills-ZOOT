---
name: zoot
description: Use only when the user explicitly says ZOOT or explicitly asks to use the research vault setup skill to set up a new lightweight Obsidian-first research vault. Do not use for ordinary Markdown, Obsidian, wiki, process-log, research, derivation, coding, hook-maintenance, or summarization tasks.
---

# ZOOT

Set up a lightweight Obsidian-first research vault with project-local Codex memory hooks.

## Activation Policy

Use this skill only when the user explicitly asks for it.

Valid activation examples:

- "Use ZOOT to set up this project."
- "Run the ZOOT skill here."
- "Set up a ZOOT-style research vault."
- "Use the research vault setup skill."

Do not use this skill for ordinary Markdown editing, Obsidian note work, process-log maintenance, wiki maintenance, hook discussion, research, derivation, coding, or summarization inside an existing vault.

## Project Framing

Use project-specific wording from the user's wizard answers. Do not add AI-specific, physics-specific, or other domain-specific framing unless the user asks for it.

## Minimal Wizard

Ask these questions before creating files:

1. What is the project title, purpose, and domain?
2. What user background or explanatory stance should future agents assume?
3. Should the vault be research-heavy, code-heavy, writing-heavy, or balanced?
4. Does the project need a domain-specific explanation style or analogy policy?
5. Should git be left untouched, or should setup commit and baseline pinning happen after explicit confirmation?

Use concise questions. If the user already provided an answer in the prompt, reuse it instead of asking again.

## Generated Structure

Create this structure in the target project:

```text
AGENTS.md
raw/
wiki/
  index.md
  process-log.md
  topics/
  concepts/
  methods/
  papers/
notes/
code/
outputs/
figures/
docs/
hooks/
.codex/
  hooks.json
  hooks/
    vault_context_reminder.py
```

Use `.gitkeep` files where empty directories need to be preserved by git.

## Template Workflow

1. Resolve the target project root from the current working directory unless the user specifies a different path.
2. Inspect existing files before writing:
   - `AGENTS.md`
   - `wiki/index.md`
   - `wiki/process-log.md`
   - `.codex/hooks.json`
   - `.codex/hooks/vault_context_reminder.py`
   - `hooks/user-prompt-submit-reminder.txt`
3. If any target file exists, do not overwrite it silently. Propose a merge or ask the user how to proceed.
4. Fill templates from `templates/` using the wizard answers.
5. Create project-local hook files. Do not edit global Codex config.
6. Verify the generated vault.
7. Ask before git initialization, commit, branch creation, or tag creation.

## Required Template Variables

Use these variables consistently:

```text
{{PROJECT_TITLE}}
{{PROJECT_PURPOSE}}
{{DOMAIN}}
{{USER_BACKGROUND}}
{{VAULT_BALANCE}}
{{EXPLANATION_STYLE}}
{{CREATED_DATE}}
```

Use ISO dates in `YYYY-MM-DD` format.

## Hook Policy

Always create the project-local `UserPromptSubmit` hook files:

- `.codex/hooks.json`
- `.codex/hooks/vault_context_reminder.py`
- `hooks/user-prompt-submit-reminder.txt`

The hook must:

- emit a compact vault-context reminder for prompts inside the project;
- emit nothing outside the project root;
- avoid reading the process log automatically;
- execute only the generated project-local `.codex/hooks/vault_context_reminder.py`;
- avoid global Codex config mutation.

Tell the user that Codex may ask them to trust the project-local hook.

## Git Policy

Do not initialize git, stage, commit, branch, or tag unless the user explicitly confirms.

If the user asks for baseline pinning, use:

```text
baseline/vault-system-ready-YYYY-MM-DD
vault-system-ready-YYYY-MM-DD
```

If the target repo is dirty, show the status and ask what should be included before committing.

## Verification

After setup, run these checks when applicable:

```bash
python3 -m json.tool .codex/hooks.json >/dev/null
python3 - <<'PY'
from pathlib import Path
compile(Path(".codex/hooks/vault_context_reminder.py").read_text(encoding="utf-8"), ".codex/hooks/vault_context_reminder.py", "exec")
PY
printf '{"cwd":"%s"}' "$PWD" | /usr/bin/python3 .codex/hooks/vault_context_reminder.py
printf '{"cwd":"/tmp"}' | /usr/bin/python3 .codex/hooks/vault_context_reminder.py
test -f AGENTS.md
test -f wiki/index.md
test -f wiki/process-log.md
test -f hooks/user-prompt-submit-reminder.txt
```

Expected:

- JSON parsing succeeds.
- Python compilation succeeds.
- In-project hook simulation emits `hookSpecificOutput.additionalContext`.
- Out-of-project hook simulation emits nothing.
- Required files exist.

Report skipped checks explicitly.
