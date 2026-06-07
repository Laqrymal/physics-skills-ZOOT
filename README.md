# ZOOT

ZOOT sets up a small research vault for Codex projects.

It gives a project durable places for raw sources, wiki pages, working notes, code experiments, outputs, figures, docs, and a newest-first process log. It also adds a project-local Codex prompt hook that reminds future agents when to read vault context.

## Use

Ask for ZOOT explicitly:

- "Use ZOOT to set up this project."
- "Run the ZOOT skill here."
- "Set up a research vault."

And then your agent will ask you 5 calibration questions to calibrate the style of the project. After that it will start setting up the vault.

## Generated Layout

ZOOT creates this structure in the target project:

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

Use `.gitkeep` files for empty directories that git needs to track.

## Prompt Hook

ZOOT creates a project-local `UserPromptSubmit` hook.

The hook:

- emits a compact reminder inside the project;
- emits nothing outside the project root;
- reads only `hooks/user-prompt-submit-reminder.txt`;
- runs only the generated `.codex/hooks/vault_context_reminder.py`;
- leaves global Codex configuration alone.

The hook does not read notes, summarize the process log, call the network, write files, initialize git, commit changes, or edit global Codex config.

Codex may ask the user to trust the project-local hook.

### Emitted Context

Inside the project, the hook emits this `additionalContext` block:

```text
<vault-context-reminder>
Project root: <PROJECT_ROOT>

Before answering in this project:
1. Classify the prompt: standalone direct answer, project-continuity task, or durable vault update.
2. If project continuity matters, read newest relevant entries of [[wiki/process-log]] and [[wiki/index]] before answering.
3. If durable knowledge changes, update the relevant wiki/note pages and prepend a compact entry to [[wiki/process-log]].
4. Prefer links/backlinks over long summaries.
5. If the task involves Markdown files, use the Obsidian Markdown skill for detailed formatting/linking instructions when available.
6. If the prompt belongs to an existing research process, identify its process ID from recent [[wiki/process-log]] entries or [[wiki/index]] before continuing; when starting a clearly new research process, assign the next simple process ID and mention it in the note and process log.

Do not read the whole process log by default. Read the newest relevant entries first, then follow links outward only when continuity matters.
</vault-context-reminder>
```

Outside the project, the hook emits nothing.

## Git Behavior

ZOOT leaves git alone unless the user confirms a git action.

For baseline pinning, use these names:

```text
baseline/vault-system-ready-YYYY-MM-DD
vault-system-ready-YYYY-MM-DD
```

If the target repo has existing changes, show `git status` and ask what to include before committing.

## Before Publishing a Generated Vault

Check generated vaults for private notes, raw sources, credentials, local paths, and project-specific assumptions before publishing them.

This source bundle excludes macOS Finder metadata such as `.DS_Store` and AppleDouble `._*` files. The included `.gitignore` keeps them out of later commits.

## License

BRIT. See `LICENSE`.
