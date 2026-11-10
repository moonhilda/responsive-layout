# Responsive behavior and validation

Use during Phase 1 Steps 2f/2g and Phase 2 validation, inside the approved section. Follow the
component protection and permitted changes in SKILL.md: matching exposed Viewport switches are
allowed; other instance overrides require specific approval. Never detach or edit main components.

## Audit by layout role

Walk the entire eligible subtree, stopping layout/token traversal at each protected INSTANCE.
Audit exposed Viewport variants separately; enter internals only for specifically approved work. For each meaningful container record:
node ID, purpose, parent role, horizontal/vertical sizing, wrap mode, width constraints and bindings,
item/row gaps, available inner width, and the action or intentional exception. Inspect equivalent
siblings together so repeated layouts do not get inconsistent treatment.

| Role | Required decision |
|---|---|
| Outer section shell | FILL where supported; outer gutter and section vertical padding; no content min/max |
| Main `content` wrapper | FILL, verified min/max bindings, intended horizontal centering; WRAP if it contains reflowing columns |
| CTA-group container | FILL its content column; evaluate wrapping when its buttons cannot share one row |
| Individual CTA, label, icon, logo | Preserve appropriate HUG/FIXED sizing and existing valid constraints |
| Card or text/media column | FILL when appropriate, with verified minimum width so it reflows before becoming unusably narrow |
| Paragraph or heading | Use available column width with compatible text height/wrapping behavior |
| Navigation or comparison table | Establish its specific responsive treatment; indiscriminate wrapping can destroy meaning |
| Artwork, overlays, cropped media | Preserve intentional composition; distinguish it from accidental flow overflow |

A role decision must be supported by the user's instructions, approved reference, or established
layout. If unresolved, report the specific choice rather than silently preserving the existing
setting and claiming the audit passed.

## FILL and child sizing are separate decisions

A row holding buttons can fill its parent while its individual buttons remain content-sized.
Check FILL validity against the parent's auto-layout context and the node's positioning. Keep
established proportions, height behavior, and content order. Release conflicting fixed-width
bindings on an eligible flow container only when needed for its established FILL behavior.
Do not release the viewport root's legitimate viewport-width binding as a layout fix.

## WRAP and minimum widths work together

For each horizontal content row that should reflow:

1. Enable WRAP; retain intended horizontal spacing and alignment. Establish the wrapped-row gap
   from a verified semantic spacing variable or approved reference. Do not assume a horizontal
   gap is automatically the correct vertical gap. Unresolved new gaps require a concrete choice.
2. Inspect each child's minimum, maximum, and sizing. A FILL column without a useful minimum can
   shrink until text is unusable instead of triggering the intended wrap. A fixed image or protected
   button may also block the row. Record these dependencies explicitly.
3. Bind the approved `width/w-min` (327px here) to eligible independent responsive columns/cards,
   including nested ones. Verify the live token ID, source, type, and effective value first.
4. Calculate available inner width after all ancestor padding. Do not give a padded card's inner
   text frame a 327px minimum when only 263px is available inside that card. The minimum belongs
   on the independent column/card boundary; inner text generally fills the remaining space.
5. Let the row and its necessary ancestors grow vertically. Preserve deliberate fixed-height media,
   but flag a height constraint that clips wrapped content. Media that must shrink needs compatible
   sizing and proportion handling; enabling WRAP on its parent cannot fix an oversized fixed image.
6. Validate the row and its ancestors/descendants at narrow widths. Preserve intentional table,
   navigation, and control behavior; do not hide content or make arbitrary variant swaps.

Do not apply the page's `w-max` to every nested column. Preserve or select appropriate existing
column/media constraints from approved references rather than inventing arbitrary constants.

## Representative outcomes

- A CTA-group container becomes FILL; its buttons are evaluated independently.
- A statistics/CTA content row uses WRAP, with a suitable minimum on its content
  column and compatible internal statistics wrapping. The wrap flag alone is not a passing result.
- An independent text column binds the 327px minimum token. Its image/text parent must also
  support reflow and its image must fit the available width.

Apply these role rules to equivalent nodes discovered throughout the user's actual target.

## Image sizing and crop review

Distinguish an image node's dimensions from the image paint's scaling/crop transform. For
photographic media that should scale proportionally, use an appropriate image Fill mode and/or
lock the node's intended aspect ratio. Do not impose a media ratio on an entire content section
whose height must grow with text. Preserve deliberate crops and artwork proportions.

A Desktop CROP transform can distort a photo after resizing. Correct distortion within scope,
then inspect the focal subject and text contrast at each breakpoint. Fill can remove stretching
while cropping out the subject; a locked node ratio alone does not guarantee a good crop.
Do not declare either setting sufficient without visual review.

Flag image IDs and breakpoints needing designer adjustment, such as lost subjects, awkward crops,
or poor text contrast. Preserve the image asset and report these as open crop decisions rather
than guessing a composition or claiming full visual readiness. An explicitly accepted crop-review
item can accompany structurally validated layouts; visible failures remain documented.

## Breakpoint verification

1. Capture the original-viewport baseline before layout changes. Test the prepared viewport on
   isolated temporary copies in Phase 1, or retained deliverables in Phase 2 after source approval
   and scaling authorization. Verify every copy belongs to the approved section. Track all
   created IDs outside the file and keep the source's viewport/mode intact. Never delete preexisting work.
2. Set the verified Responsive mode on each test root only. Use 1920/Desktop, 768/Tablet, and
   375/Mobile unless the project explicitly specifies other targets. Confirm the corresponding
   outer gutter values are 24/26/24 and inspect the effective min/max values. Match exposed
   Viewport variants to the tested breakpoint separately from the root collection mode.
3. Exercise continuous resizing too: test an intermediate width on each side of a changed row's
   wrap transition in the relevant root mode. A root's mode is explicitly assigned; merely resizing
   its width must not be described as automatically changing Responsive modes.
4. Read actual node bounds and sizing after layout settles. Check that eligible shells follow the
   root, centered content fits within the padded area, columns respect valid minimums, rows wrap
   before overflow, and ancestor heights accommodate the resulting content. Check text reflow,
   image bounds/proportions, focal crops, text contrast, and visible protected-instance behavior. Distinguish intentional
   artwork overflow from accidental content overflow.
5. Visually inspect each required viewport result and the changed wrap transitions. Compare the
   original viewport against its baseline. Treat clipped text, overlapping content, or oversized
   fixed media as failures even when variable bindings and token counts are correct.
6. Correct verified in-scope layout problems, then repeat only affected checks. If a change needs
   an instance override beyond a verified matching Viewport switch, unresolved design choice,
   or new spacing value, report it and obtain the necessary direction; complete independent fixes meanwhile.
7. Remove only temporary copies created for validation, including on partial failure. Retain
   authorized Phase 2 deliverable versions. Report widths/modes tested, results, remaining dependencies,
   and cleanup. Do not claim responsive readiness when required validation is missing or fails.
