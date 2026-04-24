---
トークン使用量推定値:
  1回あたり: 370
  エンコーディング: "cl100k_base"
---

# git-commit

Claude Code用のコミットスキルです。
ステージング済みの変更を解析してコミットを実行します。失敗した場合のみ、そのまま貼り付けて実行できる`git commit`コマンドを提案します。

## Install

### すべてのプロジェクトで利用する場合

```bash
git clone git@github.com:uga-skills/git-commit.git ~/.claude/skills/git-commit
```

### 特定のプロジェクトでサブモジュールとして使う場合

```bash
git submodule add git@github.com:uga-skills/git-commit.git .claude/skills/git-commit
```

## 使い方

Claude Codeのチャットで`/git-commit`を実行します。

ステージング済みの差分を自動で取得し、以下の形式でコマンドを返します:

```
git commit -m "Fix typo in README"
```

変更が複数の種別にまたがる場合はbody付きで返すこともあります:

```
git commit -m "Refactor auth module

- Extract token validation into separate function
- Remove unused imports"
```

## 注意

コミットに失敗した場合（GPG署名エラーなど）は、手動で実行できるコマンドを返します。
