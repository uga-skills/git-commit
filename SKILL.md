---
name: git-commit
description: ステージング済みの変更（git diff --cached）を解析してコミットを実行する。失敗した場合はそのまま実行できるコミットコマンドを提案する。ユーザーが「コミットして」「commitして」「これでコミットしたい」など、ステージング済みの変更のコミットを望んでいる場合は必ずこのスキルを使う。
---

# Skill: git-commit

## Rules (never violate)

- Never run `git add`. Forbidden under any circumstance.
- Never use `--no-verify`, `-n`, `--no-gpg-sign`, or `-c commit.gpgsign=false`. Fix root causes instead.
- If staging area is empty: respond only "No staged changes" and exit immediately. No other action.
- Never modify git/gpg config while investigating a failure. `git config <key> <value>` WRITES; giving two keys (`git config user.signingkey gpg.program`) sets the first to the second's text and corrupts `.git/config`. Read only with `git config --get <key>` (one key per call) or `git config --show-origin --get-regexp '<pattern>'`. If you cannot tell whether a command reads or writes, do not run it.
- Never claim a root cause you have not confirmed. Separate "confirmed" from "hypothesis" in the reply.

## Steps

1. Run `git diff --cached --stat` — if empty, exit with "No staged changes"
2. Run `git diff --cached` to get full diff
3. Draft commit message from diff
4. Run `git commit -m "..."`
5. If success: report and exit
6. If failure:
   - GPG error: investigate with read-only commands only (see Rules).
     - Check `~/.gnupg/gpg-agent.conf` for cache settings; if missing, suggest (do not run it yourself):
       ```bash
       cat >> ~/.gnupg/gpg-agent.conf << 'EOF'
       default-cache-ttl 3600
       max-cache-ttl 86400
       EOF
       gpgconf --kill gpg-agent
       ```
     - To inspect signing config, use `git config --show-origin --get-regexp '^(user\.signingkey|gpg\.|commit\.gpgsign)'`. Add `gpg --list-secret-keys --keyid-format long` when the key itself is in question.
     - Missing cache settings only explain a passphrase-cache miss, not errors like `No such file or directory`. Do not present gpg-agent as the cause unless the evidence shows it; otherwise say the cause is unidentified.
     - If a config change is needed, tell the user first and let them run it.
   - Return ready-to-run commit command in a code block

## Commit message

- Subject: English imperative ("Fix", "Add", "Update", "Remove", "Refactor"), max 72 chars
- Body: bullet list of what changed (omit if obvious from diff)

## Output

- Success: one-line confirmation
- Failure: command in code block, minimal prose. State confirmed facts and unconfirmed hypotheses separately; if the cause is unknown, say so.
