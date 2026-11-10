---
name: responsive-layout
description: >
  Applies layout structure and spacing variables to frames within a user-specified Figma section.
  Handles auto-layout enforcement, frame/margin bindings on section-level frames, content wrapper
  creation, w-max/w-min/FILL on content wrappers, px-value/* token binding on padding and gap,
  nested FILL sizing, row wrapping, column minimum widths, and root Responsive modes.
  Validates behavior at Desktop, Tablet, and Mobile widths before claiming responsive readiness.
  Guides two phases: prepare and review responsive source frames, then create and validate
  scaled viewport designs after the user approves the prepared sources and authorizes scaling.
  Use this skill when asked to: "apply frame variables", "tokenize layout", "fix frame structure",
  "make frames responsive", "apply spacing tokens", or "run responsive-layout on this section".
  Do NOT use for typography or color — those are separate skills.
---

# responsive-layout

Applies layout structure and spacing variables to a user-specified section of a Figma file.
Identify scope first, then discover variables before applying bindings. Never guess variable
names or IDs. Preserve intentional layout exceptions and report unresolved dependencies.

## Two-phase workflow

**Phase 1 — Prepare responsive source frames.** Apply the layout and token steps below, then
validate responsive behavior using temporary copies. Preserve the sources at their original
widths/modes. Temporary validation copies are not scaled deliverables.

**Review checkpoint.** Present the prepared sources, validation results, and unresolved issues.
Ask the user to approve the responsive sources before Phase 2. A request to run this skill or
scale designs does not by itself approve the result of Phase 1. If the user requests revisions,
make them and repeat affected checks before returning to review. Do not retain scaled versions
while this approval is pending.

**Phase 2 — Create and validate scaled designs.** After source approval, guide the user into
scaling using the procedure at the end of this skill. Source approval alone does not authorize
new deliverables: if scaling is already requested, proceed without asking again; otherwise ask
whether to create the target viewport versions. A single clear response may approve the sources
and authorize scaling. Honor preparation-only requests or a declined scaling offer.

Track which source frame IDs were approved, any accepted limitations, and the authorized scaling
targets in the conversation or workflow state outside the Figma file. If relevant source changes
invalidate that approval, revalidate and return the changed sources to review.

---

## Component protection and permitted changes

Protect INSTANCE nodes and their descendants from general layout/token edits. Do not detach
instances or edit main components. Check each node and its ancestors before mutation.

Two actions are permitted within the approved section:

- Reparent an instance into `content` during Step 2d, preserving its properties. Undo the move
  if Figma automatically changes its sizing, clipping, or inherited variable context.
- If an instance exposes a `Viewport` variant with a verified matching Desktop/Tablet/Mobile
  option, set that property to match its full viewport root. Use exposed properties at protected
  instance boundaries; do not traverse internal instances just to find additional variants. Preserve other properties,
  overrides, and the component link. Do not set a child Responsive collection mode to achieve this.

For component changes beyond the two permitted actions above, ask for specific approval before
editing. If it is uncertain whether a change could affect the component's internal layout or
relationship to its parent, preserve it and ask rather than guessing. Complete independent work
while awaiting the answer.

Explain the request in plain language, naming the affected component copies and exactly what
would change, why it is needed, and any uncertainty about the effect. Distinguish changes to the
component's outermost layer (the instance root, such as setting the whole field to Fill width)
from changes to layers inside it (such as its label, icon, or text wrapping). Batch related
requests when their scope is clear. Approval covers only the named components and changes;
approval for outer sizing never extends to internal layers. Do not proceed on silence.

Existing explicit approval for the same changes remains valid; do not ask again unless the scope
or proposed change differs. Approval to run the skill, approve sources, or scale frames is not
blanket component permission. Automatic Viewport matching is a separate permitted action above.

Stop layout/token traversal at protected instance boundaries. You may inspect exposed root
properties and variant definitions, and collect fonts read-only for the font preflight. Inspect
internal layout only after approval for the relevant diagnosis/fix. Apply approved exceptions
only to the specified instances/properties, verify read-back and appearance, and record them in
the review summary. If an override is unsupported, preserve the component and report the blocker;
do not bypass it through detachment or main-component edits.

---

## Step 0 — Discover and Verify Variables

Resolve identities at runtime from the intended design system. Never hardcode IDs, select by
name alone, or treat variables with equal pixel values as interchangeable.

### Discovery

1. Inspect bindings on approved reference frames from the same brand/design system. Existing
   bindings are evidence, not proof of correctness.
2. Enumerate available local variables/collections and discover accessible library variables
   using the connected tools. Include appropriate unused tokens; bindings already present in
   the file are not a complete library inventory.
3. Verify the source collection/library. Import remote variables by verified library key when
   required for binding; distinguish stable keys from file-local IDs.

Read-only discovery may inspect references outside the target. Never mutate those references.
If discovery is incomplete or unavailable, state the limitation; do not label a partial scale complete.

### Identity and effective values

Record each candidate's name, runtime ID, key when available, collection ID/name, source library,
resolved type, intended role/property, alias chain, and resolved values in intended modes.
Padding, gaps, and width constraints require numeric variables and a suitable semantic role.
Resolve aliases through the effective modes of their own collections; mode IDs are not shared
between collections. Missing or cyclic aliases remain unresolved dependencies.

Verify that `frame/margin` is the approved outer page gutter, `width/w-max` caps the main
`content` wrapper, and `width/w-min` (327px in this project) supplies the minimum for that wrapper
and eligible nested responsive columns. Verify equivalent names from the library; do not invent
a variable named `min-width` or hardcode 327px in place of its binding. Identify the actual Responsive
collection and Desktop/Tablet/Mobile mode IDs rather than assuming them from a token name.

The project uses Bootstrap outer page gutters: `frame/margin` should resolve to Desktop 24px,
Tablet 26px, and Mobile 24px. Verify these values in the live library; do not replace the binding
with hardcoded padding. If they differ, report the discrepancy before applying that token.

Build a `px-value/*` lookup for each effective mode, retaining identity, role, and resolved value.
Names are step indices, not pixel counts. Include all discoverable eligible tokens. Resolve
same-value ties by approved role and library, never discovery order. If the correct source or
required variable remains ambiguous, preserve affected properties and ask for the missing choice
or reference; do not invent a substitute.

### Existing bindings and width constraints

Audit existing bindings using the same identity and semantic checks. Preserve valid bindings.
Plan corrections to established mismatches only when their replacements are verified and in scope;
apply them in the corresponding binding step after establishing the root mode. Report
ambiguity without replacing it. This audit precedes Step 2e's skip rule for already-bound properties.

Check `w-min <= w-max` in intended modes and that minimum width fits the available content
width after root/section padding. Report conflicts rather than forcing widths or inventing tokens.

---

## Step 1 — Identify the Target Section

Wait for the user to specify the section. Use the Figma MCP to locate it, switch to the correct
page, and list all direct children of the section before proceeding.

Distinguish the Figma SECTION container, viewport root FRAME(s), and page section FRAME(s).
Section-level frames are direct content sections of a viewport root, not every arbitrary FRAME.
Record the approved SECTION ID and authorized source root IDs. All persistent node mutations,
validation copies, and scaled deliverables must stay inside that section. Never modify neighboring
sections or unrelated frames. Reference and library discovery outside the section remains read-only.
If a tool creates a clone at page level, immediately insert that newly created clone into the
approved section in the same operation and verify its actual parent ID before other edits.
If placement fails, remove only that new clone and report the blocker. Verify local coordinates
and section bounds; a frame visually near the section is not proof of section membership.

Capture a before screenshot and record bounds, sizing, bindings, and effective modes for the
authorized targets. Record intended width/mode. Existing explicit user instructions authorize
the task; ask only when scope or layout intent remains unclear.

If only a nested section is authorized, changing an ancestor root's mode/layout requires the root
to be included in scope. Report that dependency instead of silently expanding the target.

---

### Font preflight — before mutation or cloning

Verify that required font families/styles can load in the active editing environment, including
mixed text runs and text inside components affected by cloning/reparenting. This is read-only
font discovery, not permission to edit protected descendants. Preserve fonts, text content, and
font-variable bindings.

A remote editing service may lack fonts that are installed locally. If loading fails, check an
available, correctly connected Desktop Bridge and verify the exact font loads there before
continuing. Do not describe a remote font failure as a missing font on the user's computer.
Never substitute fonts without explicit approval. If no available environment can load them,
report affected operations as blocked and continue only work that does not depend on them.

---

## Step 2 — Layout & Spacing Tokens

### Layer hierarchy — understand this before touching anything

```
Root frame (1920 / 768 / 375px)
  ├─ Header (INSTANCE)              ← protect internals; match exposed Viewport variant
  ├─ Hero banner (FRAME, full-width) ← descriptive shell: outer gutter + top/bottom padding
  │    └─ content (FRAME)           ← content wrapper: gets w-max / w-min / FILL
  │         ├─ left-section (FRAME) ← child content: 2e + 2f apply here
  │         ├─ image (FRAME)        ← child content
  │         └─ CTA (INSTANCE)       ← protected; lives inside content
  ├─ Offers section (FRAME, full-width) ← descriptive shell
  │    └─ content (FRAME)
  └─ P1 Footer (INSTANCE)           ← protect internals; match exposed Viewport variant
```

---

### 2a. Responsive Mode on the Full Viewport Frame

After discovery and before layout/token mutations, apply the Responsive collection mode to the
**full viewport root frame only** using `setExplicitVariableModeForCollection`. Never explicitly
assign Desktop/Tablet/Mobile mode to individual shells, `content` wrappers, nested frames, or
instances. They inherit from the full frame.

| Root frame width | Mode to apply |
|---|---|
| 1920px | Desktop |
| 768px | Tablet |
| 375px | Mobile |

Use the verified collection and actual mode IDs from Step 0. For other widths, use an established
project breakpoint mapping or ask; do not infer a mode by proximity. If the root is outside the
authorized scope and does not already supply the intended mode, report that dependency before
binding mode-dependent properties.

Check effective inheritance on eligible descendants. Preserve intentional explicit overrides and
report them. Clear an override only when established as incorrect and in scope, so the node can
inherit; do not replace it with another explicit mode. Never change overrides on protected
instances or descendants without the specific approval described above. Report visible
outer-instance conflicts without inventorying internals. Separately audit
exposed `Viewport` variants: discover exact option values and match the root breakpoint under
the component rule above. A Responsive collection mode does not automatically select a component
variant. Report missing/ambiguous options instead of guessing or swapping unrelated variants.
On Phase 1 validation copies, use the matching variant for each tested breakpoint; keep the
prepared source matched to its original viewport.

After the root mode is established, resolve actual effective values for spacing matching and width
checks. Re-resolve values for nodes moved into `content`. A root mode change may affect protected
instances through inheritance even without direct edits; include that in visual verification.

---

### 2b. Auto-Layout Enforcement

Establish each eligible frame's role first: flow container, control, media, or intentional
positioned/decorative composition. Only enforce auto-layout on established flow containers.
Preserve intentional artwork, overlays, and media crops, and report layout exceptions.

**If auto-layout is already present:** preserve its initial direction and spacing during this
step, then evaluate its sizing and wrapping in Step 2f. Existing HUG, FIXED, and NO_WRAP settings
are inputs to that audit, not evidence that the frame is already responsive.

**If auto-layout is missing**, detect orientation from child positions:

| Child arrangement | Apply |
|---|---|
| Children share the same Y (side-by-side) | Horizontal auto-layout |
| Children share the same X (stacked) | Vertical auto-layout |
| Single child | Use the direction established by the layout role or an approved reference |
| Direction remains unclear from the layout and approved references | **Prompt the user** |

When prompting:
> "Frame **[name]** has no auto-layout and child arrangement is unclear.
> Should I apply **horizontal** or **vertical** auto-layout?"

A single child alone does not require confirmation. Ask only when the direction cannot be
established from the layout or an approved reference. If a question is required, wait for the
user's answer before applying auto-layout to that frame.
Preserve all existing padding and gap values — do not change spacing at this step.

---

### 2c. `frame/margin` on Section-Level Frames

Apply `frame/margin` ONLY to the **left and right padding that establishes the outer page gutter**
between the full-width shell edge and its available content area. It resolves to Desktop 24px,
Tablet 26px, and Mobile 24px.

Identify the outer gutter owner by layout role, not frame name or direct-child position alone.
The descriptively named outer shell (for example `Hero banner`) owns this gutter and its
section-level top/bottom padding. A banner's internal padding, card inset,
content-wrapper padding, or other internal spacing must NEVER receive `frame/margin`.
If an existing outer shell already supplies the gutter, do not apply it again to nested content.
A banner may have an outer page shell and an inner padded banner inside `content`; only the
outer page gutter qualifies. Bind outer shell top/bottom padding to appropriate spacing tokens
under Step 2e, never to `frame/margin`. Internal padding uses other appropriate spacing
variables, even when its pixel value happens to match `frame/margin`.

Rules:
- Viewport roots keep a FIXED target width (for example 1920 / 768 / 375px).
- Ordinary flow sections use horizontal FILL when their auto-layout parent supports it. Verify
  root padding permits the intended full-width section behavior.
- Do not convert the root to auto-layout solely to enable FILL without establishing layout intent.
  If the parent cannot support FILL, preserve intentional constraints or explicitly size sections
  to available root width. Report fixed-width dependencies: later frame creation must resize them.
- Preserve deliberate fixed-width exceptions rather than treating every FRAME as a full-width section.
- Do **not** apply `frame/margin` to internal padding, including banners, cards, and nested content
- Do **not** apply `frame/margin` to the root frame itself
- Do **not** apply `frame/margin` to top or bottom padding on any frame
- Do **not** apply to any INSTANCE (component protection rule)

```
Root (1920px)                   ← skip
  ├─ Header (INSTANCE)          ← skip
  ├─ Section (FRAME, 1920px)    ← ✓ frame/margin → paddingLeft + paddingRight
  ├─ Section (FRAME, 1920px)    ← ✓ frame/margin → paddingLeft + paddingRight
  └─ P1 Footer (INSTANCE)       ← skip
```

---

### 2d. Content Wrapper: `w-max`, `w-min`, FILL

Each ordinary section has a descriptively named outer shell with one `content` wrapper that
holds all its content frames/elements. The shell owns outer `frame/margin` padding and section
top/bottom padding; `content` owns the min/max width bindings. Keep any internal banner padding
on the appropriate nested content layer using internal spacing tokens.

Use `content` as the wrapper name after verifying its role. Preserve the outer shell's descriptive
name. Only retain decorative/full-bleed siblings outside `content` when an established layout
requires that exception; report it rather than guessing.
Bind `maxWidth` to the verified `w-max` and `minWidth` to the verified `w-min`;
apply horizontal FILL where supported.

A max-width cap may leave extra horizontal space between `content` and the shell's padded edges.
This is expected, not an error. Center the capped content unless the approved design specifies
another alignment. Do not increase max width, change `frame/margin`, or add a gap/spacer to remove
this space. Only flag invalid constraints, unintended alignment, or actual overflow.

Outer gutter padding and the visible viewport-to-content inset are different measurements.
The gutter remains the live `frame/margin` value (24 / 26 / 24). Within those padded edges,
`content` fills available width until capped by `w-max`, with `w-min` as its lower constraint.
When capped, centering supplies extra space on each side. As the viewport shrinks, that extra
space naturally decreases and content follows the available width within its constraints.
Never derive a fixed Desktop padding from the current content position, create a token for that
inset, or change the library gutter to reproduce it. Verify inherited Responsive modes separately;
fluid sizing responds to width changes within the active mode.

**Recognize the wrapper by role, not child count or name alone.** Confirm that it groups the
section's flow content and owns content width/alignment. A lone image, card, or control is not
necessarily a wrapper. Reuse valid wrappers; a second run must not nest duplicate wrappers.

**Existing wrapper:** retain intended alignment, distribution, padding, gaps, and content order.
Preserve its initial direction/height behavior, then evaluate wrapping and sizing in Step 2f.
Apply verified width bindings and horizontal FILL where valid. Center the
capped wrapper on the section's horizontal axis when the approved layout requires it; preserve
intentional left/right alignment.

**New wrapper:**
1. Record flow order, positions, sizing, alignment, wrapping, distribution, and gaps. Separate
   flow content from intentional absolute/decorative/full-bleed layers.
2. Create a transparent auto-layout FRAME named `content`, with zero padding and
   `clipsContent = false`. Transfer the intended flow layout, not just direction and gap.
3. Move only intended flow children into it, preserving order. INSTANCE roots may be reparented
   under the component protection rule; do not add unrelated overrides.
4. Keep outer left/right gutter and section top/bottom padding on the descriptive shell. Move
   the original content-flow direction, gaps, wrapping, and distribution into `content`. Configure
   the shell to contain and align that single wrapper; preserve its background and intended
   height behavior. Do not leave content distribution duplicated on the shell. Set shell layout
   properties only as needed for the verified wrapper structure; ask if intended alignment is unclear.
   Keep nested banner/card padding on its own content layer. Avoid duplicating padding or spacing.
5. Bind verified width constraints and apply horizontal FILL where supported. Preserve intended
   horizontal placement when max width takes effect.
6. Compare bounds and appearance before/after. Undo a move that unexpectedly changes instance
   layout, clipping, or inherited variable context, and report the dependency.

**Wrapper with siblings:** move only siblings established as flow content. An INSTANCE sibling
is not automatically misplaced. Preserve deliberate backgrounds and full-bleed layers.

> Outer section shells do not receive these content width constraints. Bind `w-max` on the
> main `content` wrapper; bind `w-min` there and on eligible nested responsive columns (Step 2f).

---

### 2e. Vertical Padding & Gap → `px-value/*` Tokens

For all non-INSTANCE auto-layout frames at any depth, bind:
- `paddingTop` → matching `px-value/*`
- `paddingBottom` → matching `px-value/*`
- `itemSpacing` (gap) → matching `px-value/*`
- `counterAxisSpacing` (row gap) on wrapping frames → matching `px-value/*`

This step covers vertical padding, item gap, and wrapped-row gap. Step 2c handles left/right padding
ONLY at the outer page gutter. Internal horizontal padding is not covered by Step 2c and must
never use `frame/margin`; preserve its existing appropriate spacing bindings. If internal horizontal
tokenization is requested, use verified internal spacing variables, not the outer gutter token.

**Matching rule: exact 1:1 values only.** Bind a verified, semantically suitable variable only
when its resolved numeric value equals the raw value in the node's effective mode. For example,
16px may bind to `px-value/4` if it resolves to 16px; raw 17px must not be rounded to 16px or 20px.
Variable names are step indices, not pixel counts; use the Step 0 lookup rather than guessing.

If there is no exact eligible match, leave that property unchanged and flag it for manual approval.
Record the layer, property, raw value, effective mode, and available suitable candidates with their
resolved values. Batch unmatched values into one review request while continuing independent
exact matches. A proposal or lack of response is not approval to change the value or binding.
Do not create new tokens or silently normalize spacing.

Skip properties with value 0 and valid existing bindings (after the Step 0 audit). Skipping one
property must not skip other eligible properties on the same frame. If the mode or token identity
is unresolved, leave the affected property unchanged and report the dependency.

---

### 2f. Nested Responsive Behavior — Required

Read [Responsive behavior and validation](references/responsive-behavior.md) before this step.
Audit every eligible flow container, not just the root and `content` wrappers. Stop at INSTANCE
boundaries. For each content row/column, record its role and decisions for FILL, WRAP, and min
width, or its intentional exception. Do not infer completion from binding counts.

Apply these coupled rules:
- **FILL:** content-bearing groups, including CTA-group containers, fill their available parent
  width when their role requires it. Individual buttons/labels/icons may still HUG or stay FIXED.
- **WRAP:** horizontal content rows that should reflow as space narrows use WRAP. Evaluate child
  minimums, fixed siblings, row gaps, and parent height together; WRAP alone is not sufficient.
- **Minimum width:** meaningful nested columns/cards that should wrap before collapsing bind the
  verified `width/w-min` token where appropriate. It is not restricted to `content` wrappers.
  Do not put 327px on every nested frame, control, or inner element of a padded card.
- **Height/text/media:** preserve intended media proportions and let wrapped content grow in
  height. Remove an obsolete fixed-width binding only when it conflicts with established FILL
  behavior. Do not use clipping, hidden content, or smaller text to conceal overflow.

Use approved references and the user's established intent for layout choices. Preserve deliberate
single-row navigation, tables, controls, and artwork. Match verified exposed `Viewport` variants
under the component rule; report other unresolved breakpoint behavior. Ask only about unresolved
choices and continue independent work.
Run Step 2e after this step so new row gaps also receive exact token matching/manual review.

---

### 2g. Final Verification — Every Run

Follow the breakpoint test procedure in
[Responsive behavior and validation](references/responsive-behavior.md#breakpoint-verification).
Verify the prepared layout at 1920/Desktop, 768/Tablet, and 375/Mobile, plus an intermediate width
around each changed row's wrap transition. Use isolated temporary copies of the authorized root;
keep the prepared source at its original viewport/mode and remove only copies created for testing.
Retain scaled viewport versions only in Phase 2 after the review checkpoint; an earlier scaling
request does not bypass source review.

Compare the original viewport with its baseline for unintended changes. At each test width check
outer gutters, content/column constraints, actual row wrapping, text/media clipping, height growth,
and visible instance behavior. Extra centered max-width space is allowed. Modes belong only on
the full viewport root. Components retain their links; only permitted variant switches and
specifically approved overrides may change.

Report per-width results and unresolved failures. A Desktop screenshot or zero unmatched tokens
alone does not establish responsiveness. If a required check cannot be performed or a protected
component blocks it, label readiness **incomplete** rather than declaring the frame ready.
A second run on an unchanged result must not add wrappers or redundant bindings.

---

## Phase 1 — Execution Order

1. Step 1 — identify approved section/source IDs, verify fonts, and capture the baseline
2. Step 0 — discover variables and audit bindings
3. Step 2a — establish Responsive mode on the full viewport root only
4. Step 2b — auto-layout for eligible containers
5. Step 2c — outer page gutter on descriptive shells
6. Step 2d — `content` wrapper structure and width constraints
7. Step 2f — audit/apply nested FILL, WRAP, minimum widths, and height behavior
8. Step 2e — exact padding/item-gap/row-gap matches; flag unmatched values
9. Step 2g — original-viewport comparison and breakpoint verification
10. Present the Phase 1 summary and obtain source approval at the review checkpoint

---

## Phase 1 — Review Summary

Before requesting source approval, report:

- How many section frames received `frame/margin`
- How many content wrappers were created vs already existed
- How many `px-value/*` bindings were applied
- How many nodes received FILL sizing (section frames, wrappers, and nested content)
- Rows changed to WRAP, nested columns given min-width bindings, and intentional exceptions
- Which Responsive mode was applied to the root
- Any frames that needed auto-layout direction confirmed by user
- Existing bindings corrected or unresolved, and discovery limitations
- Layout exceptions, fixed-width dependencies, and effective-mode/width conflicts
- Unmatched raw values awaiting manual approval
- Per-width visual/structural results, responsive readiness, unresolved failures, and limitations
- Permitted Viewport switches and specifically approved component exceptions
- Font/environment blockers and images requiring designer crop review
- Any steps skipped and why

---

## Guardrails

- **Protect instances and their descendants** — allow only the documented reparenting and
  matching Viewport switches; all other overrides need specific approval
- **Keep mutations and all generated frames inside the approved section; verify actual parent IDs**
- **Read-only reference discovery outside the section is allowed**
- **Never apply `frame/margin` to the root frame** — section-level frames only
- **Never apply `frame/margin` to internal padding, including banner padding; outer page gutter only**
- **Never apply `frame/margin` to top or bottom padding**
- **Never apply content width limits to outer section shells**
- **Apply `w-max` to main `content`; `w-min` also applies to eligible nested columns**
- **Keep viewport roots fixed-width; use FILL for eligible flow sections as defined in Step 2c**
- **Never hardcode variable IDs** — always discover from the live file in Step 0
- **Never guess a `px-value/*` step** — always resolve through the lookup table
- **Never round unmatched raw spacing values** — leave unchanged pending manual approval
- **Set Responsive mode only on the full viewport root** — descendants inherit
- **Do not treat max-width whitespace as a gutter error**
- **Never apply auto-layout direction when ambiguous** — always prompt the user
- **Never skip Step 0** — variable verification must happen before any binding
- **Never force FILL or create wrappers solely from node type, child count, or names**
- **Do not preserve HUG/FIXED/NO_WRAP blindly or declare readiness from Desktop alone**


---

## Phase 2 — Create and Validate Scaled Designs

### Confirm the scaling scope

At the review checkpoint, combine the two decisions when useful: "Do you approve these
responsive sources, and would you like Tablet and Mobile versions created from them?" Adapt
the targets to the project. Enter Phase 2 only after source approval and scaling authorization.
If the sources are already approved but scaling is not authorized, ask only about scaling.
Do not repeat either question when its answer is already clear from the conversation.

Establish the approved source frame IDs, target widths/modes, destination section, and any
unresolved breakpoint behavior from the conversation and approved references. Default targets
for this project are 1920/Desktop, 768/Tablet, and 375/Mobile; keep the approved Desktop source
rather than duplicating it unnecessarily. Ask only for missing decisions that affect the result.
When resuming, inspect existing deliverables and update the intended versions instead of adding
duplicates. Preserve unrelated frames.

### Scale from the approved sources

- Create the requested viewport versions from each approved responsive source inside the
  approved section. Explicitly verify parent IDs, local positions, section bounds, and absence
  of overlaps. Preserve the source, component links, content order, variables, and intentional
  layout exceptions. Do not declare creation complete until section membership is confirmed.
- Set the verified Responsive collection mode on each full viewport root only, and use its
  target width. Let prepared FILL, WRAP, min/max constraints, and inherited tokens drive reflow.
- Match exposed `Viewport` variants to Tablet/Mobile using their verified options; preserve
  all unrelated instance properties. Root collection mode and component variant are separate checks.
- Inspect fixed-width dependencies and breakpoint-specific composition. Apply in-scope fixes;
  request specific approval for instance overrides beyond matching Viewport switches.
- Resolve ambiguous treatments such as comparison-table stacking or an unavailable component
  variant before making those changes. Reuse established decisions across equivalent frames.
- Follow the [image sizing and crop checks](references/responsive-behavior.md#image-sizing-and-crop-review).
  Use suitable image Fill and/or locked aspect ratios to prevent distortion. Report designer
  crop decisions separately from structural layout completion.
- If scaling exposes a source-layout defect, correct the authorized source and affected versions,
  repeat relevant checks, and return materially changed sources to review before continuing.
  Do not silently redefine a shared component or repair only one copy of a shared layout defect.

### Validate and hand off

Follow [Breakpoint verification](references/responsive-behavior.md#breakpoint-verification) on
all retained versions. Check actual bounds and visually inspect gutters, wrapping, text/media
clipping, height growth, alignment, inherited modes, and visible protected-component behavior.
Verify section membership and non-overlapping placement too. Root resizing or a mode switch
alone does not establish completion. Preserve extra centered space caused by max width; never convert it into outer padding.

Resolve failures within scope. If a protected component or unresolved design choice blocks a
version, report that version as incomplete. An explicitly accepted limitation may accompany a
trial deliverable, but must not be reported as a passing check.

Report created or updated frame IDs/links, source-to-viewport mapping, widths/modes, validation
results, remaining dependencies, and temporary-copy cleanup. Distinguish prepared sources from
scaled deliverables so the user can review the correct frames.
