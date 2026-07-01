# Accessibility and Quality

## Contents

- [Semantics](#semantics)
- [Interaction](#interaction)
- [Visual Accessibility](#visual-accessibility)
- [Forms](#forms)
- [Custom Components](#custom-components)
- [Quality Checks](#quality-checks)

## Semantics

- Provide meaningful labels for interactive controls.
- Use correct roles and state descriptions.
- Provide error semantics and associate errors with affected fields.
- Mark headings where structure matters.
- Provide progress semantics.
- Use collection semantics where they improve navigation.
- Merge semantics thoughtfully.
- Use null content descriptions for decorative images.
- Avoid duplicate spoken labels.
- Do not use test tags as accessibility labels.
- Prefer semantics that describe user meaning over implementation details.
- Use custom actions when a gesture or secondary action needs a screen-reader-accessible equivalent.
- Use `selectableGroup`, `selectable`, `toggleable`, and built-in components where they match the interaction.
- Print or inspect the semantics tree when tests cannot find expected nodes.

## Interaction

- Maintain practical minimum touch targets according to platform guidance.
- Target at least 48 dp for touchable controls unless the platform or design system provides an equivalent accessible target.
- Support keyboard navigation where relevant.
- Preserve logical focus order and screen-reader traversal.
- Support Switch Access and other assistive input where relevant.
- Avoid gesture-only actions without alternatives.
- Show visible focus where the platform expects it.
- Ensure disabled controls expose disabled state and do not respond to clicks.
- Keep back behavior predictable.
- Ensure custom click targets, drag handles, tabs, chips, and toggle rows expose the correct role and selected/checked/disabled state.

## Visual Accessibility

- Support font scaling without clipped or overlapping text.
- Maintain contrast according to project and platform standards.
- Do not rely only on color.
- Handle long translations, dynamic content, RTL, and localization.
- Consider reduced motion or motion sensitivity for animated flows.
- Validate dark theme where supported.

## Forms

- Use labels separate from placeholders.
- Associate errors with fields.
- Communicate required fields.
- Choose validation timing deliberately.
- Move focus after submit errors when useful.
- Use correct keyboard and input types.
- Handle password visibility and secure text entry correctly.
- Show submit progress and prevent duplicate submit when needed.

## Custom Components

- Start from Material, Compose Foundation, or the project design-system component when possible.
- If a custom component replaces a built-in control, re-create the missing semantics, focus, enabled state, interaction source, and touch target.
- Keep icon-only controls labeled through `contentDescription` or surrounding semantics.
- Keep loading, error, selected, checked, expanded, collapsed, and disabled states available to assistive tech.
- Do not rely on color, icon shape, animation, or position as the only signal.
- Provide accessible alternatives for swipe, drag, long-press, hover, and multi-pointer gestures.

## Quality Checks

- Add previews for accessibility-sensitive states.
- Add semantics-based Compose tests.
- Run automated accessibility checks where the project supports them.
- Manually verify TalkBack or equivalent for critical flows when feasible.
- Test large font, RTL, dark theme, long text, and narrow layouts when relevant.
- Test keyboard navigation and focus order for desktop, tablet, and form-heavy flows.
- Assert semantics for custom components rather than only asserting `testTag`.
