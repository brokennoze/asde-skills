---
name: asde-orchestrator
description: >-
  Runs the ASDE (Agentic Specification-Driven Engineering) workflow end to end:
  two system-level stages (Big-Picture Guide, Workshop Builder), then seven
  stages per module (Playground Partner, Project Therapist, Blueprint Maker,
  Contract Keeper, Test Referee, Pre-Flight Auditor, Build Pilot), producing a
  Build Pack of specs, locked contracts and locked tests that a coding agent
  builds against. Handles resuming, existing systems, a fast path for
  presentation-only modules, and spec-first changes. Use when the user wants to
  plan and build a system or feature with agents, asks for a Build Pack, or
  names ASDE or any of its personas.
disable-model-invocation: true
---

# ASDE Orchestrator

ASDE replaces user-story slicing with a specification-first flow sized for coding agents. Each module is specified as a whole, with system-wide context, so the agent never builds one slice without knowing what comes next. Its output is pinned down by typed contracts and proven by tests written before the code.

You coordinate nine persona skills. Each one runs a conversation, produces one artifact, and hands off to the next. You decide which stage runs, enforce the gates between stages, and own the change protocol.

## The pipeline

**System stages: run once, then updated as needed**

| Stage | Skill | Produces |
|-------|-------|----------|
| 1 | [asde-01-big-picture-guide](../asde-01-big-picture-guide/SKILL.md) | `system/01_BIG_PICTURE.md`: journeys, module map, constraints |
| 2 | [asde-02-workshop-builder](../asde-02-workshop-builder/SKILL.md) | `AGENTS.md` and `system/02_WORKSHOP_DECISIONS.md` |

**Module stages: run for each module, in the Module Map's build order**

| Stage | Skill | Produces |
|-------|-------|----------|
| 3 | [asde-03-playground-partner](../asde-03-playground-partner/SKILL.md) | `03_PLAYGROUND_STORIES.md` |
| 4 | [asde-04-project-therapist](../asde-04-project-therapist/SKILL.md) | `04_WORRIES_AND_BOUNDARIES.md` |
| 5 | [asde-05-blueprint-maker](../asde-05-blueprint-maker/SKILL.md) | `05_BLUEPRINT.md` |
| 6 | [asde-06-contract-keeper](../asde-06-contract-keeper/SKILL.md) | Contract files in code (locked) and `06_CONTRACTS.md` |
| 7 | [asde-07-test-referee](../asde-07-test-referee/SKILL.md) | Test files in code (locked) and `07_TEST_PLAN.md` |
| 8 | [asde-08-pre-flight-auditor](../asde-08-pre-flight-auditor/SKILL.md) | `08_PREFLIGHT_REPORT.md` with a verdict |
| 9 | [asde-09-build-pilot](../asde-09-build-pilot/SKILL.md) | Implementation and `09_BUILD_LOG.md` |

## Build Pack layout

```
AGENTS.md
docs/build-pack/
├── system/
│   ├── 01_BIG_PICTURE.md
│   └── 02_WORKSHOP_DECISIONS.md
└── modules/
    └── <ID>-<short-name>/         one folder per Module Map entry
        ├── 03_PLAYGROUND_STORIES.md
        ├── 04_WORRIES_AND_BOUNDARIES.md
        ├── 05_BLUEPRINT.md
        ├── 06_CONTRACTS.md
        ├── 07_TEST_PLAN.md
        ├── 08_PREFLIGHT_REPORT.md
        └── 09_BUILD_LOG.md
```

Contract and test files live in the codebase, where `AGENTS.md` says; the module folder only indexes them.

## Starting or resuming

1. Check what exists.
   - If `01_BIG_PICTURE.md` is missing, start at stage 1.
   - If `AGENTS.md` or `02_WORKSHOP_DECISIONS.md` is missing, start at stage 2.
2. Otherwise, pick the module:
   - If the user names a module, use it.
   - If not, use the first module in build order that has no `09_BUILD_LOG.md` with a Complete result.
3. Within that module, start at the first stage whose artifact is missing.
4. If the user names a stage, go to it, but check its gate first (below). If the gate is not met, explain what is missing and offer to run the stage that produces it.
5. Before stage 1 of a new system, explain the journey to the user in 2–3 plain sentences:
   - two short conversations set up the whole system;
   - then each module goes from stories to tested code;
   - they can stop and resume at any time.

## Running a stage

1. **Load the persona.** Read its `SKILL.md` in full and adopt its role, tone and rules. While the stage runs, the persona's rules override your default style.
2. **Load the inputs** the persona lists. Do not ask the user anything those documents already answer.
3. **Open the stage** with the persona's first-message behaviour. From the second stage onwards, start with one sentence naming what was just finished and who the user is talking to now.
4. **Stay in scope.** Topics that belong to another stage or module go in that document's Parking Lot, not into this stage's artifact.
5. **Draft, confirm, save.** The user approves or corrects every artifact before it is saved. Only then hand off.

## Gates

A stage may not start until its gate is met:

| Stage | Gate |
|-------|------|
| 2 | `01_BIG_PICTURE.md` approved |
| 3 | `AGENTS.md` exists; every module this one depends on has a Complete build log |
| 4–6 | The previous stage's artifact for this module is approved |
| 7 | All of this module's contracts are Locked and type-check passes |
| 8 | All of this module's tests are Locked and the baseline run shows no broken tests |
| 9 | `08_PREFLIGHT_REPORT.md` says **Cleared for build** |

If a dependency is not built yet but the user wants to specify this module anyway, allow stages 3 to 5 and warn that stages 6 onwards must wait.

## Paths

Choose the path at the start of each module and tell the user which one you are using.

- **Standard path:** stages 3 to 9 in order.
- **Existing-system path:** used automatically when the Big Picture says the system already exists. Same stages; each persona reads the current code first. The Test Referee writes passing characterisation tests of current behaviour before any acceptance tests.
- **Fast path:** only for a module that presents or arranges data other modules already own and has no rules of its own, such as a dashboard or report screen. Offer it; never choose it silently.
  - Stage 3: a short set of stories covering what is shown and how filters and controls behave.
  - Stage 4: skip it unless the module shows private data. If it does, cover only peeking and privacy.
  - Stage 5: only the guardrails, meaning where state lives (one source of truth), which shared data functions are used, and the UI rules.
  - Stage 6: contracts for that state and those data functions only.
  - Stage 7: tests that every view and export uses the shared data functions, plus one end-to-end smoke test.
  - Stage 8: mechanical checks only.
  - Stage 9: as normal. Within these guardrails, visual layout can be iterated freely afterwards.

## Change protocol

Specs are the single source of truth. Any change after a document is approved goes through these steps, whether it comes from the user, a later stage, or the Build Pilot.

1. **Find the earliest affected document**, in chain order: Big Picture, workshop, stories, boundaries, blueprint, contracts, tests.
2. **Explain the change** to the user in one or two plain sentences, and get agreement.
3. **Update that document, then every later document in the chain, in order.** Add a Change Log entry to each one. Locked contracts and tests may be changed only at this step.
4. **Check other modules.** If a shared contract or interface changed, list every module that imports it and run their tests.
5. **Re-audit.** If contracts or tests changed, re-run the Pre-Flight Auditor's mechanical checks before building continues.
6. **Only then change the implementation.**

Never change code first and update the spec afterwards.

## Handoffs

At the end of each stage, give the user a short handoff:

```markdown
**Saved:** `[path]`
**Next up:** [Persona name], who will [one-line purpose].
Ready to continue, or would you like to pause here?
```

If the user pauses, stop. When they return, resume using the rules above.

## Progress

Keep this checklist updated during the session. Repeat the module block for each module.

```
System
- [ ] 1. Big Picture
- [ ] 2. Workshop Rules (AGENTS.md)

Module [ID] [name] — path: standard | existing | fast
- [ ] 3. Playground Stories
- [ ] 4. Worries & Boundaries
- [ ] 5. Blueprint
- [ ] 6. Contracts (locked)
- [ ] 7. Tests (locked, failing for the right reason)
- [ ] 8. Pre-Flight: cleared
- [ ] 9. Build: all green, reviewed
```

## Finishing a module and the system

- **After each module:** summarise what was built, by story, and point to the reviewer notes in `09_BUILD_LOG.md`. Then offer to start the next module in build order.
- **When every module in the Module Map is complete:** run the full test suite and summarise the system, including the modules, any open questions still unresolved, and all changes made through the change protocol.
