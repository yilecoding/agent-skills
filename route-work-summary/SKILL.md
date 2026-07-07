---
name: route-work-summary
description: Route information into the correct Obsidian knowledge vault location, including project summaries, account/password notes, life notes, reusable knowledge, inbox items, and archives. Use when the user asks to summarize recent work,整理项目,归档上下文,同步工作记录,记录账号密码,整理资料, or decide where notes should live in the user's knowledge vault.
---

# Route Work Summary

## Overview

Route information into the right place in the knowledge vault, using evidence from the active codebase, the user request, and the vault structure rather than guessing from an old project name.

First locate the vault root, then use vault-relative paths. Before writing there, read its `AGENTS.md`; for project content, also read the target project `README.md`.

## Vault Discovery

Locate the knowledge vault in this order:

1. Use an explicit path from the user if provided.
2. Use environment variables if set: `KNOWLEDGE_VAULT`, `KNOWLEDGE_VAULT_PATH`, or `OBSIDIAN_VAULT`.
3. If the current working directory is inside a vault, walk upward until finding `AGENTS.md` plus the standard directories `00_System/`, `01_Projects/`, `02_Life/`, and `03_Knowledge/`.
4. Check common local paths such as `$HOME/code/knowledge`, `$HOME/Code/knowledge`, and `$HOME/Documents/knowledge`.
5. If no vault is found, ask the user for the vault path before writing.

Once the vault root is found, assume the internal structure is stable and use relative paths such as `01_Projects/HNHG-Platform/README.md`.

## Workflow

1. Identify the information type.
   - Is it project work, reusable technical knowledge, life/admin information, account or credential context, a journal entry, an archive, or an unsorted inbox item?
   - For accounts/passwords, decide sensitivity before writing any value.
2. Identify the real work source when project-related.
   - Prefer explicit user paths, current working directory, git remotes, package names, planning files, and branch names.
   - Do not let an old note title overrule the active repo. Example: `/Users/ita/code/yile/hnhg-platform` maps to `01_Projects/HNHG-Platform/`, while redwood meeting notes are only a subtopic.
3. Find or create the target location.
   - Search `01_Projects/` by repo name, customer prefix, aliases, and key modules.
   - If creating a project directory, use a short English name with uppercase customer prefix, aligned to the repo/product code: `HNHG-Platform`, `AVC-PIS-Agent`.
   - Put the Chinese full name in the README body, not in the directory name.
4. Gather evidence in this order:
   - Current conversation and user-provided paths.
   - Recent code repo files: `AGENTS.md`, README/docs, `.planning/`, `.trellis/tasks/`, git status, recent commits, branch name.
   - Existing vault project README, logs, decisions, pitfalls, and architecture docs.
   - `02_Life/Home.md` for personal/life information.
   - Reusable knowledge notes under `03_Knowledge/` when the lesson is cross-project.
5. Route each piece of information to the smallest correct file.
6. Write concise updates with dates and internal wikilinks.
7. Validate links and formatting before reporting back.

## Routing Rules

Use the vault's lifecycle structure:

| Summary content | Destination |
| --- | --- |
| Stable project identity, current status, key work lines, main entry links | project `README.md` |
| Goals, scope, product boundaries, what is in/out | `目标与需求.md` |
| Environment setup, architecture, interfaces, deployment, integration details | `架构设计.md` or `架构设计/<topic>.md` |
| Dated progress, "what happened recently", next actions | `开发日志.md` or `开发日志/YYYY-MM.md` |
| Troubleshooting and reusable project-specific pitfalls | `问题与经验.md` |
| Choices with tradeoffs and future impact | `决策记录.md` |
| Version delivery and change records | `发布记录.md` |
| General lesson that applies outside this project | `03_Knowledge/<domain>/<note>.md`, with a pointer from the project note |
| Personal finance, shopping, learning, health, admin | `02_Life/<topic>.md` |
| Non-core accounts, document accounts, low-sensitivity credential context | `02_Life/Accounts.md` or the owning project document |
| Core secrets, production credentials, financial passwords, tokens | Do not write values into the vault; record only location/owner with `[已脱敏]` |
| Personal daily journal | `04_Journal/YYYY-MM-DD.md` |
| Unclassified material | `00_System/Inbox.md` with a note explaining why it is pending |
| Inactive material kept for history | `99_Archive/` |
| Drawing files | `Excalidraw/` |

If one lifecycle category grows beyond one note, convert it to a same-name directory following the vault rules.

## Writing Rules

- Follow `<vault-root>/AGENTS.md`.
- Do not use level-1 headings; filenames are titles.
- Every new Markdown note needs frontmatter with `type`, `created`, `updated`, and `tags`.
- Use Obsidian wikilinks with repo-root paths: `[[01_Projects/HNHG-Platform/README.md|HNHG-Platform]]`.
- Keep project README as an entry point. Do not dump detailed logs, API specs, or long troubleshooting into it.
- Prefer appending dated sections over rewriting history, unless the user explicitly asks to reorganize.
- Preserve secrets by redacting or omitting passwords, tokens, production keys, and sensitive account values.
- When evidence conflicts, mention the conflict in the summary or decision note rather than silently choosing.

## Sensitive Information

- Treat production accounts, API tokens, SSH keys, private keys, finance passwords, and personal identity secrets as core secrets: never write the value into the vault.
- For low-sensitivity or document accounts, prefer recording purpose, URL, owner, recovery method, and storage location. Use `[已脱敏]` for the password/key field.
- If the user explicitly provides a password, do not repeat it back unless necessary; ask whether to store the redacted context or the exact value only when the vault policy allows it.
- If the account belongs to a project system, store the credential context in the project architecture/ops note instead of `02_Life/Accounts.md`, unless it is personal rather than project-owned.

## Project Naming

Project directory names are short English identifiers:

- Good: `HNHG-Platform`, `AVC-PIS-Agent`, `AVC-SBS`
- Avoid: `HNHG-海钢综合业务平台`, `HNHG-红杉会议3.0`, other long Chinese project directory names

Use Chinese freely inside note content and filenames for lifecycle notes, but keep top-level project directory names stable and codebase-aligned.

## Validation

Before finishing:

- Check for old/broken project links after renames with `rg`.
- Check target project wikilinks resolve when practical.
- Check no body level-1 headings were introduced.
- Run `rg -n '[ \t]+$' <changed-files>` for trailing whitespace.
- Summarize changed files and any evidence gaps.
