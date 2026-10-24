# Orchestra sviluppo

[Italiano](README.md) | [English](README.en.md)

A skill for planning and coordinating complex development work through independent work packages,
file ownership, a separate tester, and verifiable checkpoints. This repository contains two
self-contained versions for **Claude Code** and **Codex**. Choose the version for your harness:
the environment that provides the agent with tools, context, and execution permissions.

The workflow starts by discovering the actual project environment. It does not assume a language,
framework, operating system, branch naming scheme, issue tracker, or project script. No additional
libraries are required.

## Structure

```text
claude/
  SKILL.md
  references/
    contesto.md
    analisi.md
    artifact.md
    protocollo.md
  templates/
    CONTESTO.md
    PLAN.md
    piano.md
    AVVIO.md
    esito.md
codex/
  SKILL.md
  agents/openai.yaml
  references/
  templates/
```

Each directory includes all its own references and templates. Once installed, neither version
needs links to the other harness's version. `codex/agents/openai.yaml` keeps skill invocation
explicit; Claude Code uses `disable-model-invocation: true`.

## Harness differences

| Aspect | Claude Code | Codex |
| --- | --- | --- |
| Invocation | `/orchestra-sviluppo <phase> <slug>` | `$orchestra-sviluppo <phase> <slug>` |
| Orchestrator | A dedicated session | The main chat |
| Workers and tester | Separate sessions opened by the user | Native subagents created by the orchestrator |
| Coordination | `ListAgents` and `SendMessage`, when available | The native delegation, messaging, waiting, and follow-up capabilities actually exposed by the runtime |
| Permissions | Each session's configuration and supported flags | Sandbox and approvals inherited from the parent |
| Recovery | Sessions recovered through the resume mechanism | Agents recovered when supported; otherwise, state reconstructed from artifacts |

Ownership is a coordination rule, not a technical filesystem boundary. In particular, assigning
a role to a Codex subagent does not give it a separate sandbox. Neither version automatically
creates an isolated working copy simply because it creates or uses a collaborator.

## Manual installation

Choose one variant for each harness. Copy **the entire variant directory** to the destination,
naming it `orchestra-sviluppo` and preserving its references and templates. Do not copy only
`SKILL.md` or leave an intermediate `claude/` or `codex/` directory at the destination.

| Harness | In the target project | Personal, across projects |
| --- | --- | --- |
| Claude Code | `<project>/.claude/skills/orchestra-sviluppo/` | `~/.claude/skills/orchestra-sviluppo/` |
| Codex | `<project>/.agents/skills/orchestra-sviluppo/` | `~/.agents/skills/orchestra-sviluppo/` |

On Windows, `~` refers to the user's home directory; use the appropriate path separators for your
system. For example, the resulting path should be
`<project>/.agents/skills/orchestra-sviluppo/SKILL.md`, with `references/`, `templates/`, and,
for Codex, `agents/` alongside it.

If the destination already exists, compare it before replacing it and preserve customizations.
Avoid personal and project copies with the same name unless you want multiple entries for the
same skill. These instructions do not install anything automatically or change harness configuration.

Official documentation describes skill locations and loading:
[Claude Code skills](https://code.claude.com/docs/en/skills) and
[Codex skills](https://learn.chatgpt.com/docs/build-skills).

## Usage

Start your harness in the target project with its normal permissions. To begin, enter this in chat:

Claude Code:

```text
/orchestra-sviluppo raccogli nome-sviluppo
```

Codex:

```text
$orchestra-sviluppo raccogli nome-sviluppo
```

These are prompts for the harness, not shell commands. `nome-sviluppo` is the slug: a short
identifier used to locate the artifacts for that development effort.

| Phase | Output | Next step |
| --- | --- | --- |
| `raccogli` (collect) | `CONTESTO.md`: tools, commands, environment, integration, and permissions | Clarify blockers that affect the work |
| `analizza` (analyze) | `ANALYSIS.md`, `CONTRATTO.md`: requirements, checkpoints, and dependency graph | Explicit approval |
| `pianifica` (plan) | `PLAN.md`, package and test plans, `AVVIO.md` | Explicit approval before execution |
| `esegui` (execute) | Implementation in waves, checks, and results | Decisions and deliveries according to the plan |
| `riprendi` (resume) | State reconstructed from artifacts | The first step without recorded evidence |
| `chiudi` (close) | Requirements coverage, documentation, gaps, and handover | Final actions only when authorized |

If you start with `analizza` without an existing context, the skill first performs the necessary
discovery. It reads instructions, documentation, and manifests; it does not install tools, start
collaborators, or run commands merely to check their availability. Unconfirmed information remains
explicitly marked as such.

Artifacts normally live in `.planning/filoni/<slug>/` in the main working copy. A different path
can be agreed upon if the project already has a convention. Approval for file changes always follows
local instructions and any authorization already granted.

## Workflow principles

- Parallel work follows the dependency graph. If the work is sequential, the skill reports that.
- Each file has a single writer. Workers implement; the tester owns tests and result reports;
  the orchestrator updates state and handles decisions.
- The approved contract defines the expected outcome. A successful build does not prove every
  requirement has been met.
- Every wave receives a documented result, even when it only involves unit tests: `OK`, `KO`,
  or `PARZIALE`.
- Checkpoints provide a useful path to try. Temporary mocks have a planned removal point.
- The main working copy remains frozen from a checkpoint's technical `OK` through delivery. Subsequent
  work waits or uses isolated working copies that have already been authorized.
- Tool availability does not grant authorization. Without valid delegation, Git, installations,
  checks, application startup, and external actions remain the user's responsibility. Commands
  are prepared with their working directory, prerequisites, and expected result.
- Settings are not changed to bypass limits, and credentials are not recorded in artifacts.
- Artifacts are retained at closure unless their transfer or cleanup is explicitly agreed upon.

## First manual trial

Prerequisites: the selected variant is installed, and the harness is available in a test project
with known instructions. A skill-loading trial does not authorize changes to the real project.

1. Open the target project and check that the skill appears in the selector. Restart the harness
   if needed, and check that the skill directory contains `SKILL.md` directly.
2. Invoke `raccogli prova` with a small development request and ask the agent to present the context
   before writing it. It should identify the stack and commands from available sources, report
   missing information, and follow local instructions without implicit installations or execution.
3. After your approval, inspect `CONTESTO.md`: commands should include working directories and
   sources, tools should actually be available, branches should not be assumed, and permissions
   should be recorded in a matrix.
4. Continue with `analizza prova`. The result should distinguish requirements, contract,
   checkpoints, and dependencies, then wait for approval. An entirely sequential task should
   not create agents.
5. For a full trial, use an example with two independent packages, approve planning and execution,
   and follow `AVVIO.md`. In Claude Code, open the planned sessions; in Codex, observe the subagents
   from the main chat. Check disjoint file ownership, `FINITO` reports, the tester's result, and
   recovery.
6. Include a wave with only unit tests and a case with a missing check. The first must receive
   a result report; the second must remain `PARZIALE`. A technical `OK` alone must not trigger
   an external delivery.

This repository contains instructions and templates, with no executable code or test suite of its
own. Static review does not demonstrate that the workflow works with your harness configuration.
These generic versions need an initial real-world trial; they are not claimed to be field-validated.

## Maintenance

Common resources are intentionally duplicated so that each variant can be installed independently:

- `references/contesto.md`, `references/analisi.md`, `references/artifact.md`;
- `templates/CONTESTO.md`, `templates/PLAN.md`, `templates/piano.md`, `templates/esito.md`.

Update both copies when the shared method changes. `SKILL.md`, `references/protocollo.md`, and
`templates/AVVIO.md` are specific to each harness. For native capabilities, consult current
documentation and the tools actually exposed by the runtime:
[Claude Code messaging](https://code.claude.com/docs/en/cross-session-messaging) and
[Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents).

## Experimental Flow Editor + Paseo integration (Codex only)

The `codex/` variant includes an explicit, optional Paseo mode. Flow Editor launches the
orchestrator; after plan approval, it starts workers and the tester as Codex sessions in the same
local Paseo daemon and workspace. See [the Paseo protocol](codex/references/paseo.md).

Paseo Desktop, a compatible CLI, and Codex authenticated with ChatGPT must already be available.
Nothing is installed automatically. Session usage shares your plan limits; roles do not create
separate sandboxes. Existing authorization rules and artifacts still apply. This initial
integration uses existing local directories and does not create worktrees. Native delegation
remains the default, and the Claude variant is unchanged. Manual integration testing is pending.
