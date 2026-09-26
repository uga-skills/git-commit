---
name: git-commit
description: ステージング済みの変更（git diff --cached）を解析してコミットを実行する。失敗した場合はそのまま実行できるコミットコマンドを提案する。ユーザーが「コミットして」「commitして」「これでコミットしたい」など、ステージング済みの変更のコミットを望んでいる場合は必ずこのスキルを使う。
---

# Skill: git-commit

## Rules (never violate)

- Never run `git add`. Forbidden under any circumstance.
- Never use `--no-verify`, `-n`, `--no-gpg-sign`, or `-c commit.gpgsign=false`.
- If staging area is empty: respond only "No staged changes" and exit immediately. No other action.
- If `git commit` fails (GPG, hook, anything): stop immediately. Run no further command — no investigation, no config check, no retry, no fix. Return the command per Output and exit.

## Steps

1. Run `git diff --cached --stat` — if empty, exit with "No staged changes"
2. Run `git diff --cached` to get full diff
3. Draft commit message from diff
4. Run `git commit -m "..."`
5. Success: report and exit. Failure: stop per Rules.

## Commit message

- Subject: English imperative ("Fix", "Add", "Update", "Remove", "Refactor"), max 72 chars
- Body: bullet list of what changed (omit if obvious from diff)

## Output

- Success: one-line confirmation
- Failure: the error's first line, then the ready-to-run commit command in a code block. Do not guess the cause.
