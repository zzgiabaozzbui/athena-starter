# Contributing

Issues and pull requests are welcome: a rule that is too strict, a skill that misfires, a hook that blocks something it should not.

- **Bug or misfire:** open an issue with the request you gave, what the agent did, and what you expected.
- **Questions and ideas:** use [Discussions](https://github.com/zzgiabaozzbui/athena-starter/discussions).
- **Pull requests:** one change per PR. Skills live in `skills/<name>/SKILL.md`, agents in `agents/`, hooks in `hooks/` (each with a `.selftest.mjs`).

Before opening a PR, run the same checks as CI (Node 18+):

```
node scripts/check.mjs
for t in hooks/*.selftest.mjs; do node "$t"; done
```
