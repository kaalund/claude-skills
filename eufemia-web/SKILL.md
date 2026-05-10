---
name: eufemia-web
description: Enforce DNB Eufemia design system compliance - spacing, colors, typography tokens across React, HTML/CSS, Figma
---

# DNB Eufemia Design System Compliance

This skill enforces Eufemia design system principles for designers building POCs and prototypes.

## How This Skill Works

**Activation:** Triggered by "eufemia" keyword (case-insensitive, whole word)

**Session Persistence:** Once activated, all UI work in the session uses Eufemia compliance

**Silent Enforcement:** Applies tokens and components without status reports

**Multi-Stack:** Supports React, HTML/CSS, Tailwind, and Figma workflows

---

## Activation Logic

### Keyword Detection

Look for "eufemia" as a whole word (case-insensitive) in the user's message:
- Matches: "using eufemia", "Eufemia components", "EUFEMIA style"
- No match: "eufemian", "non-eufemia"

### Session State Check

On skill invocation:

1. **Check for existing session flag:**
   - Use `TaskList` to search for task with metadata `{ eufemia_mode: true }`
   - If found: Skip opt-in, proceed to enforcement
   - If not found: Continue to step 2

2. **Detect if UI work is being requested:**
   - UI work indicators: creating/modifying components, forms, buttons, layouts, cards, pages
   - Non-UI indicators: questions about Eufemia, explanations, documentation
   - If UI work: Continue to step 3
   - If not UI work: Provide brief explanation of Eufemia, exit skill

3. **Present opt-in prompt:**
   - Ask: "Apply full Eufemia compliance for this session? This will use Eufemia tokens and components for all UI work until the session ends."
   - If YES: Continue to step 4
   - If NO: Exit skill, proceed with standard approach

4. **Create session marker:**
   - Create task: `TaskCreate` with:
     - `subject`: "Eufemia mode active"
     - `description`: "Session-wide Eufemia design system compliance"
     - `metadata`: `{ eufemia_mode: true, eufemia_stack: null }`
   - Immediately mark as completed: `TaskUpdate` with `status: "completed"`
   - Note: Task serves as session flag only, not actual work to do

5. **Detect stack:**
   - Check context for stack indicators:
     - React: imports from `@dnb/eufemia`, JSX syntax, `.tsx/.jsx` files
     - HTML/CSS: HTML tags, `.css` files, `<style>` blocks
     - Tailwind: `tailwind.config`, utility classes, `@tailwind` directives
     - Figma: Figma URL, `use_figma` tool mention
   - If ambiguous: Ask "Which stack? (React / HTML/CSS / Tailwind / Figma)"
   - Update task metadata: `TaskUpdate` with `metadata: { eufemia_stack: "react|html-css|tailwind|figma" }`

6. **Enforce silently:**
   - Proceed with implementation using Eufemia tokens and components
   - No announcement like "Applying Eufemia tokens..."
   - Just build correctly using the guidance in sections below

### Stack Detection Precedence

When multiple indicators present:

1. Explicit user mention ("using React", "plain HTML", "in Figma")
2. Figma URL or `use_figma` tool
3. Imports from `@dnb/eufemia` package
4. Tailwind config or utility classes
5. HTML tags with CSS
6. Ask if still ambiguous

---

## Design Tokens (v11)

Eufemia v11 uses semantic design tokens (`--token-*` CSS custom properties). The legacy `--color-*` CSS custom properties are **deprecated**. Always prefer semantic tokens.

### Token Naming Pattern

```
--token-color-{section}-{role}[-{state}][-{modifier}]
```

**Sections:** `background`, `text`, `icon`, `stroke`, `decorative`

**States:** (no suffix) = default, `-hover`, `-pressed`, `-focus`, `-disabled`, `-selected`

**Status states:** `-error`, `-destructive`, `-positive`, `-warning`, `-info`

**Modifiers:** `-subtle` (lower-contrast), `-inverse` (opposite scheme), `-ondark` (local dark surface), `-static` (doesn't change with mode)

### Token Selection Checklist

When styling a custom component:

1. What is the **role** of this color? (text, icon, background, stroke, decorative)
2. What **state** is it in? (default, hover, focus, pressed, disabled, selected, error)
3. Does an Eufemia component with similar behavior exist? Check which tokens it uses.
4. Use the semantic token matching both role and state.
5. If multiple children share a state, introduce a local CSS variable.
6. **Never** pick a token by matching hex values. Pick by semantic meaning.

### Color Tokens - Core Usage

| Purpose | Token |
|---------|-------|
| Page background | `--token-color-background-page-background` |
| Neutral surface | `--token-color-background-neutral` |
| Subtle surface | `--token-color-background-neutral-subtle` |
| Primary text | `--token-color-text-neutral` |
| Secondary text | `--token-color-text-neutral-alternative` |
| Action background | `--token-color-background-action` |
| Action text | `--token-color-text-action` |
| Action text on action bg | `--token-color-text-action-inverse` |
| Action hover | `--token-color-background-action-hover` |
| Action border | `--token-color-stroke-action` |
| Focus ring | `--token-color-stroke-action-focus` |
| Error border | `--token-color-stroke-error` |
| Error text | `--token-color-text-destructive` |
| Success background | `--token-color-background-positive-subtle` |
| Warning background | `--token-color-background-warning-subtle` |
| Neutral border | `--token-color-stroke-neutral-subtle` |
| Icon default | `--token-color-icon-neutral` |
| Icon action | `--token-color-icon-action` |
| Disabled text | `--token-color-text-action-disabled` |
| Disabled border | `--token-color-stroke-action-disabled` |

### Common Mistakes to Avoid

- Picking a token because its current hex value matches an old hardcoded color
- Using `--token-color-component-*` tokens in application code (internal to Eufemia components only)
- Using decorative tokens for UI states like error, hover, or disabled
- Hardcoding `ondark` tokens without checking the surface context
- Repeating the same token in many selectors instead of using a local CSS variable
- Using deprecated `--color-*` variables (use `--token-*` instead)

### Spacing Tokens

Use these tokens for margins, padding, gaps, and layout spacing.

| Token | Value | CSS Usage | React Usage |
|-------|-------|-----------|-------------|
| `--spacing-xx-small` | 0.25rem (4px) | `margin: var(--spacing-xx-small)` | `space="xx-small"` |
| `--spacing-x-small` | 0.5rem (8px) | `margin: var(--spacing-x-small)` | `space="x-small"` |
| `--spacing-small` | 1rem (16px) | `margin: var(--spacing-small)` | `space="small"` |
| `--spacing-medium` | 1.5rem (24px) | `margin: var(--spacing-medium)` | `space="medium"` |
| `--spacing-large` | 2rem (32px) | `margin: var(--spacing-large)` | `space="large"` |
| `--spacing-x-large` | 3rem (48px) | `margin: var(--spacing-x-large)` | `space="x-large"` |
| `--spacing-xx-large` | 3.5rem (56px) | `margin: var(--spacing-xx-large)` | `space="xx-large"` |

**Common Patterns:**
- Component spacing: `small` to `medium`
- Section spacing: `large` to `x-large`
- Page margins: `x-large` to `xx-large`
- Inline element gaps: `xx-small` to `x-small`

### Radius Tokens

| Token | DNB Value |
|-------|-----------|
| `--token-radius-0` | `0` |
| `--token-radius-xs` | `0.125rem` (2px) |
| `--token-radius-sm` | `0.25rem` (4px) |
| `--token-radius-md` | `0.5rem` (8px) |
| `--token-radius-lg` | `1rem` (16px) |
| `--token-radius-xl` | `1.5rem` (24px) |
| `--token-radius-full` | `9999px` |

### Typography Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--font-family-default` | DNB, Arial, sans-serif | Body text, UI |
| `--font-family-heading` | DNB, Arial, sans-serif | Headings |
| `--font-size-x-small` | 0.875rem (14px) | Small labels, captions |
| `--font-size-small` | 1rem (16px) | Secondary text |
| `--font-size-basis` | 1.125rem (18px) | Body text (default) |
| `--font-size-medium` | 1.25rem (20px) | Emphasized text, lead |
| `--font-size-large` | 1.625rem (26px) | Small headings, H4-H5 |
| `--font-size-x-large` | 2.125rem (34px) | Medium headings, H3 |
| `--font-size-xx-large` | 3rem (48px) | Large headings, H1-H2 |

### Z-Index Scale

| Token | Value | Usage |
|-------|-------|-------|
| `--z-index-section` | `1` | Content sections |
| `--z-index-dropdown` | `100` | Inline dropdowns, drawer-lists |
| `--z-index-popover` | `1000` | Popovers, floating elements |
| `--z-index-tooltip` | `1100` | Tooltips |
| `--z-index-modal` | `3000` | Dialogs, drawers, overlays |

---

## Dark Mode

### Setup

Dark mode tokens are NOT included in the default theme import. Import separately:

```tsx
// DNB theme
import '@dnb/eufemia/style/themes/ui/ui-theme-dark-mode.min.css'

// Sbanken theme
import '@dnb/eufemia/style/themes/sbanken/sbanken-theme-dark-mode.min.css'
```

### Activation

Use the `Theme` component with `colorScheme`:

```tsx
import { Theme } from '@dnb/eufemia/shared'

// Whole app dark mode
<Theme colorScheme="dark">
  <App />
</Theme>

// Auto (follows system preference)
<Theme colorScheme="auto">
  <App />
</Theme>
```

### Choosing Token Variants

Dark mode does NOT change how you choose semantic tokens. Use the same token families as light mode, adjusting only for surface relationships:

| Situation | Use | Example |
|-----------|-----|---------|
| Content on current scheme's default surface | Base token | `--token-color-text-neutral` |
| Content on opposite-scheme surface | `inverse` variant | `--token-color-text-action-inverse` |
| Content on a local dark surface (regardless of scheme) | `ondark` variant | `--token-color-text-neutral-ondark` |

### Practical Rule

1. Is this neutral content or action content?
2. Is it on the current scheme, the opposite scheme, or a local dark surface?
3. Pick the base, `inverse`, or `ondark` variant accordingly.

### Dark Surface Pattern (CSS)

```scss
.my-card {
  --card-text-color: var(--token-color-text-neutral);
  --card-icon-color: var(--token-color-icon-neutral);
}

.my-card--on-dark {
  --card-text-color: var(--token-color-text-neutral-ondark);
  --card-icon-color: var(--token-color-icon-neutral-ondark);
}

.my-card__title {
  color: var(--card-text-color);
}
```

### Dark Surface in React Components

For Eufemia components, use `surface="dark"`:

```tsx
<Theme surface="dark">
  <Card>Components auto-switch to ondark tokens</Card>
</Theme>
```

For custom components, detect the surface with `useTheme`:

```tsx
import { useTheme } from '@dnb/eufemia/shared'

function MyComponent() {
  const theme = useTheme()
  const isDark = theme?.surface === 'dark'
  return (
    <div className={clsx('my-component', isDark && 'my-component--dark')}>
      ...
    </div>
  )
}
```

### Preventing Dark Mode Flash (FOUC)

For SSR applications, see the Theme component documentation on preventing FOUC.

---

## Layout and Spacing

### Layout Components

| Component | Use Case |
|-----------|----------|
| `Flex.Container` / `Flex.Item` | Flexbox-based layout, forms |
| `Flex.Stack` | Vertical stacking with consistent gap |
| `Grid.Container` / `Grid.Item` | Column-based responsive layouts |
| `Space` | Adding spacing margins |
| `Card` | Grouped content container |

### Flex Layout

```tsx
import { Flex } from '@dnb/eufemia'

// Vertical stack (forms, content)
<Flex.Stack>
  <Field.String label="Name" />
  <Field.Email label="Email" />
</Flex.Stack>

// Horizontal with responsive spans
<Flex.Container>
  <Flex.Item span={{ small: 12, medium: 4 }}>Sidebar</Flex.Item>
  <Flex.Item span={{ small: 12, medium: 8 }}>Content</Flex.Item>
</Flex.Container>
```

### Grid Layout

```tsx
import { Grid } from '@dnb/eufemia'

<Grid.Container columns={{ small: 1, medium: 3, large: 3 }} columnGap="small">
  <Card stack><H2>Card 1</H2><P>Content</P></Card>
  <Card stack><H2>Card 2</H2><P>Content</P></Card>
  <Card stack><H2>Card 3</H2><P>Content</P></Card>
</Grid.Container>
```

### Media Queries and Breakpoints

| Pixel | Type | Rem | CSS Property | Columns |
|-------|------|-----|--------------|---------|
| 640 | `small` | 40em | `--layout-small` | 4 |
| 960 | `medium` | 60em | `--layout-medium` | 6 |
| 1152 | `large` | 72em | `--layout-large` | 12 |

**Breakpoint ranges:**
- `isSmall`: 0 to 40em (4 columns)
- `isMedium`: 40em to 60em (6 columns)
- `isLarge`: above 60em (12 columns)

**React hooks:**

```tsx
import { useMedia } from '@dnb/eufemia/shared'

function Component() {
  const { isSmall, isMedium, isLarge } = useMedia()
  return isSmall && <MobileLayout />
}
```

**SCSS mixins:**

```scss
@use '@dnb/eufemia/style/core/utilities' as utilities;

@include utilities.allBelow(large) { /* small+medium screens */ }
@include utilities.allAbove(small) { /* medium+large screens */ }
```

### Units and the 8px Grid

- Use `rem` as default sizing unit
- Use nearest half `rem` (0.5rem, 1rem, 1.5rem) to maintain 8px base grid
- Use `px` only for borders and visual helper lines
- Use `em` for CSS `@media` queries
- Use viewport units / percentage for responsive widths and positioning

---

## Best Practices for Styling

### CSS Properties Order

Follow the rational order:

1. **Positioning** (position, top/right/bottom/left, z-index, display)
2. **Box Model** (width, height, margin, padding, color)
3. **Typography** (font, line-height, text-align)
4. **Visual** (background, border, border-radius, opacity)
5. **Animation** (transition)
6. **Misc** (user-select, cursor)

### Use Local CSS Variables for State Handling

When multiple children react to the same state, define a local variable on the parent:

```scss
.my-field {
  --field-border-color: var(--token-color-stroke-action);
  --field-background: var(--token-color-background-neutral);

  background-color: var(--field-background);
  box-shadow: inset 0 0 0 0.0625rem var(--field-border-color);

  &:hover {
    --field-border-color: var(--token-color-stroke-action-hover);
  }

  &--error {
    --field-border-color: var(--token-color-stroke-error);
  }

  &[disabled] {
    --field-border-color: var(--token-color-stroke-action-disabled);
    --field-background: var(--token-color-background-neutral-subtle);
  }
}
```

### Typography Best Practices

- Never use headings (`<h3>`) for purely styling purposes
- Heading levels must increase by one: `<h1>` then `<h2>` then `<h3>`
- For styling independent of heading level, use classes: `.dnb-h--xx-large`, `.dnb-h--x-large`, `.dnb-h--large`, `.dnb-h--medium`
- Use `proseMaxWidth` for optimal reading line lengths (body: 45-75 chars, headings: 60-80 chars)

---

## React Implementation

### Import Pattern

```jsx
import { Button, Input, Card, Space } from '@dnb/eufemia/components'
import { Heading, P } from '@dnb/eufemia/elements'
import { Flex, Grid } from '@dnb/eufemia'
import { Theme } from '@dnb/eufemia/shared'
```

### Spacing Pattern

Use the `space` prop or dedicated spacing props:

```jsx
// space prop (shorthand)
<Button space={{ top: 'small', bottom: 'medium' }}>Submit</Button>

// dedicated props
<Input top="small" bottom="medium" />

// Space wrapper for arbitrary content
<Space top="large" bottom="x-large">
  <div>Custom content</div>
</Space>
```

**Never use custom margins:**
```jsx
// WRONG
<Button style={{ marginTop: '16px' }}>Submit</Button>

// CORRECT
<Button top="small">Submit</Button>
```

### Form Pattern

Use Flex.Stack for consistent form layout:

```jsx
<Flex.Stack>
  <Form.MainHeading>Profile</Form.MainHeading>
  <Form.Card>
    <Form.SubHeading>Name</Form.SubHeading>
    <Field.String label="First name" />
    <Field.String label="Last name" />
  </Form.Card>
  <Form.Card>
    <Form.SubHeading>Contact</Form.SubHeading>
    <Field.Email label="Email" />
    <Field.PhoneNumber label="Phone" />
  </Form.Card>
</Flex.Stack>
```

### Card Pattern

```jsx
<Card stack>
  <Heading size="medium">Card Title</Heading>
  <P>Card content with automatic spacing via stack prop</P>
  <Button>Action</Button>
</Card>
```

### Custom Styling with Design Tokens

When custom CSS is needed, use semantic tokens:

```jsx
<div style={{
  padding: 'var(--spacing-medium)',
  backgroundColor: 'var(--token-color-background-neutral-subtle)',
  color: 'var(--token-color-text-neutral)',
  border: '1px solid var(--token-color-stroke-neutral-subtle)',
  borderRadius: 'var(--token-radius-md)'
}}>
  Custom styled container
</div>
```

### Common Replacements

| Generic Request | Eufemia Implementation |
|----------------|------------------------|
| "Add a button" | `<Button variant="primary">` |
| "Add a form field" | `<Field.String label="..." />` inside `<Flex.Stack>` |
| "Add spacing between X and Y" | Use `top`/`bottom` props or `<Space>` |
| "Create a card" | `<Card stack>` with content |
| "Add margin" | Use `space` prop or `top`/`bottom`/`left`/`right` props |
| "Make responsive" | `<Flex.Container>` with `span` on items |
| "Grid layout" | `<Grid.Container columns={...}>` |

---

## HTML/CSS Implementation

### Include Eufemia Styles

```html
<link rel="stylesheet" href="https://unpkg.com/@dnb/eufemia/style/dnb-ui-lib.min.css">
```

Or with imports:

```js
import '@dnb/eufemia/style/core'
import '@dnb/eufemia/style/themes/ui'
```

### Spacing Pattern

Use CSS custom properties:

```css
.container {
  margin-top: var(--spacing-medium);
  margin-bottom: var(--spacing-large);
  padding: var(--spacing-small);
  gap: var(--spacing-small);
}
```

**Never use fixed pixel values for spacing:**
```css
/* WRONG */
.container { margin: 16px; padding: 24px; }

/* CORRECT */
.container { margin: var(--spacing-small); padding: var(--spacing-medium); }
```

### Color Pattern with Semantic Tokens

```css
.my-component {
  background-color: var(--token-color-background-neutral-subtle);
  color: var(--token-color-text-neutral);
  border: 1px solid var(--token-color-stroke-neutral-subtle);
}

.my-component__action {
  background-color: var(--token-color-background-action);
  color: var(--token-color-text-action-inverse);
}

.my-component__action:hover {
  background-color: var(--token-color-background-action-hover);
}

.my-component--error {
  border-color: var(--token-color-stroke-error);
}
```

### Typography Pattern

```css
body {
  font-family: var(--font-family-default);
  font-size: var(--font-size-basis);
  color: var(--token-color-text-neutral);
}

h1 {
  font-family: var(--font-family-heading);
  font-size: var(--font-size-xx-large);
}
```

### Component Classes

```html
<button class="dnb-button dnb-button--primary">Submit</button>

<div class="dnb-card">
  <h2 class="dnb-h--medium">Card Title</h2>
  <p class="dnb-p">Card content</p>
</div>
```

### Scoped Eufemia (when coexisting with other CSS)

```js
import '@dnb/eufemia/style/basis'
import '@dnb/eufemia/style/themes/ui'
```

```html
<div class="dnb-core-style">
  <!-- Eufemia styles apply only inside this wrapper -->
</div>
```

---

## Tailwind Integration

Eufemia provides Tailwind-compatible CSS variable files:

```css
@layer eufemia, theme, utilities;

@import '@dnb/eufemia/style/dnb-ui-core.min.css' layer(eufemia);
@import '@dnb/eufemia/style/themes/ui/ui-theme-components.min.css' layer(eufemia);
@import '@dnb/eufemia/style/themes/ui/ui-theme-basis.min.css' layer(eufemia);

@import 'tailwindcss/theme.css' layer(theme);
@import 'tailwindcss/utilities.css' layer(utilities);

/* Eufemia properties in Tailwind format */
@import '@dnb/eufemia/style/themes/ui/properties-tailwind.css' layer(utilities);

/* Semantic design tokens in Tailwind format */
@import '@dnb/eufemia/style/themes/ui/tokens-tailwind.css' layer(utilities);
```

### Key Rules

- Keep Eufemia as the single source of tokens, colors, spacing, typography
- Disable Tailwind's `preflight` to avoid reset collisions
- Use semantic token utility classes:

```html
<div class="bg-background-action text-text-neutral border-stroke-action">
  Styled with semantic tokens
</div>
```

### Dark mode with Tailwind

```css
@import '@dnb/eufemia/style/themes/ui/tokens-dark-tailwind.css';
```

---

## Figma Integration

When Eufemia mode is active and stack is Figma (detected by Figma URL or `use_figma` tool), follow these patterns.

### Prerequisites

Before using Eufemia in Figma, ensure:
- You have loaded the `figma:figma-use` skill (MANDATORY before any `use_figma` call)
- The target Figma file exists and you have edit access

### Variable References

When creating or modifying designs via `use_figma`, reference Eufemia variables:

```javascript
const frame = figma.createFrame()
frame.layoutMode = 'VERTICAL'
frame.itemSpacing = 16  // --spacing-small equivalent
frame.paddingLeft = 24  // --spacing-medium equivalent
frame.paddingRight = 24
frame.paddingTop = 24
frame.paddingBottom = 24
```

### Eufemia Variable Mappings

**Spacing:**
- `xx-small` (4px) -> `itemSpacing: 4`, `padding: 4`
- `x-small` (8px) -> `itemSpacing: 8`, `padding: 8`
- `small` (16px) -> `itemSpacing: 16`, `padding: 16`
- `medium` (24px) -> `itemSpacing: 24`, `padding: 24`
- `large` (32px) -> `itemSpacing: 32`, `padding: 32`
- `x-large` (48px) -> `itemSpacing: 48`, `padding: 48`
- `xx-large` (56px) -> `itemSpacing: 56`, `padding: 56`

**Colors (RGB 0-1 format for DNB Light theme):**
- Action / sea-green (#007272) -> `{ r: 0, g: 0.447, b: 0.447 }`
- Action pressed / emerald-green (#14555A) -> `{ r: 0.078, g: 0.333, b: 0.353 }`
- Positive (#007B5E) -> `{ r: 0, g: 0.482, b: 0.369 }`
- Neutral text (#333333) -> `{ r: 0.2, g: 0.2, b: 0.2 }`
- Secondary text (#737373) -> `{ r: 0.451, g: 0.451, b: 0.451 }`
- White (#FFFFFF) -> `{ r: 1, g: 1, b: 1 }`
- Error (#D52525) -> `{ r: 0.835, g: 0.145, b: 0.145 }`
- Warning (#FDBB31) -> `{ r: 0.992, g: 0.733, b: 0.192 }`
- Mint-green / subtle (#A5E1D2) -> `{ r: 0.647, g: 0.882, b: 0.824 }`
- Ocean-green / intense (#00343E) -> `{ r: 0, g: 0.204, b: 0.243 }`
- Neutral surface (#F8F8F8) -> `{ r: 0.973, g: 0.973, b: 0.973 }`
- Focus ring (#276ACE) -> `{ r: 0.153, g: 0.416, b: 0.808 }`

**Typography:**
- `x-small` -> `fontSize: 14`
- `small` -> `fontSize: 16`
- `basis` -> `fontSize: 18`
- `medium` -> `fontSize: 20`
- `large` -> `fontSize: 26`
- `x-large` -> `fontSize: 34`
- `xx-large` -> `fontSize: 48`

**Radius (DNB):**
- `sm` -> `cornerRadius: 4`
- `md` -> `cornerRadius: 8`
- `lg` -> `cornerRadius: 16`
- `xl` -> `cornerRadius: 24`
- `full` -> `cornerRadius: 9999`

### Auto Layout Pattern

```javascript
const container = figma.createFrame()
container.name = "Eufemia Container"
container.layoutMode = 'VERTICAL'
container.itemSpacing = 16  // small spacing between items
container.paddingLeft = 24  // medium padding
container.paddingRight = 24
container.paddingTop = 24
container.paddingBottom = 24
container.cornerRadius = 8  // --token-radius-md
container.primaryAxisSizingMode = 'AUTO'
container.counterAxisSizingMode = 'AUTO'
```

### Component Instance Pattern

If Eufemia component library is available in Figma, prefer using component instances:

```javascript
const components = await figma.importComponentByKeyAsync('eufemia-button-primary-key')
if (components) {
  const buttonInstance = components.createInstance()
  frame.appendChild(buttonInstance)
}
```

---

## Audit Mode

When reviewing or modifying existing code for Eufemia compliance, detect and fix violations silently.

### Detection Patterns

**Deprecated color variables:**
```
color: var(--color-sea-green)
background: var(--color-emerald-green)
```
-> Replace with semantic `--token-color-*` equivalents

**Fixed pixel spacing:**
```
margin: 8px; padding: 16px; gap: 24px;
```

**Fixed hex colors:**
```
color: #333333; background: #007272;
```

**Fixed font sizes:**
```
font-size: 14px; font-size: 16px;
```

### Fix Patterns

**Deprecated color migration:**
```
var(--color-sea-green) -> var(--token-color-background-action) or var(--token-color-text-action) depending on role
var(--color-emerald-green) -> var(--token-color-background-action-pressed) or contextual semantic token
var(--color-black-80) -> var(--token-color-text-neutral)
var(--color-white) -> var(--token-color-background-neutral) or var(--token-color-background-page-background)
var(--color-fire-red) -> var(--token-color-text-destructive) or var(--token-color-stroke-error)
var(--color-accent-yellow) -> var(--token-color-background-warning) or var(--token-color-text-warning)
```

**Spacing fixes:**
```
margin: 8px -> margin: var(--spacing-x-small)
padding: 16px -> padding: var(--spacing-small)
gap: 24px -> gap: var(--spacing-medium)
margin-top: 32px -> margin-top: var(--spacing-large)
```

**Typography fixes:**
```
font-size: 14px -> font-size: var(--font-size-x-small)
font-size: 16px -> font-size: var(--font-size-small)
font-size: 18px -> font-size: var(--font-size-basis)
```

**Border radius fixes:**
```
border-radius: 4px -> border-radius: var(--token-radius-sm)
border-radius: 8px -> border-radius: var(--token-radius-md)
border-radius: 16px -> border-radius: var(--token-radius-lg)
```

**React component fixes:**
```jsx
// Before
<button style={{margin: '16px', padding: '8px', background: '#007272'}}>Submit</button>

// After
<Button variant="primary" top="small">Submit</Button>
```

### Audit Workflow

1. **Read the target files** using Read tool
2. **Scan for violations** using patterns above
3. **Apply fixes silently** using Edit tool
4. **No status report** - just fix and continue
