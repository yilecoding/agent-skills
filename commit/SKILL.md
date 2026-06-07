---
name: commit
description: Generate standardized Git commit messages from staged changes and run git commit. Use when the user asks to commit code, generate a commit message, amend the last commit, or invoke $commit with optional arguments such as --all, --amend, --confirm, or extra context.
---

# Commit

Analyze the staged Git changes, produce a project-appropriate Chinese commit message, and commit automatically after safety checks. Ask for confirmation only when `--confirm` is present or when the staged changes are risky or ambiguous.

## Arguments

Interpret `$ARGUMENTS` as optional flags plus free-form context:

- No argument: analyze currently staged changes and commit.
- `--all`: run `git add -A` before analysis.
- `--amend`: update the previous commit message with `git commit --amend`.
- `--confirm`: show the generated message and wait for explicit confirmation before committing.
- Free text: treat as extra context for message generation.

`--all`, `--amend`, and `--confirm` may be combined when the user clearly requests them.

## Workflow

1. Run `git status --short` to inspect the worktree.
2. If `--all` is present, run `git add -A`, then inspect status again.
3. If no files are staged, stop and ask the user to stage files or rerun with `--all`.
4. Check staged paths for secrets or risky files before reading the full diff. Warn and stop if staged files include `.env`, private keys, credential files, tokens, or obvious generated secret material.
5. Read staged changes:
   - `git diff --cached --stat`
   - `git diff --cached`
6. Read recent context with `git log --oneline -5`.
7. If `.gitlab/commit_message_template.md` exists, read it and prefer its local rules when they do not conflict with this skill.
8. Classify the change type. If staged changes mix unrelated work, tell the user the split you recommend and ask whether to continue.
9. Generate a Chinese commit message:
   - Title format: `<type>: <一句话说明>`
   - Title length: keep under 50 characters when possible.
   - Focus on the result, not the editing action.
   - Use the body for meaningful feature, bug fix, and refactor commits.
10. If `--confirm` is present, show the exact generated commit message to the user and wait for explicit confirmation before committing. Otherwise, show the generated message and commit immediately.
11. Run:
   - Normal: `git commit -m "<title>" -m "<body>"`
   - Amend: `git commit --amend -m "<title>" -m "<body>"`
12. Report the commit hash or amendment result.

Do not run `git commit` when safety checks fail, when staged changes appear unrelated and require splitting, or when `--confirm` is present and the user has not confirmed.

## Type Selection

Choose one type:

| Change | Type |
| --- | --- |
| New component, page, API, or user-visible feature | `feat` |
| Bug fix, incorrect behavior, regression, or broken flow | `fix` |
| Rename, module split, extraction, directory move, internal reshaping | `refactor` |
| README, comments, specifications, guides, or documentation only | `docs` |
| Tests added or changed | `test` |
| Dependencies, package scripts, CI, build config, tooling, routine maintenance | `chore` |
| Render reduction, lazy loading, caching, query optimization, speed/memory improvement | `perf` |
| Formatting, whitespace, import ordering, typography, or non-behavioral style cleanup | `style` |

If multiple types appear, choose the dominant user-facing or behavioral intent. If there is no dominant intent, recommend splitting the commit.

## Message Format

For small, obvious commits, use only a title:

```text
fix: 修复登录超时后重复跳转
```

For significant commits, include a body:

```text
<type>: <一句话说明>

描述：<为什么改、改完解决什么问题>

细项：
- <具体改动 1>
- <具体改动 2>

验证：<运行过的测试命令或说明未运行原因>
```

Body rules:

- Keep the message factual and specific to the staged diff.
- Mention key files, modules, or behavior changes in `细项`.
- Fill `验证` truthfully. If no tests were run, state the reason.
- Do not invent tests, issue numbers, reviewers, or business context.

## Safety Checks

- Do not commit secrets, passwords, tokens, certificates, private keys, or local environment files.
- Treat these staged paths as high risk: `.env*`, `*.pem`, `*.key`, `*.p12`, `*.jks`, credential/config dumps, database exports, and files whose names contain `secret`, `token`, `password`, or `credential`.
- If the full diff contains obvious secret values, stop and tell the user what to remove from staging.
- Respect user changes in the worktree. Only stage files automatically when `--all` is present.
