# CRD DS workflow starter

The start of your own design system repo, context first. You write a **CRD** (Component
Reference Document) for a component with Claude, then design the component in Figma using the
CRD as context.

Deliberately simple: just enough tokens and structure to design one basic component and learn the
workflow. There are gaps, so treat it as yours. Rename things, add tokens and modes, and record
why in the CRD. The rules for adding tokens are in
[`docs/design/token-conventions.md`](docs/design/token-conventions.md).

## What you need

- A GitHub account
- A paid Claude plan
- A Figma account that can edit files in your drafts
- Installed: Git, the GitHub CLI, Claude Code and its Figma plugin
  (`/plugin install figma@claude-plugins-official` in Claude Code)

## The Figma file

Open the [CRD DS workflow starter](https://www.figma.com/design/CgJ63UUJUYDdl3JpJzLr6H/CRD-DS-workflow-starter)
file, click the file name at the top, then **Duplicate**. Your copy lands in your drafts. Copy
its URL: setup asks for it.

## Quick start

1. Click **Use this template** → **Create a new repository**. Name it after your design system.
2. Clone your repo (not this one) and start Claude Code inside it:

   ```
   git clone https://github.com/YOUR-USERNAME/YOUR-REPO.git
   cd YOUR-REPO
   claude
   ```

3. Say: **"Set up the workflow kit."** Setup guides you from there.

Pick any basic component: one building block, not made of other components.

## The main prompts

- **"Write the CRD for {component}."** Claude researches the component, then interviews you.
- **"Review the {component} CRD."** A second opinion, concerns only. Best in a fresh session.
- **"Design the {component} component in Figma, from the CRD and my concept: {link}."**
  Sketch your concept first on your copy of the **❖ Your first component** page. Right-click
  the sketch, choose **Copy link to selection**, and paste it in place of `{link}`. Claude finds
  the CRD by name and follows your concept. No concept? Leave off "and my concept" and the
  link: Claude designs from the file's tokens and any components already in it.

## What's in here

```
CLAUDE.md                   points Claude at the kit's rules; add your own notes
.claude/
  CLAUDE.kit.md             the kit's rules
  design-system.config.md   your system's details (setup fills them in)
  skills/                   setup, CRD writing, review and update, component design
docs/
  crd/                      the CRD template and how CRDs work
  design/                   foundations, token conventions, accessibility rules, glossary
tokens/                     exported Figma tokens as JSON (later)
components/                 built components, one folder per component (later)
```

## Next steps

- Rebrand the starter file: change the brand colours, fonts and radii in Primitives, and where
  Semantic points. No token gets renamed
- Export your tokens into `tokens/`
- Build your component in code into `components/`

## Credit and licence

Made by Cassie Groos. MIT licensed.
