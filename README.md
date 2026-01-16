# Immobilien Search Component

A modern, accessible property search hero component for immobilien.de

## Table of Contents

- [HTML Structure & Semantics](#html-structure--semantics)
- [Design Decisions & Rationale](#design-decisions--rationale)
- [Responsive Design Strategy](#responsive-design-strategy)
- [Accessibility Considerations](#accessibility-considerations)
- [CSS Architecture](#css-architecture)

---

## HTML Structure & Semantics

### Document Structure

```
<section class="immo-search-hero">
  └── <div class="immo-search-hero__container">
      └── <search class="immo-search">
          ├── <h1> Title
          └── <form> Search Form
              ├── Switcher (Mieten/Kaufen)
              └── Input Group
                  ├── Property Type Dropdown
                  ├── Location Input
                  └── Submit Button
```

### Semantic Elements

| Element | Purpose |
|---------|---------|
| `<section>` | Defines the hero as a thematic grouping |
| `<search>` | HTML5 semantic element indicating search functionality |
| `<form>` | Groups all form controls with `aria-label` for context |
| `<h1>` | Primary heading establishing page hierarchy |
| `<button type="button">` | Interactive controls that don't submit the form |
| `<button type="submit">` | Form submission trigger |
| `<input type="hidden">` | Stores selected values for form submission |

### Form Control Patterns

**Tab Pattern (Switcher)**
```html
<div role="tablist" aria-label="Transaktionsart wählen">
  <button role="tab" aria-selected="true" tabindex="0">Mieten</button>
  <button role="tab" aria-selected="false" tabindex="-1">Kaufen</button>
</div>
```

**Listbox Pattern (Dropdown)**
```html
<button aria-haspopup="listbox" aria-expanded="false" aria-controls="list-id">
<ul role="listbox" aria-labelledby="label-id">
  <li role="option" aria-selected="true">Option</li>
</ul>
```

**Input with Ghost Text**
```html
<input placeholder="Static text for screen readers" aria-describedby="error-id">
<span aria-hidden="true">Animated ghost text</span>
<span role="alert" aria-live="assertive">Error messages</span>
```

---

## Design Decisions & Rationale

### Glassmorphism Effect

The component uses a frosted glass aesthetic for form controls:

```scss
--as-glass-bg: rgba(180, 180, 180, 0.25);
--as-glass-blur: 20px;
--as-glass-border: rgba(255, 255, 255, 0.3);
```

**Rationale:**
- Creates visual depth against the background image
- Maintains readability while allowing background visibility
- Modern aesthetic that works across light and dark images
- Transitions to solid white on focus for clarity

### Color Palette

**Brand Colors (Teal-Blue)**
```scss
--as-color-brand-primary: #0077b6;
--as-color-brand-primary-hover: #005f92;
```

**Rationale:**
- Teal-blue conveys trust, stability, and professionalism
- Appropriate for real estate/property industry
- Passes WCAG AA contrast with white text (4.6:1)
- Distinct from common blue (#2563eb) for brand differentiation

### Animated Typing Effect

Uses ghost text overlay instead of animated placeholder:

**Rationale:**
- `aria-hidden="true"` on ghost text hides animation from screen readers
- Static placeholder remains accessible for assistive technology
- Same visual effect for sighted users
- Blinking cursor adds authenticity to typing effect

### Switcher with Sliding Indicator

```scss
--switcher-indicator-width: calculated;
--switcher-indicator-offset: calculated;
```

**Rationale:**
- CSS custom properties enable smooth JavaScript-controlled animations
- Indicator positioned behind buttons (z-index layering)
- Width adapts to button text length dynamically
- Transitions disabled on initial load to prevent flash

### Icons in UI Controls

**Switcher Icons:**
- Mieten (Rent): Key icon - universal symbol for rental
- Kaufen (Buy): House with checkmark - represents ownership

**Dropdown Icons:**
- Each property type has a descriptive icon
- Helps users quickly identify options visually
- Icons use `currentColor` to inherit text color

---

## Responsive Design Strategy

### Mobile-First Approach

Base styles target mobile (320px+), with progressive enhancements:

```
320px+  → Base (mobile)
481px+  → Large mobile / Small tablet
768px+  → Tablet
1000px+ → Desktop
1440px+ → Large desktop
```

### Breakpoint Behavior

| Breakpoint | Switcher | Inputs | Layout |
|------------|----------|--------|--------|
| 320px+ | Full width, centered | Stacked | Column |
| 481px+ | Auto width | Stacked | Column |
| 768px+ | Auto width | Row | Column |
| 1000px+ | Auto width | Row | Row (all inline) |

### Key Responsive Patterns

**Fluid Typography**
```scss
font-size: clamp(2.25rem, 3.5vw, 3rem);
```

**Flexible Switcher**
```scss
// Mobile: full width with flex buttons
.immo-search__switcher { width: 100%; }
.immo-search__switcher-button { flex: 1; }

// Tablet+: auto width
@media (min-width: 481px) {
  .immo-search__switcher { width: auto; }
  .immo-search__switcher-button { flex: none; }
}
```

**Dropdown Overflow Prevention**
```scss
max-width: calc(100vw - var(--as-spacing-xl));
```

### Landscape Orientation

Special handling for mobile landscape where vertical space is limited:

```scss
@media (max-width: 767px) and (orientation: landscape) {
  .immo-search-hero { min-height: 350px; }
  .immo-search__title { font-size: var(--as-font-size-xl); }
}
```

---

## Accessibility Considerations

### WCAG 2.1 Compliance

| Criterion | Implementation |
|-----------|----------------|
| 1.3.1 Info & Relationships | Semantic HTML, ARIA roles |
| 1.4.3 Contrast (AA) | All text meets 4.5:1 minimum |
| 1.4.11 Non-text Contrast | UI components meet 3:1 |
| 2.1.1 Keyboard | Full keyboard navigation |
| 2.4.6 Headings | Clear `<h1>` hierarchy |
| 4.1.2 Name, Role, Value | ARIA states updated dynamically |

### Color Contrast Ratios

| Element | Contrast | Level |
|---------|----------|-------|
| Primary text on white | 12.6:1 | AAA |
| Secondary text on white | 5.4:1 | AA |
| Tertiary text on white | 4.6:1 | AA |
| White text on brand primary | 4.6:1 | AA |
| Error text on white | 5.9:1 | AA |
| Ghost text on glass | ~4.5:1 | AA |

### Keyboard Navigation

**Switcher (Tab Pattern)**
- `Tab`: Move focus to/from switcher
- `Arrow Left/Right`: Switch between options
- `Enter/Space`: Activate option

**Dropdown (Listbox Pattern)**
- `Tab`: Move focus to/from dropdown
- `Enter/Space`: Open dropdown or select focused option
- `Arrow Up/Down`: Navigate options
- `Escape`: Close dropdown

### Screen Reader Support

**Live Regions**
```html
<span role="alert" aria-live="assertive">Error announcements</span>
```

**Hidden Decorative Content**
```html
<svg aria-hidden="true">...</svg>
<span class="immo-search__ghost-text" aria-hidden="true">Animated text</span>
```

**Descriptive Labels**
```html
<label for="zipCode" class="immo-search__sr-only">Ort oder Postleitzahl</label>
<span id="propertyTypeLabel" class="immo-search__sr-only">Immobilientyp</span>
```

### Reduced Motion

```scss
@media (prefers-reduced-motion: reduce) {
  // Disable all transitions
  .immo-search__switcher-button,
  .immo-search__dropdown-list { transition: none; }

  // Disable blur effects (performance)
  .immo-search__switcher { backdrop-filter: none; }

  // Static cursor for ghost text
  .immo-search__ghost-text::after { animation: none; }
}
```

### High Contrast Mode

```scss
@media (prefers-contrast: more) {
  .immo-search__input {
    background: var(--as-color-bg-primary);
    border: 3px solid var(--as-color-text-primary);
    backdrop-filter: none;
  }
}
```

---

## CSS Architecture

### Methodology: BEM (Block Element Modifier)

```
.block {}
.block__element {}
.block__element--modifier {}
```

**Examples:**
```scss
.immo-search {}                              // Block
.immo-search__input {}                       // Element
.immo-search__input--error {}                // Modifier
.immo-search__switcher-button--active {}     // Modifier
```

### Design Tokens (CSS Custom Properties)

Centralized in `:root` for consistency and theming:

```scss
:root {
  // Colors
  --as-color-brand-primary: #0077b6;
  --as-color-text-primary: #1a1a1a;

  // Spacing (8px base unit)
  --as-spacing-xs: 0.5rem;   // 8px
  --as-spacing-md: 1rem;     // 16px

  // Typography
  --as-font-size-base: 1rem;

  // Borders
  --as-radius-lg: 0.75rem;

  // Transitions
  --as-transition-base: 200ms ease-in-out;

  // Component-specific
  --as-field-height: 52px;
  --as-glass-blur: 20px;
}
```

### File Structure

```scss
// 1. Variables (design tokens)
:root { ... }

// 2. Base styles
body { ... }
* { box-sizing: border-box; }

// 3. Layout components
.immo-search-hero { ... }
.immo-search-hero__container { ... }

// 4. UI components
.immo-search { ... }
.immo-search__switcher { ... }
.immo-search__dropdown { ... }
.immo-search__input { ... }

// 5. Utilities
.immo-search__sr-only { ... }

// 6. Responsive overrides
@media (min-width: 768px) { ... }

// 7. Accessibility overrides
@media (prefers-reduced-motion: reduce) { ... }
@media (prefers-contrast: more) { ... }
```

### Performance Optimizations

**Layout Containment**
```scss
.immo-search-hero { contain: layout style; }
.immo-search__form { contain: layout style; }
```

**Compositor Layer Promotion**
```scss
.immo-search__dropdown-list {
  will-change: opacity, transform;
}
```

**Transition Initialization**
```scss
// Prevent animation on page load
.immo-search__switcher--ready .immo-search__switcher-indicator {
  transition: transform 180ms, width 180ms;
}
```

**Passive Event Listeners**
```javascript
window.addEventListener('resize', handleResize, { passive: true });
```

### Fallbacks

**Backdrop Filter**
```scss
@supports not (backdrop-filter: blur(1px)) {
  .immo-search__input {
    background: rgba(80, 80, 80, 0.85);
  }
}
```

**Safari Prefixes**
```scss
backdrop-filter: blur(var(--as-glass-blur));
-webkit-backdrop-filter: blur(var(--as-glass-blur));
```

---

## Browser Support

| Browser | Version | Notes |
|---------|---------|-------|
| Chrome | 88+ | Full support |
| Firefox | 103+ | Full support |
| Safari | 15.4+ | Requires `-webkit-` prefix for backdrop-filter |
| Edge | 88+ | Full support |

---

## Development

### Prerequisites

- Sass compiler

### Build CSS

```bash
sass finder-wohnen.scss finder-wohnen.css
```

### Watch for Changes

```bash
sass --watch finder-wohnen.scss:finder-wohnen.css
```
