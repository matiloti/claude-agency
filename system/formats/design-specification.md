# Design Specification Format

<!-- Last Updated: 2026-01-27 -->
<!-- Status: Current -->
<!-- Author: orchestrator -->

Standard format for UX/UI design specifications created by the UX Designer agent.

---

## Template

```markdown
# {Feature Name} Design Specification

## Overview
[Brief description of the feature and its purpose]

## User Story Reference
- US-{EPIC}-{NN}: {story title}

## Screen Layout

### Information Architecture
[Hierarchy and organization of content]

### Layout Specifications
[Point measurements, safe area handling, size class adaptations]

## Visual Design

### Colors
[Semantic color names - e.g., .label, .systemBlue]

### Typography
[Text style names - e.g., .headline, .body]

### Icons
[SF Symbol names with rendering mode specifications]

## Interactions

### Gestures
[Tap, swipe, long-press behaviors]

### Haptic Feedback
[When and what type of haptics]

### Animations
[Transition descriptions, preferring system animations]

## States

### Loading State
[Skeleton/placeholder design]

### Empty State
[Empty state design with guidance]

### Error State
[Error presentation and recovery options]

### Success State
[Confirmation and next steps]

## Accessibility

### VoiceOver
[Labels, hints, navigation order]

### Dynamic Type
[Text scaling behavior]

### Contrast
[Contrast ratio compliance]

## Implementation Notes
[Specific guidance for developers]
```

---

## Section Guidelines

### Overview
- Keep concise (2-3 sentences)
- State the problem being solved
- Reference related features if applicable

### Screen Layout
- Use point measurements (not pixels)
- Document safe area handling
- Specify size class adaptations (compact/regular)

### Visual Design
- **Colors**: Always use semantic names (`.label`, `.systemBackground`)
- **Typography**: Use iOS text styles (`.headline`, `.body`), never fixed sizes
- **Icons**: Specify SF Symbol name and rendering mode

### Interactions
- **Gestures**: Standard iOS gestures (tap, swipe, long-press)
- **Haptics**: Use UIFeedbackGenerator types (`.success`, `.warning`, `.selection`)
- **Animations**: Prefer system animations; custom only when needed

### States
All interactive features must document:
1. Loading state (skeleton/placeholder)
2. Empty state (with guidance)
3. Error state (with recovery)
4. Success state (with next steps)

### Accessibility
Minimum requirements:
- VoiceOver labels for all interactive elements
- Dynamic Type scaling support
- Contrast ratios: 7:1 (small text), 4.5:1 (large text)
- Reduced motion alternatives

---

## File Naming Convention

- `knowledge/frontend/design/{feature-name}-design-spec.md`
- `knowledge/frontend/design/accessibility/{feature-name}-accessibility.md`
- `knowledge/frontend/design/hig-compliance/{feature-name}-hig-review.md`

---

## Related Documentation

- [Formats Index](./README.md)
- UX Designer Agent: `.claude/agents/ios-ux-designer.md`
- iOS UX Design Skill: Provides HIG compliance and accessibility guidance
