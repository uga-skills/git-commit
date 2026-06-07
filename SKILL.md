---
description: ステージング済みの変更を見てコミットを試みる。失敗した場合はコミットメッセージを提案する
---

# Skill: git-commit

## Rules (never violate)

- Never run `git add`. Forbidden under any circumstance.
- Never use `--no-verify`, `-n`, `--no-gpg-sign`, or `-c commit.gpgsign=false`. Fix root causes instead.
- If staging area is empty: respond only "No staged changes" and exit immediately. No other action.

## Steps

1. Run `git diff --cached --stat` — if empty, exit with "No staged changes"
2. Run `git diff --cached` to get full diff
3. Draft commit message from diff
4. Run `git commit -m "..."`
5. If success: report and exit
6. If failure:
   - GPG error: check `~/.gnupg/gpg-agent.conf` for cache settings; if missing, suggest:
     ```bash
     cat >> ~/.gnupg/gpg-agent.conf << 'EOF'
     default-cache-ttl 3600
     max-cache-ttl 86400
     EOF
     gpgconf --kill gpg-agent
     ```
   - Return ready-to-run commit command in a code block

## Commit message

- Subject: English imperative ("Fix", "Add", "Update", "Remove", "Refactor"), max 72 chars
- Body: bullet list of what changed (omit if obvious from diff)

## Output

- Success: one-line confirmation
- Failure: command in code block only, minimal prose
