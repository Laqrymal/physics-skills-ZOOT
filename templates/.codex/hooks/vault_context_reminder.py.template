#!/usr/bin/env python3
"""Inject the research vault retrieval reminder on user prompt submit."""

from __future__ import annotations

import json
import sys
from pathlib import Path


def _read_hook_input() -> dict:
    raw = sys.stdin.read()
    if not raw.strip():
        return {}
    try:
        data = json.loads(raw)
    except json.JSONDecodeError:
        return {}
    return data if isinstance(data, dict) else {}


def _is_relative_to(path: Path, root: Path) -> bool:
    try:
        path.resolve().relative_to(root.resolve())
    except ValueError:
        return False
    return True


def main() -> int:
    hook_input = _read_hook_input()
    repo_root = Path(__file__).resolve().parents[2]

    cwd_value = hook_input.get("cwd")
    cwd = Path(cwd_value).resolve() if isinstance(cwd_value, str) and cwd_value else Path.cwd().resolve()
    if not _is_relative_to(cwd, repo_root):
        return 0

    reminder_path = repo_root / "hooks" / "user-prompt-submit-reminder.txt"
    try:
        reminder = reminder_path.read_text(encoding="utf-8").strip()
    except OSError:
        return 0

    additional_context = (
        "<vault-context-reminder>\n"
        f"Project root: {repo_root}\n\n"
        f"{reminder}\n\n"
        "Do not read the whole process log by default. Read the newest relevant entries first, "
        "then follow links outward only when continuity matters.\n"
        "</vault-context-reminder>"
    )

    payload = {
        "hookSpecificOutput": {
            "hookEventName": "UserPromptSubmit",
            "additionalContext": additional_context,
        }
    }
    print(json.dumps(payload, ensure_ascii=False))
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
