## Design system workflow

## Starter edition

This repo is the **starter edition** of the design system workflow kit: one designer, learning
the workflow by writing one CRD and designing one basic component against the shared starter
Figma file. These rules override anything below, and anything in a skill, that assumes a team:

- **No tracker.** Skip every ticket step: raising, linking, moving or citing tickets. Never ask
  for a ticket ref.
- **The designer holds every role.** Publisher, ready-for-dev awarder, skill owner: all them.
  Where a skill pauses for a role, ask the designer directly.
- **Commit straight to `main`.** No branches or pull requests unless the designer asks for one.
  Commit when a piece of work is agreed (a CRD draft, a revised section), with a short plain
  message, then push. Explain what a commit and a push are the first time, in one line each.
- **Skills not in this repo** (`ds-ticket`, `ds-ready-for-dev`, `ds-helper`, `ds-token-variables`,
  `ds-design-journey`, `ds-crd-read`, `ds-publish`): when a skill hands over to one, skip that
  step and say it's part of the full kit, not the starter. Where a skill sends you to
  `ds-crd-read` to find a CRD, glob config § Paths → `crd_dir` and match the `name:` front matter.
- **Every new or changed token is the designer's call**, by config § Paths →
  `token_conventions`: recommend a tier and a name with the reason, let the designer decide, and
  record it with their reason in the CRD's Design decisions.
- **One Figma file, no publish gates.** Tokens, icons and the component all live in the
  designer's duplicate as local variables and components, so nothing needs publishing before
  it can be bound. Skip every publish gate and `importVariableByKeyAsync` step.
- **The CRD conversation is the point.** Slow down there. Recommend, but make the designer
  decide, and when they don't take your recommendation, ask them for their reason in one line
  and record it in the CRD's decision notes.


The kit's standing rules, installed as is and imported from the repo's `CLAUDE.md` with
`@.claude/CLAUDE.kit.md`. Never reword or merge them into `CLAUDE.md`: every repo runs the same
rules as the kit. Changes go to config § Roles → `skill_owners`, like the skills.

- **The config is the source of system facts.** Read `.claude/design-system.config.md` for
  branches, the tracker, roles, paths and accessibility targets. Never guess a value it holds;
  if one is empty, ask.
- **Design facts** (Figma files, token tiers, modes, text styles, spacing) live in config § Paths
  → `design_foundations`. Read it before any Figma work.
- **Token rules** (which tier, naming, who decides) live in config § Paths → `token_conventions`.
  Read it before creating or changing a token.
- **Skills own the workflows.** CRDs, Figma components, tickets and the ready-for-dev hand-off
  each have a `ds-*` skill in `.claude/skills/`. Don't edit skills or their reference docs from
  inside other work; propose the change to config § Roles → `skill_owners`.

### Asking questions

Every `ds-*` skill that asks the person anything asks in this order:

1. **Show what you found first**, as plain text: findings, the gap table, any table they need
   to answer. No question dialog in the same turn: the dialog hides the text.
2. **End that turn with one line**: how many questions, then "Shall we start the interview?"
3. **On a yes, ask one question at a time** with AskUserQuestion, and take each answer in
   before asking the next. Never several at once.
   - The recommended option comes first, marked "(Recommended)", then the alternatives, each
     with its trade-off in the description.
   - A preview (a mockup, a table, a snippet) wherever seeing the options helps.
   - An open question (a purpose, a name) still gets options: proposals to react to, with
     "Other" for their own words.

One question and nothing to show: ask it straight away.

### Only what exists now counts

**Anything deleted was deleted on purpose**: Figma nodes and pages, files, branches, tickets,
the output of earlier runs. Never mention it, offer to restore or recreate it, or ask about it.
Don't read version history or earlier runs to find what used to be there. If something that
exists now is broken, such as a binding, a property reference or a link, report the breakage as
it stands, not the deletion.

### Repo

- **Pull the integration branch at session start** if config § Repo → `pull_at_session_start` is
  Yes, fast-forward only.
- **Branches and pull requests follow config § Repo → `direct_push`.** In the starter edition
  that's Yes: commit to `main`.
- **A denied `git push` is usually the command's shape, not a missing permission.** Permission
  rules match the start of the command, so `cd repo && git push` or `git push | tail` can be
  refused when `git push` alone is allowed. Retry bare as `git -C /absolute/path push …` before
  concluding anything.

### Figma

- **Text content follows config § Writing → `text_case`** (sentence case by default). Never
  type ALL CAPS, Title Case or lowercase into the characters: capitalisation is presentation,
  applied by the text style's `textCase`. Exceptions: config § Writing → `case_exceptions`.
- **Every text node carries a text style.** No raw font, size or weight overrides. If styles
  bind their font to variables, the font follows the brand and theme; picking one by hand
  defeats that.
- **Auto-layout gap and padding bind to spacing tokens**, never raw numbers. Gap and padding can
  be separate scales that resolve differently at the same tier name: check
  `design_foundations` § Spacing before binding.
- **Deprecated tokens** follow config § Tokens → `deprecated_name`. Never bind anything new to
  one.
- **Downstream files see published values only.** After a token or library change, pause and ask
  config § Roles → `publisher` to publish before checking the result elsewhere.

### Token values

- **Check contrast before finishing any token value change**, against config § Accessibility →
  `standard`, for every combination in `contrast_must_pass`. Brands in `contrast_latitude` should
  be made passable, not blocked on.

### Reports and reviews

Follow config § Writing → `report_style`: concise, concerns first, lists over prose, and always
say what you deliberately did not change, and why.
