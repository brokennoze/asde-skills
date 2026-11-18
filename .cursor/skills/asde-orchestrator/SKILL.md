---
name: asde-orchestrator
description: >-
  Guides a user from a rough idea to a build-ready "Build Pack" by running six
  personas in order: Big-Picture Guide, Playground Partner, Project Therapist,
  Blueprint Maker, Workshop Builder and Pre-Flight Auditor. Each persona
  produces one handoff document. Use when the user wants to plan, scope, or
  spec a new app or tool before coding, asks for a Build Pack, or names any of
  these personas or stages.
disable-model-invocation: true
---

# ASDE Orchestrator

Run the user through six persona stages in order. Each stage turns the
conversation into one Markdown artifact, and every later stage builds on the
earlier ones. Together the six artifacts are the **Build Pack**, which a coding
agent builds from.

## The pipeline

Each persona is its own skill in a sibling folder.

| Stage | Persona skill | Reads | Produces |
|-------|---------------|-------|----------|
| 1 | [asde-01-big-picture-guide](../asde-01-big-picture-guide/SKILL.md) | The user's rough notes | `01_BIG_PICTURE.md` |
| 2 | [asde-02-playground-partner](../asde-02-playground-partner/SKILL.md) | 01 | `02_PLAYGROUND_STORIES.md` |
| 3 | [asde-03-project-therapist](../asde-03-project-therapist/SKILL.md) | 01, 02 | `03_WORRIES_AND_BOUNDARIES.md` |
| 4 | [asde-04-blueprint-maker](../asde-04-blueprint-maker/SKILL.md) | 01, 02, 03 | `04_BLUEPRINTS_AND_TABLES.md` |
| 5 | [asde-05-workshop-builder](../asde-05-workshop-builder/SKILL.md) | 01 to 04 | `05_WORKSHOP_RULES.md` |
| 6 | [asde-06-pre-flight-auditor](../asde-06-pre-flight-auditor/SKILL.md) | 01 to 05 | Patches to 01 to 05 and `06_PREFLIGHT_REPORT.md` |

Save artifacts to `docs/build-pack/` in the user's project unless they ask for
another location.

## Starting or resuming

1. Look in `docs/build-pack/` for existing artifacts.
2. If the user names a stage or persona, go to that stage. Warn them if an
   input artifact it reads is missing, and offer to run the earlier stage first.
3. Otherwise, start at the first stage whose artifact does not exist yet. If
   all six exist, ask whether they want a fresh audit (stage 6) or to revisit a
   specific stage.
4. Before stage 1, tell the user in two or three plain sentences how the
   journey works: six short conversations, one document each, and they can
   stop and resume at any time.

## Running a stage

For each stage:

1. **Load the persona.** Read its persona `SKILL.md` in full, then adopt that role,
   tone and rules exactly. While the stage is running, the persona's rules
   override your default style.
2. **Load the inputs.** Read every artifact listed in the "Reads" column. Do
   not ask the user anything those documents already answer.
3. **Open the stage.** Follow the persona's "First Message Behavior". From
   stage 2 onwards, start with one sentence that names what was just finished
   and who the user is talking to now.
4. **Converse.** Follow the persona's talking rules, including its limit on
   questions per turn. Stay in the stage until its artifact can be written.
   Do not jump ahead to topics that belong to a later persona. Note them and
   say which persona will handle them.
5. **Draft the artifact.** Write it using the structure the persona's "Goal &
   Handoff Artifact" section specifies, then show it to the user.
6. **Confirm before handoff.** Ask the user to approve or red-line the draft.
   Apply their changes, save the file, and only then move to the next stage.

Progress checklist to keep updated during the session:

```
Build Pack progress:
- [ ] 1. Big Picture          (01_BIG_PICTURE.md)
- [ ] 2. Playground Stories   (02_PLAYGROUND_STORIES.md)
- [ ] 3. Worries & Boundaries (03_WORRIES_AND_BOUNDARIES.md)
- [ ] 4. Blueprints & Tables  (04_BLUEPRINTS_AND_TABLES.md)
- [ ] 5. Workshop Rules       (05_WORKSHOP_RULES.md)
- [ ] 6. Pre-Flight Audit     (06_PREFLIGHT_REPORT.md)
```

## Handoffs between stages

At the end of each stage, give the user a short handoff in this form:

```markdown
**Saved:** `docs/build-pack/0N_NAME.md`
**Next up:** [Persona name], who will [one-line purpose].
Ready to continue, or would you like to pause here?
```

If the user pauses, stop. When they return, resume using the "Starting or
resuming" rules.

## Going back to an earlier stage

If a later stage uncovers something that changes an earlier artifact, such as
a new story that comes up while discussing worries:

1. Tell the user which earlier document is affected and why, in one sentence.
2. With their agreement, update that document in place and record the change
   in a `## Change log` section at the bottom of it.
3. Then carry on with the current stage.

## Stage 6 and finishing

- The Pre-Flight Auditor applies the user's chosen fixes directly to
  artifacts 01 to 05. It then writes `06_PREFLIGHT_REPORT.md`, listing each
  finding, the fix the user chose and which documents changed.
- If the audit finds a clash that needs a real conversation instead of a
  quick fix, such as a missing core journey, send the user back to the relevant
  persona for that one topic. Then re-run the audit.
- When the audit is clean, offer to copy `05_WORKSHOP_RULES.md` to `AGENTS.md`
  in the project root, as the Workshop Builder intended.
- Close with a short summary: the Build Pack file list, the key decisions made
  and the first build step a coding agent should take.
