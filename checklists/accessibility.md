# Accessibility Review Checklist

Review all UI components produced in this feature against the following criteria.

## Semantic Markup
- Proper HTML elements used (button, nav, main, etc.)
- ARIA labels on all interactive elements
- ARIA roles assigned where semantic HTML is insufficient
- Logical heading hierarchy (h1 > h2 > h3)
- Form inputs have associated labels

## Visual Accessibility
- Color contrast ratio >= 4.5:1 for normal text, >= 3:1 for large text
- Information never conveyed by color alone
- Focus indicators visible on all interactive elements
- Text scales to 200% without layout breakage
- Reduced motion respected (prefers-reduced-motion)

## Motor Accessibility
- Touch targets minimum 44x44pt with adequate spacing
- All functionality reachable via keyboard
- No complex gestures required without alternatives
- Tab order follows logical reading order
- No time-dependent interactions without extension option

## Screen Reader Support
- All images have meaningful alt text (or empty alt for decorative)
- Dynamic content changes announced via live regions
- Modal dialogs trap focus and announce properly
- Skip navigation links for repetitive content
- State changes (expanded, selected, disabled) communicated

## Testing Guidance
- Test with VoiceOver (macOS/iOS) and TalkBack (Android)
- Verify full keyboard navigation flow
- Run automated accessibility linter
- Test at 200% zoom / large text size
- Verify with high contrast mode enabled
