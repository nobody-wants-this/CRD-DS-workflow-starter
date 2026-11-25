---
name: ds-setup
description: Set up the starter edition of the design system workflow kit in a repo made from the template — check the prerequisites, connect GitHub and the designer's duplicate of the starter Figma file, fill in the few remaining config values, ask a handful of questions about the designer and how they like to work, then commit and push. Use when the designer says "set up the workflow kit", when asked to "check the setup", or when a skill reports a missing config value. Re-running picks up where the last run stopped.
---

# Set up the starter kit

Gets a designer from "I've made my repo from the template" to "I can write a CRD". The repo
already holds everything: nothing gets installed or moved. Most of the config is pre-filled for
the starter edition, so setup is short. Aim for ten minutes.

The person is probably new to GitHub, the terminal and Claude Code. Say what each step is for
in one plain line, never assume a term is known, and celebrate small wins (their first commit is
a big one).

**Two modes:**
- **Setup** (phases 1–5). Re-running resumes: done steps are re-checked, not redone.
- **Check** ("check the setup"): phase 5 only, read-only.

## Rules for the whole run

- **Follow `.claude/CLAUDE.kit.md`**, starter edition rules first.
- **Work out, confirm, then write.** Show what you found before writing it.
- **Ask per `.claude/CLAUDE.kit.md` § Asking questions.**
- **Sign-in that needs a browser or a prompt is the person's.** Hand it over as `! <command>`,
  then check it worked.
- **One phase at a time.** Report in a line or two, then carry on.

## Phase 1: Prerequisites

Check, and fix anything missing before going on:

- **This is a repo made from the template**: `.claude/design-system.config.md` and
  `.claude/skills/ds-crd-write/` exist, and `git remote get-url origin` points at the person's
  own GitHub repo, not the template's. If it points at the template, they cloned the template
  itself: explain the difference and walk them through "Use this template".
- **The Figma plugin** is installed in Claude Code. If not: `! /plugin install
  figma@claude-plugins-official`, then restart Claude Code in this folder.
- **The model.** Recommend a top-tier model for the CRD and design work.

## Phase 2: Connect

Stop at the first connection that fails, fix it, then carry on.

| Connection | Check | What to do |
|---|---|---|
| **GitHub** | `gh auth status`, or a dry `git push` | If signed out: `! gh auth login`. If `gh` isn't installed, `git push` prompting in the browser is fine |
| **Figma** | `whoami` | Needs a seat that can edit their own drafts. Ask for the **URL of their duplicate** of the starter file |

**The starter file.** Every designer works in their own duplicate of the shared "CRD DS
workflow starter" file. If they haven't duplicated it yet, give them the starter file link from
`README.md` and wait. If the URL they paste is the shared original, not a duplicate, say so:
they can't build in the original.

Prove the connection with a read: open the file and list its pages and variable collections.
Expect Cover, Read me, Foundations, Icons, Components, ❖ Your first component and Doc utilities
(with divider pages between groups), and the Primitives, Semantic and Component collections.
Anything missing: say what, and ask whether they duplicated the right file.

## Phase 3: Fill in what's left

Pre-filled values stay as they are. Fill only these, showing each with its evidence:

| Config key | Source |
|---|---|
| System → `name` | Ask: "What do you want to call your design system?" Offer two or three playful proposals, plus their own |
| System → `org` | Their GitHub username, from the remote |
| Repo → `slug`, `url` | From `git remote get-url origin` |
| Figma → `org` | From `whoami` |
| Figma → `component_library` | Their duplicate's file name and key, from the URL |
| Figma → `reference_page` | `—` for now: their first component becomes the reference |

**Design foundations** (config § Paths → `design_foundations` Part 1) and **token conventions**
(`token_conventions`) come filled in: the starter file is the same for everyone. Check their
duplicate still matches: the collections and modes in § Token tiers and § Mode axes. If they've
already changed the file, say what differs and ask whether to update the docs to match.

The classification doc (config § Paths → `classification`) comes filled in. Their component is
`tier: core` and `composition: basic`: one building block, not made of other components.

## Phase 4: About you

A short interview, one question at a time, written into `docs/design/glossary.md` Part 1 and a
new **§ About the designer** section at the end of `design_foundations` Part 1. The CRD skill
reads both. Keep it to five questions or fewer:

1. **Your role and experience**: product designer, systems designer, student, other. How much
   design system work they've done. This sets how much the CRD interview explains.
2. **What you design for**: web, iOS, Android, all of them. This becomes the CRD's platforms.
3. **Which component** they're starting with. Recommend radio, switch or text input, or one of
   their own, as long as it's basic: not made of other components.
4. **How you like to be challenged**: gently with lots of explanation, or direct and brisk.
   Either way, the CRD interview still asks for their reasons.
5. **Any words you use** that a newcomer wouldn't know. Optional: add each to the glossary.

## Phase 5: Check, commit, push

Read-only checks first. Report blockers, then warnings, then what passed in one line.

**Block:**
- An empty config value that a kept skill cites (grep `.claude/skills/` for `config §`).
- A path in config § Paths that doesn't resolve.
- The Figma file won't open, or no seat that can edit.
- An empty Part 1 heading in foundations.

**Warn:**
- The glossary's Part 1 still holding the Acme example.

Then **commit and push**, the first time explaining in one line each what a commit and a push
are. Stage the files setup changed by name, never `git add -A`. Message: `Set up the workflow
kit`. After the push, give them the link to see it on GitHub.

End with the next sentence to say: *"Write the CRD for {their component}."*
