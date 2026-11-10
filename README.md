# Responsive Layout

A Figma skill that prepares existing designs to respond to changing screen widths, then creates Tablet and Mobile versions after review.

The skill works in two phases: first it fixes the responsive structure of the source frames; then, with user approval, it scales those prepared designs. It uses your existing design-system variables and preserves component links.

## What it does

- Applies auto-layout to established content containers.
- Binds outer page gutters, spacing, and width constraints to verified variables.
- Creates or reuses `content` wrappers with minimum and maximum widths.
- Evaluates nested Fill sizing, row wrapping, column minimum widths, and height growth.
- Sets the Responsive collection mode on each full viewport frame.
- Matches exposed component `Viewport` variants to the target breakpoint.
- Checks Desktop, Tablet, Mobile, and widths around changed wrap transitions.
- Creates scaled frames inside the approved Figma section after source approval.
- Reports unresolved component, font, spacing, and image-crop issues.

This is a layout and responsiveness workflow. It does not redesign typography, colors, or content.

## Requirements

- Codex with skills enabled.
- A Figma connection that supports reading and editing the target file.
- Edit access to the target Figma file.
- Access to the design-system variables used by the file.
- Required fonts available in the editing environment.

A connected Figma Desktop Bridge can help when the remote editing environment cannot load fonts installed on your computer.

This repository contains one standalone skill. No coordinator or additional stage skills are required.

## Install

In Codex, ask:

> Install the responsive-layout skill from https://github.com/moonhilda/responsive-layout/tree/main/responsive-layout

Once installed, invoke it as `$responsive-layout` or ask Codex to run Responsive Layout on a specified Figma section.

Each teammate needs the skill installed and their own authorized Figma connection. Installing the skill does not grant access to Figma files or libraries.

## How to use

### Phase 1: Prepare responsive source frames

Start a chat with the Figma section link and a request such as:

> Use $responsive-layout on the frames in this section. Prepare the source frames to be responsive, then stop for my review before creating Tablet and Mobile versions: [Figma link]

The skill will:

1. Identify the approved section and source frames.
2. Verify variables, fonts, existing bindings, and layout structure.
3. Apply responsive layout changes within that section.
4. Test behavior using temporary copies while preserving the source viewport widths and modes.
5. Remove temporary validation copies and report changes, results, and unresolved issues.

Review the prepared source frames. Request revisions or approve them before proceeding.

### Phase 2: Create scaled designs

After reviewing the source frames, reply:

> I approve the responsive sources. Create Tablet and Mobile versions in the same section.

The skill creates the requested versions, applies their root modes and matching exposed `Viewport` variants, and validates the resulting layouts.

Default project targets are:

| Viewport | Width |
|---|---:|
| Desktop | 1920px |
| Tablet | 768px |
| Mobile | 375px |

Specify different targets when your project requires them.

## Design-system conventions

The current instructions reflect these project conventions:

- `frame/margin` is reserved for the outer page gutter: **24px Desktop, 26px Tablet, and 24px Mobile**.
- Internal banner and card padding uses appropriate internal spacing variables.
- Main `content` wrappers use verified minimum and maximum width variables.
- The expected content minimum is **327px**.
- Extra space around centered, maximum-width content is intentional. It must not be converted into fixed outer padding.
- Raw spacing values receive a variable only when an exact, suitable match exists. Unmatched values remain unchanged and are flagged for review.

The skill verifies live variable identities and values rather than assuming that names or IDs are correct.

## Component protection

Component instances remain linked to their main components.

The skill may match an exposed `Viewport` variant and reparent an instance into a content wrapper while preserving its properties.

Other component changes require specific approval. Requests distinguish changes to the whole component’s sizing from changes to layers inside it. Approval applies only to the named components and changes.

The skill does not detach instances or edit main components.

## Review expectations

Responsive validation includes visual checks as well as variable bindings and node bounds.

Images may still need designer adjustments. The skill checks proportions, suitable Fill behavior, focal crops, and text contrast, then flags unresolved crop decisions.

A protected component, missing font, unclear breakpoint treatment, or unmatched spacing value can leave part of the result incomplete. The skill should report those limitations explicitly.

## Repository structure

```text
README.md
responsive-layout/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    └── responsive-behavior.md
```

- `SKILL.md` contains the workflow, scope, approval rules, and variable guidance.
- `agents/openai.yaml` contains the Codex display name and default prompt.
- `responsive-behavior.md` contains detailed layout and validation guidance.

## Updating the skill

- Treat this repository as the shared source of truth.
- Keep the main instructions and supporting reference consistent.
- Test changes on representative Figma frames before distributing them.
- Reinstall or update teammates’ local copies after repository changes. GitHub edits do not automatically update installed copies.
- Start a fresh chat after updating so the revised instructions are loaded.

## Owner

Maintained by [moonhilda](https://github.com/moonhilda).
