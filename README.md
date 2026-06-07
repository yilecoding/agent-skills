# Agent Skills

This repository stores personal Codex skills that can be copied or cloned across projects and machines.

## Skills

- `commit/`: generates standardized Chinese Git commit messages from staged changes and runs `git commit`.

## Usage

Each skill is a directory containing a `SKILL.md` file. To use these skills on another machine or project, copy the skill directory into your Codex skills directory, for example:

```sh
cp -R commit ~/.codex/skills/
```

After copying, restart Codex so the skill registry can discover the new `SKILL.md`.
