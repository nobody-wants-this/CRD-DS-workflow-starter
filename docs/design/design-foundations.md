# Design foundations

The facts `ds-design-component` and the CRD skills rely on. The skills carry the workflows.

- **Part 1, Your system** describes the starter Figma file. It's the same for everyone, so it
  comes filled in. When you change the file (new tokens, modes, styles), change this too.
- **Part 2, Working in Figma** is general Figma and Figma API knowledge for any system. Keep it,
  and add to it.

Rules for **changing or adding tokens** (tiers, naming, who decides) live in config § Paths →
`token_conventions`. Team facts (people, repo, page conventions) live in
`.claude/design-system.config.md`.

---

# Part 1 · Your system

## Files

One file does every job: your duplicate of the starter file, config § Figma →
`component_library`.

| Role | Holds |
|---|---|
| primitives | The Primitives collection: raw values, brand ones included |
| semantic | The Semantic collection: what components bind |
| component library | The Component collection, the components, icons and doc utilities |

## Token tiers

Primitives → Semantic → Component, by aliasing. Components bind **Semantic** or **Component**
tokens only, never Primitives. What belongs in each tier: `token_conventions` § The three tiers.

## Mode axes

| Axis | Collections | Modes |
|---|---|---|
| Theme | Semantic, Component | `Light` (default), `Dark` |

Figma sets modes per collection: **to show a frame in Dark, set both Semantic and Component to
Dark.** No brand or breakpoint axes.

Something already dark in Light, like a tooltip, uses the **inverse** context
(`ink/inverse/…` on `background/structure/inverse/…`): the opposite of the theme, so it flips
with it.

## Component tokens

The Component collection starts empty. Naming and when to add one: `token_conventions`
§ Component names and § Adding a token.

**Components bind `background/structure/*`, never `background/surface/*`.** Surface is the page
itself; a component bound to it is a defect.

## Naming

Lowercase, `/`-separated. The patterns and code syntax: `token_conventions` § Semantic names and
§ Doing it. Deprecated tokens follow config § Tokens → `deprecated_name`.

## Text styles

`Display`, `Heading lg|md|sm`, `Body lg|md|sm`, `Label md|sm`, `Caption`, `Overline`.

- Font family and weight bind to Semantic `font/family/*` and `font/weight/*`: `Display` and
  `Heading lg|md` use `font/family/display`, the rest `font/family/body`. **Never pick a font by
  hand.**
- Size and line height are fixed numbers, not variables.
- `Overline` uppercases via `textCase`. Type its text in sentence case.
- No monospace style.

## Spacing

**One scale for gap and padding**, `spacing/*` in Semantic, in px.

| Token | px | Use |
|---|---|---|
| `3xs` | 2 | Inside dense controls |
| `2xs` | 4 | An icon and its label |
| `xs` | 8 | A control and its label |
| `sm` | 12 | Padding inside compact components |
| `md` | 16 | Default padding inside components and cards |
| `lg` | 20 | Padding inside larger components |
| `xl` | 24 | Between related groups |
| `2xl` | 32 | Between sections inside a container |
| `3xl` | 48 | Between rows in a doc frame |
| `4xl` | 64 | Outer padding of a doc frame |
| `5xl`–`7xl` | 80–128 | Page-level gaps and margins |

Width and height bind `size/*` (`xs` 16 to `2xl` 48); corners bind `radius/*`;
strokes bind `border-width/*`.

## Effects and icons

- **Effect styles**, applied as styles, never rebuilt by hand:
  - `Focus ring`: keyboard focus only. A gap in `background/structure/base/neutral/default`, then
    a ring in `state/focus/color`. The spreads are fixed numbers (2 and 4), because Figma can't
    bind a sum: change `state/focus/width` or `offset` and update the style too.
  - `Elevation sm` (a menu) and `Elevation md` (a popover or dialog). The shadow colours bind
    `shadow/sm` (sm) and `shadow/md/contact` + `shadow/md/ambient` (md). Light and Dark share the
    same values, so shadows are barely visible on dark surfaces.
- **Icons** live on the Icons page as `icon/{name}`, 24×24, outline only.
  - **Sizes**: `size/md` (24) by default, `size/xs` (16) for small. No wrapper component: resize
    the instance.
  - **Recolour** the stroke on the `Vector` layer inside the instance, with an ink token.
  - **Search**: each icon's description lists what it's for ("Tick, done, selected, confirm").
  - No app or logo icons.

## System rules

Each rule, and the question it raises for every new component.

- **Single brand.** Nothing varies by brand.
- **Interactive states belong to Component sets**, except action ink's hover and pressed. *Which
  states does this component have, and do they need a Component set?*
- **Contrast passes in Light and Dark** (config § Accessibility).

## Design conventions

- **Text fits its container** in every variant, at the longest realistic content. It wraps or
  truncates; it never spills out.
- **Nested corners**: an inner corner's radius is the outer radius minus the padding between
  them, taken to the nearest `radius/*` token. At zero or less, the inner corner is square.
- **Accent lines on rounded corners**: a side stripe either reaches past the end of the corner
  curve (stripe width at least the radius) or the element has square corners. Never a stripe
  that stops partway round the curve.
- **Icon and label rows centre on the cross axis.**
- **Interactive targets are at least 24×24** (`docs/design/accessibility.md`).

---

# Part 2 · Working in Figma

## Before you finish a layout

The three failures that break layouts most often. Details are in § Working in Figma via MCP.

1. **Text sizing.** A new text node grows sideways forever. Inside a container, set it to fill
   the width and grow in height, or truncate it if it's a single-line label.
2. **Order of operations.** Append a node to its auto-layout parent before setting it to fill or
   hug.
3. **Look at it.** Screenshot every variant and doc frame (`get_screenshot`). Reading node state
   misses text spilling out, overlaps, clipped content and uneven padding.

## Finding tokens

**Use `search_design_system` against the published library.** The Plugin API
(`getLocalVariablesAsync`, `getVariableByIdAsync`) only sees variables already imported into the
current file. It can't see unpublished or unused library tokens, so it reports a token as missing
when it merely hasn't been imported.

**Don't use an exported token JSON to decide what exists.** It's generated downstream for code,
lags until the library is republished and re-exported, and may be keyed on code syntax rather than
the variable name. For design work, the published library is the authority.

**Reading a resolved value:** `variable.resolveForConsumer(node)`. `valuesByMode` returns the
alias, not a number.

## Publish gates

**Consumers only see PUBLISHED values.** After editing tokens, the source library must be
published before a downstream file's imports, bindings or demos reflect the change.
`importVariableByKeyAsync` fails outright on an unpublished variable, and "not found" almost always
means needs-publish, not wrong key. Each tier is its own gate: publish them in aliasing order.

Only config § Roles → `publisher` publishes. **Pause and ask at every gate.**

## Building layouts

These apply to everything built in Figma.

- **Auto-layout everywhere.** Pages, screens and components are built from auto-layout frames.
  Absolute positioning is rare and deliberate: a badge pinned to a corner, an overlay.
- **Never use a spacer frame**: no empty frame with `layoutGrow = 1`, and no fixed-size empty
  frame to push things apart. A spacer leaks into every instance and into dev handoff, needs
  maintaining across variants, and hides the layout intent. Use the real mechanism:
  `primaryAxisAlignItems`, `FILL` on an element that genuinely fills, absolute positioning with
  constraints, padding, or moving a visibility binding. If no clean mechanism exists, say so and
  ask.
- **Clip content only where it's genuinely needed**: a screen root, a scroll viewport, a frame
  whose content really overflows, or a rounded frame with full-bleed children. Elsewhere, clipping
  hides overflow rather than fixing it: the content is still there, still wrong, still in the
  accessibility tree. It also cuts shadows.
- **Never fake an edge-docked panel with uniform padding.** A side panel or bar that docks to an
  edge is its own frame in the layout, not content pushed inward by padding on the root.
- **Strip the defaults on every frame you create.** `figma.createFrame()` and
  `figma.createAutoLayout()` ship a solid white fill and clip content on. Set `frame.fills = []`
  and `frame.clipsContent = false` immediately. A frame keeps a fill only when it's bound to a
  token.
- **Every gap, padding and size binds to a token**, never a hardcoded number (§ Spacing).

## Working in Figma via MCP

Facts only. Procedures (which keys, what order, how to recover) belong in the skills.

**Any claim here about what the Figma API cannot do is a dated observation about someone else's
product.** Write the date, and re-test before trusting it.

### Reads and writes

- **A rejected or interrupted `use_figma` write may already have landed.** A call reported
  "rejected by user" had in fact fully run (2026-09-25). Re-read the file state before re-running
  anything, say what actually landed, and guard mutations with a verify-it-exists check so a
  re-run fails safely.
- **A script is transactional.** One thrown error rolls back everything, so put read-back
  reporting in a separate call.
- **Reads straight after a mutation are stale within the same script.** `defaultVariant` still
  reported the old variant right after a reorder; a fresh call showed the truth. Verify read-back
  in a separate call, not just on error. *(2026-09-09)*
- **`get_metadata`'s page list includes only LOADED pages**, without saying it's partial. It
  reported 2 pages in a 63-page file. `figma.root.children` inside `use_figma` returns them all,
  and child counts on an unloaded page also read low: load the page before trusting a count.
  *(Re-test: on 2026-11-25, called without a node id, it listed all 10 pages of the starter file.)*
- **`importComponentSetByKeyAsync` returns the PUBLISHED mirror, not the editable node.** The same
  set exists twice: a local node on its page (`remote: false`, writable) and a library mirror
  (`remote: true`, no parent, read-only) sharing one key. Read published state from the mirror;
  write to the local node, and **check `.remote` before any mutation**. *(2026-09-09)*

### Variables and paints

- **Bind paint colour with `setBoundVariableForPaint`.** `setBoundVariable` doesn't work for
  paints.
- **Imported-but-unused variables get pruned between scripts.** Import and bind in the same
  script, and null-check before binding.
- **A paint binding can die silently between scripts.** The binding stays (`boundVariables`
  shows the alias, `resolveForConsumer` returns the right value) but the paint renders nothing.
  **The tell: `paint.color` reads `{r:1,g:1,b:1}` instead of the resolved colour.** At risk:
  variables not already used elsewhere in the file. Re-assigning the fill or stroke does not fix
  it; **create the node fresh in the script that imports the variable**, or clone a good node.
  Verify by rendering (`get_screenshot`), not by reading state: `absoluteRenderBounds` and an
  in-script `node.screenshot()` both missed it. *(2026-09)*
- **Effects bound through an effect style show the style's internal variables** in
  `effects.boundVariables`, which can look like a forbidden lower-tier binding. **Read
  `node.effectStyleId` before judging an effect.**

### Text

- **New text nodes grow sideways forever.** `figma.createText()` defaults to `textAutoResize =
  'WIDTH_AND_HEIGHT'`, so in a fixed or fill container the text runs out of it. Set
  `layoutSizingHorizontal = 'FILL'` and `textAutoResize = 'HEIGHT'` so it wraps. A single-line
  label in a fixed-size component: `textTruncation = 'ENDING'` and `maxLines = 1`.
- **Load the font before writing text.** `loadFontAsync` the node's font before setting
  `characters` or any font property, or it throws.
- **Apply the text style; never reproduce it by hand.** Hand-matched text has no style binding,
  so it goes stale silently.
- **`setTextStyleIdAsync()` changes the node's resolved font.** Before writing any
  font-dependent property afterwards (restoring `textDecoration`, say), `loadFontAsync` the NEW
  font, or it throws `Cannot write to node with unloaded font`.
- **A hugging text node with `maxWidth` wraps, but the box stays at `maxWidth`.** Whatever follows
  it in the row (a required-field asterisk, say) pins to the end of the box, not to the last word.
  *(2026-10)*
- **Switching modes or editing instance text can need fonts you haven't loaded.** On an
  "unloaded font" error, parse the family and style from the message, load it and retry.

### Component sets and auto-layout

- **Sizing modes need the parent first.** `layoutSizingHorizontal` or `layoutSizingVertical =
  'FILL'` throws on a node that isn't yet a child of an auto-layout frame, and `'HUG'` works only
  on auto-layout frames and text. Append, then set sizing.
- **A set's default variant comes from canvas POSITION, not child order.** The default is
  whichever variant sits top-left. *(2026-09-09)*
- **Sets can be `GRID` auto-layout**, where `x`/`y` are computed and can't be written. Place with
  `child.setGridChildPosition(row, col)`, called on each child, not on the set. The readable anchors are `gridRowAnchorIndex` and
  `gridColumnAnchorIndex`; **`gridRowStart` / `gridColumnStart` throw on read**. The setter
  throws on an occupied cell, so swapping cells in a full grid needs `gridColumnCount` (or
  `gridRowCount`) raised temporarily to park in. *(2026-09-09)*
- **Grid children ignore the cell.** Auto-layout children dropped into a GRID frame hug, and
  `FILL` is rejected. Pin the tracks to fixed sizes and resize the child to the cell.
  *(2026-09-16)*
- **Variants of mixed sizes need uniform FIXED tracks.** With HUG or flex tracks, the grid sizes
  each track to its biggest variant and moves the others as they change. Set every
  `gridColumnSizes` and `gridRowSizes` entry to FIXED, sized to the largest variant.
  *(2026-10)*
- **`resize()` on a FILL or HUG child silently flips it to FIXED.** Restore the sizing mode
  after resizing.
- **`layoutSizingHorizontal` reads FIXED on a hidden node**, even straight after setting it to
  FILL, and the setting may not stick. Set `layoutAlign = 'STRETCH'` while the node is visible,
  then hide it. *(2026-10)*
- **`rescale()` scales text with the artwork**, so it shrinks type too. Prefer `resize()` on
  auto-layout instances; keep `rescale()` for things that must stay proportional.
- **`SPACE_BETWEEN` centres a lone child.** When a boolean prop hides a child, Figma drops it
  from the *instance's* children entirely, so a two-child `SPACE_BETWEEN` row becomes one child,
  centred. The library variant looks fine because the child is visible there. **Fix: bind the
  visibility to the artwork *inside* a fixed-size wrapper**, so the wrapper always holds its
  place. Clear an existing binding with `componentPropertyReferences = {}`; `null` throws.
  *(2026-08-28)*
- **`resize()` doesn't refit a GRID set.** After converting a set to GRID or placing children, the
  frame keeps its old size and squeezes flex tracks. Set `gridColumnSizes` and `gridRowSizes` to
  HUG (FIXED when variants differ in size, above), then `resize()` explicitly to padding×2 + Σ track sizes + gaps.
- **GRID stretches variants to the row height.** Set every variant's `layoutSizingVertical =
  'HUG'` after converting, and again after any `resize()`. Rows beyond `gridRowCount` collapse to
  zero height, so the set must hug its height.
- **Auto-flow grids.** Some GRID sets have `gridAutoTracks = 'ROWS'` (missing from the bundled
  typings, so probe the live node). Symptoms: setting `gridRowCount` throws "cannot set … while
  GridAutoTracks is set", and `setGridChildPosition` throws "automatically positioned items — use
  parent.insertChild()". `gridAutoTracks = 'NONE'` unlocks the counts, but items may stay
  auto-positioned; then cell = layer index, row-major. Set `gridColumnCount` first, sort the
  children into row-major order, and `insertChild(i, child)`.
- **Variant option order can be fixed by renaming.** `editComponentProperty` rejects
  `variantOptions`, and reordering children doesn't change the picker, but renaming each value away
  and back, in the order you want, does. *(2026-10)*
- **Grid geometry keeps reflowing between scripts** after big variant additions, so geometry read
  in the script that added variants is stale. Position labels around a set in a fresh script, and
  verify them in another. Screenshot the bottom of the set, not just the top.
- **A single override on a nested node can't be reset by script.** `resetOverrides()` exists
  only on instances and strips *all* their overrides. Writing the main component's value back
  doesn't clear the override flag and can bake in different behaviour (an auto gap becomes a
  literal `0`). Set what renders correctly, then hand the person the one-click fix: right-click
  the layer → **Reset changes**. *(2026-08-28)*
- **`resetOverrides()` misses some overrides**: values set with `setProperties`, and content
  inside a nested instance's own slot. Set those back to their defaults explicitly. *(2026-10)*
- **Renaming a main component doesn't rename its instances' layers**, or nested instances' names.
  They keep the old name until a script renames them. *(2026-10)*

### Cloning and swapping

- **Cloning a variant within a set strips all `componentPropertyReferences`**: text `characters`,
  `visible` and `mainComponent` swap refs, silently. The set-level property definitions survive, so
  the damage is invisible until a consumer selects the new variant and the props vanish from the
  panel. After every clone batch, copy refs from the source variant by layer path or name (merge
  only what's missing: `{...want, ...current}`), then check ref-count parity per variant. Cloning a
  standalone COMPONENT, or a whole set, preserves refs.
- **Cloning a variant within a set degrades its SLOT nodes to plain FRAMEs** and strips
  `slotContentId` and `visible` refs. Cloning a standalone COMPONENT keeps slots, and
  `combineAsVariants` re-homes the slot property onto the new set. **Recovery:** set
  `componentPropertyReferences = { visible, slotContentId }` on the degraded frame using the set's
  *existing* slot property ids, and Figma re-promotes it to a SLOT. It still reads FRAME in the same
  script; verify in a fresh call. If the slot property doesn't exist on the set yet, one manual
  Convert-to-slot creates it.
- **Cloning within a set copies Dev Mode annotations.** Strip them from the clones so an annotation
  stays on one variant.
- **`swapComponent` keeps instance overrides.** Stale override bindings (old fills, effects) sit on
  top of the new main component and keep the old look. Call `resetOverrides()` after a swap where
  the target's own design should win, then reapply deliberate overrides.
- **Text with per-range fills** (`boundVariables.textRangeFills`): assigning `node.fills` doesn't
  clear the range binding. Load the fonts, then `setRangeFills(0, characters.length, [paint])`.
- **`setBoundVariableForPaint(paint, 'color', null)` silently unbinds** when a pruned variable
  resolves to null: a raw fill, no error. Null-check every resolved variable and throw if missing.

### Slots

- **SLOT properties can't be set with `setProperties`.** It throws `Slot component property
  values cannot be edited`. The slot is a real child node (`type: 'SLOT'`) that accepts
  `appendChild`: add content structurally. *(2026-09-16)*
- **A variant change on one slot child can invalidate sibling node references** for the rest of
  the script, even with fresh lookups. Configure instances before appending them, or split the
  work across calls.
- **A hidden child disappears from an instance's `.children`.** Never iterate by a captured
  index; re-query after each mutation.
- **`FILL` on slot children must be set in a later call** than the slot surgery.

### Annotations and descriptions

- **Annotations are writable via MCP**: `node.annotations = [{ labelMarkdown }]` *(2026-09-09)*.
  They surface in **Dev Mode**, so they're for developers and dev agents.
- **Notes a designer needs** at insert time go in the component set description instead. It's
  also how a note reaches a dev agent: `get_design_context` returns the description (and dev
  resource links, below), not Dev Mode annotations.
- **An agent can't post Figma comments.** Draft the comment, with the node link, for a person to
  paste. *(2026-10)*
- **Dev resource links can't be written by an agent.** `use_figma` blocks them both ways, so a
  person adds them. An agent reads them via `get_design_context` on a variant, under
  **Documentation**; nested components' links don't come through. *(2026-10-06)*

### Motion

- **A keyframe's easing controls the segment going *into* it**, so the first keyframe's easing is
  never used. Each animation goes on an instance in its own top-level motion frame,
  never on the set (`ds-design-component` → `documentation-frames.md` § Motion frames). *(2026-10)*

## Where to go next

| Task | Skill |
|---|---|
| Setting up the kit, or checking the setup | `ds-setup` |
| Writing a CRD | `ds-crd-write` |
| Reviewing a CRD, or its Figma page against it | `ds-crd-review` |
| Updating a CRD after design or build | `ds-crd-update` |
| Designing or extending a component in Figma | `ds-design-component` |
| Tightening the prose of a doc | `ds-simplify-prose` |

Adding or changing a token: `token_conventions`.
