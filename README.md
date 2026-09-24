# coding-skills

Agent skills by [@butaji](https://github.com/butaji), distributed via the open
Agent Skills ecosystem (`npx skills`, skills.sh).

## Skills

| Skill | Description |
| --- | --- |
| [`lisp-mindset`](skills/lisp-mindset/) | Lisp mindset for design and code: everything is data, one representation per fact, derived state, patterns over branches, a composable domain language, effects at the edges. |

## Install

```bash
npx skills add butaji/coding-skills@lisp-mindset -g -y
```

List what the repo exposes:

```bash
npx skills add butaji/coding-skills --list
```

## Adding a skill

Each skill lives in its own directory under `skills/` and must contain a
`SKILL.md` whose YAML frontmatter provides at least `name` and `description`
(the latter is the routing text used for discovery).

```text
skills/
└── <skill-name>/
    ├── SKILL.md
    └── references/   # optional supporting material
```
