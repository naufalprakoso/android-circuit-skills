# Accessibility and Quality

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

## Interaction

- Maintain practical minimum touch targets according to platform guidance.
- Support keyboard navigation where relevant.
- Preserve logical focus order and screen-reader traversal.
- Support Switch Access and other assistive input where relevant.
- Avoid gesture-only actions without alternatives.
- Show visible focus where the platform expects it.
- Ensure disabled controls expose disabled state and do not respond to clicks.
- Keep back behavior predictable.

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

## Quality Checks

- Add previews for accessibility-sensitive states.
- Add semantics-based Compose tests.
- Run automated accessibility checks where the project supports them.
- Manually verify TalkBack or equivalent for critical flows when feasible.
- Test large font, RTL, dark theme, long text, and narrow layouts when relevant.
