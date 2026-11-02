# Athena Starter

**Your Claude Code agent always knows which workflow it is in, where it is in the plan, and what is still left to do.**

Athena Starter is a Claude Code plugin that routes every request to the right workflow, keeps a live progress table for every multi-step job, and blocks the shortcuts that break real projects. A desktop dashboard shows all of it as a graph, a board and a timeline.

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-d97757.svg)](#quick-start)
[![Latest release](https://img.shields.io/github/v/release/zzgiabaozzbui/athena-starter?label=desktop%20app)](https://github.com/zzgiabaozzbui/athena-starter/releases/latest)
[![check](https://github.com/zzgiabaozzbui/athena-starter/actions/workflows/check.yml/badge.svg)](https://github.com/zzgiabaozzbui/athena-starter/actions/workflows/check.yml)

![Agent Observatory showing the full plan of a 14-task mission](docs/images/observatory-full-plan.png)

*The plan above is the real 14-task plan that built this kit, shown in Agent Observatory: each card is a task with its status, agents and token use; edges are dependencies.*

## Features

### 1. Automatic flow routing

Every new request is matched to one of **30 workflows** before the agent reads or edits anything. The agent restates what you asked, recommends a flow and lists the runners-up, so a misread request is caught in one click instead of ten edited files.

```
You:    Users can see other departments' records on the report screen.
Agent:  Flow: you want users to see only their own department's records. Which flow?
          > Auth / permissions · #12 (Recommended): CRITICAL, you approve the proposal before any edit, live test with an ordinary account
            Bug fix · #6: red test first
            UI change · #15: frontend only
```

Each flow carries its own risk level, so a one-line change to permissions still gets the full process that a one-line typo fix does not.

| Group | Flows |
|---|---|
| **Idea to delivery** | Vague request (clarify scope first) · Large one-sided feature · Feature across backend + frontend · Code review · Commit / push / release |
| **High-risk work** | Production incident · Bug fix · New CRUD module · Schema / migrations · Writing database data · Auth / permissions · Public endpoint · Shared config |
| **Small jobs, no plan** | Typo / format / rename · Routine backend code · Write tests · UI change |
| **Standalone** | Q&A · Multi-file research · Mockup / prototype · Docs · Merge conflicts · Large migration · Plan progress · Glossary and ADRs · Architecture (C4) · Security testing · Project roadmap |

Already know the flow? Say `Flow: #6` or name the skill and the agent skips the question.

### 2. The agent knows where it is: project, plan and task

Progress is written to plain Markdown and JSON in your repo, not kept in the chat. Ask in plain words and the agent answers from those files:

| You ask | Level | Answered from |
|---|---|---|
| "How far is the project? What is still missing?" | **Project** | `docs/du-an/lo-trinh.json`: goals, then items per goal (module or phase), each with status, owner, deadline and acceptance criteria |
| "Where are we in the plan?" | **Plan** | `docs/artifact/<date-name>/plan.md`: tasks and the dependencies between them |
| "What is left? What is blocked? What needs me?" | **Task** | `progress.md`: one row per task with `TODO` · `DOING` · `CHỜ-USER` (waiting for you) · `BLOCKED` · `DONE` · `SKIP` |
| "What should happen next?" | **Task** | ready tasks ranked by how many other tasks wait behind them |

- **Survives context compaction and new sessions.** After a compaction the agent resumes the running plan from `intent.md` and `progress.md` instead of starting over; a new session can pick up from the same files.
- **Multi-step work never starts without a plan.** `plan.md` and `progress.md` are written and approved before task 1, then updated after every task.
- **Scope is written down.** `intent.md` lists the files the agent may touch; anything outside needs your OK by name.

### 3. Watch it live: Agent Observatory

The same files feed a local dashboard: overview, Kanban board, mission list, full plan graph, timeline, roadmap, and agent and skill usage.

- **"Needs you" counter** in the tab title for tasks waiting on you or blocked, with optional browser notifications.
- **Token and cost per task**, with alerts for token spikes, budget overruns and stalled agents.
- **Process alerts**: a task marked done with no check after the last edit, one file edited over and over, a failed command retried without changes.
- **Export Summary** writes a report of the mission into its folder.

### 4. Guardrails that hold

| Without it | With Athena Starter |
|---|---|
| The agent starts editing before anyone agreed on scope. | Every new request gets a flow question first; scope goes into `intent.md` before any file changes. |
| Bugs get "fixed" without proof. | Bug fixes and behavior changes start with a test that fails for the right reason. |
| The agent reviews its own work and calls it done. | Risky changes go to a separate reviewer with fresh context; "done" needs the check output from this turn. |
| Plans and notes end up scattered across chats. | Each task has one folder, `docs/artifact/<date>-<name>/`, with intent, plan and a progress table a dashboard can read. |
| `git commit --no-verify` quietly skips your hooks. | Safety hooks block hook bypasses and edits that loosen lint config. |

## Quick start

```
/plugin install athena-starter --marketplace zzgiabaozzbui/athena-starter
```

Start a new session and ask for anything. The agent answers with a flow question, then works through the matching skill.

<details>
<summary>Older Claude Code, APM, and requirements</summary>

Claude Code before 2.1.275, two steps (the marketplace is named `athena`):

```
/plugin marketplace add zzgiabaozzbui/athena-starter
/plugin install athena-starter@athena
```

With APM:

```
apm install zzgiabaozzbui/athena-starter
```

The hooks run with `node`: Node 18 or later must be on your PATH. A native Claude Code install may not include Node; without it the hooks stay silent and do nothing.

</details>

## What a session looks like

1. **Route.** You ask for a change. The agent restates it and asks which flow fits (bug fix, feature, review, research...), recommended option first.
2. **Scope.** It writes `intent.md` with the files it may touch. Anything outside needs your OK by name.
3. **Plan.** Multi-step or risky work gets `plan.md` and `progress.md`, which you approve before any code changes.
4. **Build.** Tasks go to the right agent (`developer`, `quick-fix`...). Fixes start with a red test.
5. **Review and verify.** A reviewer with fresh context checks the diff; the main agent re-runs the checks itself instead of trusting the report.
6. **Commit.** Named files only, a sensitive-data scan before every commit, and no push without your OK.

## Agent Observatory desktop app

**[Download the latest Windows installer](https://github.com/zzgiabaozzbui/athena-starter/releases/latest)** (`Agent-Observatory-Setup-*.exe`).

- Reads the `docs/artifact/` folders and progress tables that Athena Starter writes.
- Read-only and local: it listens on 127.0.0.1 only and sends nothing out.
- Windows only for now; the interface is in Vietnamese.

Prefer to stay inside Claude Code? The same app ships as an optional plugin:

```
/plugin install athena-observatory@athena
```

The plugin bundles the app as is. Besides Claude Code sessions it also reads transcripts of other coding agents and route logs when they exist; these are features of the app, not data shipped with it.

## What's inside

<details>
<summary>18 skills</summary>

| Skill | When to use |
|---|---|
| `routing` | First message of a session and every new request: picks the flow. |
| `brainstorming` | New request with no intent or spec yet. |
| `grilling` | Stress-test a plan or design before building. |
| `architecture` | Vague request, or adding / changing / connecting systems: C4 pictures before the plan. |
| `writing-plans` | Intent is settled: write plan.md and progress.md for approval. |
| `executing-plans` | Run an approved plan inline. |
| `subagent-driven-development` | Run an approved plan by delegating tasks to agents. |
| `test-driven-development` | Bug fix or behavior change: red test first. |
| `systematic-debugging` | Bug, red test or stack trace: find the root cause before fixing. |
| `commit-push` | Before every commit, push, tag or release. |
| `using-git-worktrees` | Wide refactor, parallel agents, or a dirty working tree. |
| `resolving-merge-conflicts` | A merge or rebase reports CONFLICT. |
| `docs-standards` | Writing or editing Markdown docs; sensitive-data check before commit. |
| `domain-modeling` | Business terms (GLOSSARY.md) and architecture decisions (ADRs). |
| `e2e-playwright` | Write, run or fix Playwright e2e tests for a web UI. |
| `security-testing` | Role x endpoint authorization tests and dependency audit. |
| `migrating-legacy-code` | Large upgrade, rewrite or modernization. |
| `prototype` | Throwaway code that answers one design question. |

</details>

<details>
<summary>6 agents</summary>

| Agent | Role |
|---|---|
| `developer` | Implements non-trivial changes from a spec or plan; returns diff and test output. |
| `researcher` | Read-only investigation and codebase analysis. |
| `quick-fix` | Small mechanical changes (typos, formatting, one-line fixes). |
| `code-reviewer` | Read-only review of code, rule, skill, agent and hook changes. |
| `verifier` | Independent build, typecheck, lint and test run; reports only. |
| `ux-tester` | Tries the real UI as a newcomer (needs the Playwright MCP server); reports only. |

</details>

<details>
<summary>3 hooks</summary>

- **SessionStart**: injects the core rules (`rules/core.md`) and reminds the agent to invoke `routing` first (after a context compaction it reminds the agent to resume the running plan instead).
- **Block `--no-verify`**: stops Bash commands that skip git hooks.
- **Protect lint config**: stops edits to existing lint / format config files (eslint, prettier, biome, commitlint); the agent must fix the code, not loosen the config.

</details>

## Safety and privacy

- No telemetry: the hooks only print to the session; they write no files and make no network calls.
- Before every commit the `commit-push` skill scans staged lines for credentials, long numbers, IPs, emails, machine paths and key files; it prints locations, never values.
- Pushes, tags and releases always wait for your OK.

## Customize

- Your project's `CLAUDE.md` and `.claude/rules/` win over these defaults. Declare your stack and verify commands there; the `developer` agent reads them.
- Each task keeps its documents in `docs/artifact/<YYYY-MM-DD-HHMM-name>/`. The progress table uses the columns `ID | Task | Deps | Status` with the statuses `TODO`, `DOING`, `CHỜ-USER` (waiting for the user), `BLOCKED`, `DONE`, `SKIP`. Details are in the `docs-standards` skill.
- To turn the hooks off, disable the plugin with `/plugin` (select `athena-starter`, then Disable).

## Releases

This repository also holds the official releases of Agent Observatory Desktop.

## License

MIT; third-party sources are credited in [`NOTICE`](NOTICE).
