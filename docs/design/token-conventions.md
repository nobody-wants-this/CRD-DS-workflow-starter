# Token conventions

The rules for **changing or adding tokens**: which tier a token belongs in, what it's called, and
who decides. For designing *with* the tokens (what to bind, spacing, text styles), see
`design-foundations.md`.

The starter tokens are deliberately simple, with gaps. When your component needs something that
isn't there, add it by these rules.

## The three tiers

Each tier aliases the one before it.

| Tier | Collection | Modes | Holds | Named by |
|---|---|---|---|---|
| **Primitives** | Primitives | Value | Raw values: colour ramps (brand ones too), alpha ramps, fonts and weights, spacing, sizes, radii, border widths | What it *is*: `color/violet/600`, `spacing/16`, `font/family/poppins` |
| **Semantic** | Semantic | Light, Dark | What things are for: backgrounds, ink, borders, focus, fonts, and the spacing, size and radius scales | What it's *for*: `ink/base/error/default`, `spacing/md` |
| **Component** | Component | Light, Dark | Detail that only one component, or one family of components, needs. Empty to start | The component set: `button/base/primary/hover/background` |

- **Components and text styles bind Semantic or Component tokens. Never Primitives.** A binding
  straight to a primitive gets left behind when the system is restyled.
- **Raw values live only in Primitives.** Semantic tokens always alias a primitive. Component
  tokens alias any tier: a primitive, or a Semantic token.
- **Primitives carry no scopes** and are hidden from publishing, so nothing can bind them.

## Rebranding

Your brand is two things:

1. **The primitive values**: the brand ramps, fonts and radii.
2. **Where Semantic points**: accent uses `violet`, action uses `blue`, accent ink in Dark uses
   `yellow`.

Change either. The mappings needn't follow a pattern: a quieter brand could point accent at a
grey. **Names never change**, and components follow.

## Names carry no brand

Nothing in Semantic or Component names a brand, a hue or a brand ramp. Names say what a thing is
for (`accent`, `action`, `error`), never which colour fills it.

- ✅ `ink/base/accent/default`
- ❌ `ink/base/violet/default`, `background/structure/base/brand/subtle`

`primary` as a **button variant** (`button/base/primary/…`) is fine: it's an emphasis level and
an industry-standard name, not a colour.

## Semantic names

### Colour: element / context / intent / variant

`ink/base/action/hover` · `border/inverse/error/default` · `background/structure/base/accent/bold`

| Part | Values | Means |
|---|---|---|
| **element** | `background`, `ink`, `border`, `shadow` | Ink is text *and* icons. A background is either `surface` (the page itself) or `structure` (cards, panels, toasts that sit on a surface): `background/surface/…`, `background/structure/…`. Shadow is the colour of an effect style: see § Shadows |
| **context** | `base`, `inverse` | Inverse is the opposite of the theme, e.g. a tooltip. It mirrors base exactly |
| **intent** | `neutral`, `accent`, `action`, `error`, `warning`, `success` | What the colour says. Surfaces have no intent yet: `background/surface/base` |
| **variant** | `default`, `subtle`, `bold`; and `hover`, `pressed` | Hover and pressed exist only on action ink. Other interactive states live in Component sets |

Pairs that must exist, so a designer always has a combination that passes contrast. Designers
can still choose others.

- **Every background has ink of the same context and intent** that passes on it:
  `ink/base/error/default` on `background/structure/base/error/subtle`.
- **Every `bold` background has ink in the opposite context** that passes on it: `ink/inverse/…`
  on `background/structure/base/accent/bold`.
- **Every colour comes in both contexts.** Add a base token, add its inverse twin.

Not every combination exists. Add one when it's needed (§ Adding a token).

### Scales

T-shirt sizes.

| Scale | Steps (px) |
|---|---|
| `spacing/*`, gap **and** padding | `3xs` 2 · `2xs` 4 · `xs` 8 · `sm` 12 · `md` 16 · `lg` 20 · `xl` 24 · `2xl` 32 · `3xl` 48 · `4xl` 64 · `5xl` 80 · `6xl` 96 · `7xl` 128 |
| `size/*`, width and height | `xs` 16 · `sm` 20 · `md` 24 · `lg` 32 · `xl` 40 · `2xl` 48 |
| `radius/*` | `none` 0 · `sm` 4 · `md` 8 · `lg` 16 · `full` 9999 |
| `border-width/*` | `default` 1 · `bold` 2 |

### Shadows

`shadow/sm` · `shadow/md/contact` · `shadow/md/ambient`

One token per shadow layer, named for the effect style it serves (`sm`, `md`) and, where a style
has two layers, for the layer (`contact`: tight, `ambient`: wide). Scope `EFFECT_COLOR`.
Only the shadow's colour is a variable: offsets and blur stay on the effect style.

- **Colour and alpha are two aliases.** A Semantic colour variable can hold a colour variable
  *and* a separate alpha variable. `shadow/sm` = `color/neutral/1000` + `alpha/6`. The alpha is
  never baked into a colour primitive.
- **Alpha is its own Primitive ramp**: `alpha/{percent}`, FLOAT, 0 to 100 in steps of 5, plus the
  odd values the shadows need (6, 7). The value is a percentage: `6` means 6%. No scopes, like
  every Primitive.
- **Apply the Semantic token to the effect colour.** Bind `shadow/*` to the shadow's colour in
  the effect style. Don't bind a colour primitive and set the alpha on the effect: a bound colour
  overrides the effect's own alpha.
- **No inverse twin.** A shadow is dark in both contexts, so unlike other colours it has no
  `inverse` variant.
- **Light and Dark share the same values** to start. Shadows are barely visible on dark surfaces;
  point Dark at stronger alphas if your brand needs it.

**Any colour that needs alpha works this way** (scrims, overlays, tints): an alpha Primitive,
paired with a colour at the Semantic layer.

**Building it with the Plugin API.** The Plugin API typings don't document this. A colour
variable's value is an object with two aliases, not a single alias:

```js
variable.setValueForMode(modeId, {
  color:   { type: 'VARIABLE_ALIAS', id: colourVariable.id },
  opacity: { type: 'VARIABLE_ALIAS', id: alphaVariable.id },
})
```

Then bind it with `figma.variables.setBoundVariableForEffect(effect, 'color', variable)` and
assign the result to the style's `effects`. Read back `effect.color.a` to check: it should be the
percentage divided by 100. Extra keys such as `alpha` on an alias, and expressions, are rejected.

### Fonts and focus

- `font/family/{body|display}`, `font/weight/{regular|medium|semibold|bold}`.
- `state/focus/{color|width|offset}`. The focus indicator, on every focusable control.

### Extending Semantic

- **A new intent or variant: yes.** A meaning no intent covers gets a new intent, with its ink,
  border and background.
- **A new element: only for something background, ink and border can't paint.** They cover
  everything a component fills or strokes, icons included. `shadow` exists because an effect
  colour is none of those.

## Component names

`{set} / {context} / {variant} / {state} / {element}`

`button/base/primary/hover/background` · `control/base/checked/disabled/border` ·
`input/inverse/default/focus/border`

| Part | Values |
|---|---|
| **set** | A family of components that share tokens: `button`, `control`, `input`. One set can serve several components: checkbox, radio and toggle all use `control` |
| **context** | `base`, `inverse`, as in Semantic |
| **variant** | The component's own variants: `primary`, `secondary`; `checked`, `unchecked` |
| **state** | `default`, `hover`, `pressed`, `focus`, `disabled`… The states come from the CRD |
| **element** | `background`, `ink`, `border` |

**Element comes last here**, unlike Semantic, so all the parts of one state sit together in the
variables panel.

**Buttons, controls and inputs get a Component set by default.** They have too many states for
Semantic. Disabled is a state inside the set. **Buttons and controls never share a set**: controls
use colour differently (a checked box inverts its background), and forcing one set to do both
makes it unreadable.

## Adding a token for a new component

### Which tier

Ask in this order. Stop at the first yes.

1. **Does an existing Semantic token already do the job?** Bind it. No new token.
2. **Is it a button, a control or an input, or does it carry many states?** Component set.
3. **Could it be used more broadly, by things we haven't thought of yet?** Semantic.
4. **Is it holding complexity we don't want in Semantic?** Component.

**Off-scale values.** Stick to the scales. A value that isn't on one (an 18px box) needs a really
good reason. If there is one, it goes into Primitives first, and a Semantic or Component token
aliases it.

### Who decides

**The designer, for every new or changed token**, Semantic and Component alike. Claude recommends
a tier and a name, with the reason from the list above. The designer decides, and the decision
goes in the CRD's Design decisions with its reason.

### Doing it

1. **Name it** by the pattern for its tier.
2. **Alias it.** Raw values only in Primitives.
3. **Fill every mode**, Light and Dark. A Semantic colour gets its inverse twin.
4. **Scopes**: the narrowest that fits. Copy a sibling's.
5. **Description**: one line on what it's for, in the siblings' style.
6. **Code syntax** on Semantic and Component tokens: Web, `var(--{path with / as -})`, e.g.
   `var(--ink-base-error-default)`.
7. **Check contrast** in Light and Dark, per `accessibility.md`.

### Examples

**Button → a `button` set** (step 2). `button/base/primary/default/background`,
`button/base/primary/hover/background`, `button/base/secondary/disabled/ink`. Checkbox, radio and
toggle share a `control` set the same way.

**Tooltip → no new tokens.** `background/structure/inverse/neutral/default` with
`ink/inverse/neutral/default` already exist.

**A component with a meaning no intent covers → a new Semantic intent** (step 3). Other
components will want that meaning too.

**A checkbox box at 18px → `size/sm` (20).** Not worth an off-scale primitive.

## Before you finish

- [ ] The tier follows § Which tier, and the CRD records why
- [ ] The name follows its tier's pattern, with no brand or hue in it
- [ ] It aliases; any raw value is in Primitives
- [ ] Every mode is filled, and a Semantic colour has its inverse twin
- [ ] Scopes, description and code syntax are set
- [ ] Contrast passes in Light and Dark
