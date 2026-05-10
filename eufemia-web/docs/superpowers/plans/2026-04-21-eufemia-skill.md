# Eufemia Design System Compliance Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a keyword-triggered skill that silently enforces DNB Eufemia design system compliance across React, HTML/CSS, and Figma workflows

**Architecture:** Single skill file (`eufemia.md`) with embedded token reference tables, activation logic using TaskCreate metadata for session persistence, and stack-specific implementation guidance

**Tech Stack:** Markdown skill file, Claude Code Skill tool API, Eufemia design system tokens

---

## File Structure

**Files to create:**
- `eufemia.md` - Main skill file with embedded tokens and guidance
- `token-reference.md` - Temporary file holding extracted token data (deleted after embedding)

**Files to reference:**
- `docs/superpowers/specs/2026-04-21-eufemia-design.md` - Design specification

---

### Task 1: Extract Eufemia Token Data

**Files:**
- Create: `token-reference.md`

- [ ] **Step 1: Check for Eufemia package**

Run: `find /Users/peter -name "eufemia" -type d 2>/dev/null | grep node_modules | head -5`

Expected: Paths to any installed Eufemia packages, or empty if not found

- [ ] **Step 2: Attempt token extraction from package (if found)**

If node_modules/@dnb/eufemia exists:

Run: `find [path-from-step-1] -name "*.css" -o -name "tokens.json" -o -name "design-tokens*" | head -10`

Extract custom properties from CSS files or token definitions from JSON

- [ ] **Step 3: Extract from documentation (fallback)**

If package not found, use WebFetch to extract token information:

```markdown
Query https://eufemia.dnb.no/uilib/ navigation structure
Find pages for: spacing, colors, typography, components
Extract token names and values from each page
```

- [ ] **Step 4: Create token reference file**

Create `token-reference.md` with extracted data:

```markdown
# Eufemia Token Reference

## Spacing Tokens

| Token | Value | Usage |
|-------|-------|-------|
| --spacing-xx-small | 0.25rem (4px) | Minimal gaps, tight layouts |
| --spacing-x-small | 0.5rem (8px) | Compact spacing |
| --spacing-small | 1rem (16px) | Standard small spacing |
| --spacing-medium | 1.5rem (24px) | Standard medium spacing |
| --spacing-large | 2rem (32px) | Standard large spacing |
| --spacing-x-large | 2.5rem (40px) | Extra large spacing |
| --spacing-xx-large | 3.5rem (56px) | Maximum spacing |

## Color Tokens

| Token | Value | Usage |
|-------|-------|-------|
| --color-emerald-green | #007272 | Primary brand color |
| --color-sea-green | #28B482 | Secondary brand color |
| --color-mint-green | #A5E1D2 | Tertiary brand color |
| --color-black-80 | #000000CC | Primary text |
| --color-black-55 | #0000008C | Secondary text |
| --color-white | #FFFFFF | White background |
| --color-sea-green-alt | #008484 | Interactive elements |
| --color-signal-yellow | #FBAD18 | Warning states |
| --color-fire-red | #C21B1B | Error states |
| --color-summer-green | #9DD368 | Success states |

## Typography Tokens

| Token | Value | Usage |
|-------|-------|-------|
| --font-family-default | DNB, Arial, sans-serif | Body text |
| --font-family-heading | DNB, Arial, sans-serif | Headings |
| --font-size-x-small | 0.75rem (12px) | Small labels |
| --font-size-small | 0.875rem (14px) | Secondary text |
| --font-size-basis | 1rem (16px) | Body text |
| --font-size-medium | 1.125rem (18px) | Large body text |
| --font-size-large | 1.5rem (24px) | Small headings |
| --font-size-x-large | 2rem (32px) | Medium headings |
| --font-size-xx-large | 3rem (48px) | Large headings |

## Component Mappings

| UI Element | Eufemia Component (React) | HTML Pattern |
|------------|---------------------------|--------------|
| Button | `<Button>` | `<button class="dnb-button">` |
| Text input | `<Input>` | `<input class="dnb-input">` |
| Card container | `<Card>` | `<div class="dnb-card">` |
| Form group | `<FormRow>` | `<div class="dnb-form-row">` |
| Spacing wrapper | `<Space>` | `<div style="margin: var(--spacing-small)">` |
```

Note: Adjust token values based on actual extraction results

- [ ] **Step 5: Commit token reference**

```bash
git add token-reference.md
git commit -m "Extract Eufemia design tokens for skill embedding"
```

---

### Task 2: Create Skill File Skeleton

**Files:**
- Create: `eufemia.md`

- [ ] **Step 1: Create skill file with frontmatter**

Create `eufemia.md`:

```markdown
---
name: eufemia
description: Enforce DNB Eufemia design system compliance - spacing, colors, typography tokens across React, HTML/CSS, Figma
---

# DNB Eufemia Design System Compliance

This skill enforces Eufemia design system principles for designers building POCs and prototypes.

## How This Skill Works

**Activation:** Triggered by "eufemia" keyword (case-insensitive, whole word)

**Session Persistence:** Once activated, all UI work in the session uses Eufemia compliance

**Silent Enforcement:** Applies tokens and components without status reports

**Multi-Stack:** Supports React, HTML/CSS, and Figma workflows

---

## Activation Logic

[To be filled in Task 3]

---

## Eufemia Token Reference

[To be filled in Task 4]

---

## React Implementation

[To be filled in Task 5]

---

## HTML/CSS Implementation

[To be filled in Task 6]

---

## Figma Integration

[To be filled in Task 7]

---

## Audit Mode

[To be filled in Task 8]
```

- [ ] **Step 2: Commit skeleton**

```bash
git add eufemia.md
git commit -m "Create Eufemia skill file skeleton"
```

---

### Task 3: Write Activation Logic Section

**Files:**
- Modify: `eufemia.md` (Activation Logic section)

- [ ] **Step 1: Add keyword detection and session state logic**

Replace `[To be filled in Task 3]` with:

```markdown
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
     - Figma: Figma URL, `use_figma` tool mention
   - If ambiguous: Ask "Which stack? (React / HTML/CSS / Figma)"
   - Update task metadata: `TaskUpdate` with `metadata: { eufemia_stack: "react|html-css|figma" }`

6. **Enforce silently:**
   - Proceed with implementation using Eufemia tokens and components
   - No announcement like "Applying Eufemia tokens..."
   - Just build correctly using the guidance in sections below

### Stack Detection Precedence

When multiple indicators present:

1. Explicit user mention ("using React", "plain HTML", "in Figma")
2. Figma URL or `use_figma` tool
3. Imports from `@dnb/eufemia` package
4. HTML tags with CSS
5. Ask if still ambiguous
```

- [ ] **Step 2: Commit activation logic**

```bash
git add eufemia.md
git commit -m "Add activation logic and session state management"
```

---

### Task 4: Embed Token Reference Tables

**Files:**
- Modify: `eufemia.md` (Eufemia Token Reference section)
- Read: `token-reference.md`

- [ ] **Step 1: Copy spacing tokens**

Read `token-reference.md` spacing section, paste into eufemia.md:

```markdown
### Spacing Tokens

Use these tokens for margins, padding, gaps, and layout spacing.

| Token | Value | CSS Usage | React Usage |
|-------|-------|-----------|-------------|
| `--spacing-xx-small` | 0.25rem (4px) | `margin: var(--spacing-xx-small)` | `space="xx-small"` |
| `--spacing-x-small` | 0.5rem (8px) | `margin: var(--spacing-x-small)` | `space="x-small"` |
| `--spacing-small` | 1rem (16px) | `margin: var(--spacing-small)` | `space="small"` |
| `--spacing-medium` | 1.5rem (24px) | `margin: var(--spacing-medium)` | `space="medium"` |
| `--spacing-large` | 2rem (32px) | `margin: var(--spacing-large)` | `space="large"` |
| `--spacing-x-large` | 2.5rem (40px) | `margin: var(--spacing-x-large)` | `space="x-large"` |
| `--spacing-xx-large` | 3.5rem (56px) | `margin: var(--spacing-xx-large)` | `space="xx-large"` |

**Common Patterns:**
- Component spacing: `small` to `medium`
- Section spacing: `large` to `x-large`
- Page margins: `x-large` to `xx-large`
- Inline element gaps: `xx-small` to `x-small`
```

- [ ] **Step 2: Copy color tokens**

Read `token-reference.md` color section, paste into eufemia.md:

```markdown
### Color Tokens

Use these tokens for backgrounds, text, borders, and interactive states.

| Token | Value | Usage |
|-------|-------|-------|
| `--color-emerald-green` | #007272 | Primary brand, buttons, links |
| `--color-sea-green` | #28B482 | Secondary brand, accents |
| `--color-mint-green` | #A5E1D2 | Tertiary brand, backgrounds |
| `--color-black-80` | #000000CC | Primary text |
| `--color-black-55` | #0000008C | Secondary text, disabled |
| `--color-white` | #FFFFFF | Backgrounds, inverse text |
| `--color-sea-green-alt` | #008484 | Hover states, active elements |
| `--color-signal-yellow` | #FBAD18 | Warning, attention |
| `--color-fire-red` | #C21B1B | Errors, danger |
| `--color-summer-green` | #9DD368 | Success, confirmation |

**CSS Usage:**
```css
color: var(--color-black-80);
background: var(--color-white);
border-color: var(--color-sea-green);
```

**React Usage:**
Most Eufemia components handle colors automatically. For custom styling:
```jsx
<Button variant="primary"> {/* Uses emerald-green */}
<Heading style={{ color: 'var(--color-black-80)' }}>
```
```

- [ ] **Step 3: Copy typography tokens**

Read `token-reference.md` typography section, paste into eufemia.md:

```markdown
### Typography Tokens

Use these tokens for font sizes, families, and line heights.

| Token | Value | Usage |
|-------|-------|-------|
| `--font-family-default` | DNB, Arial, sans-serif | Body text, UI |
| `--font-family-heading` | DNB, Arial, sans-serif | Headings |
| `--font-size-x-small` | 0.75rem (12px) | Captions, labels |
| `--font-size-small` | 0.875rem (14px) | Secondary text |
| `--font-size-basis` | 1rem (16px) | Body text |
| `--font-size-medium` | 1.125rem (18px) | Emphasized text |
| `--font-size-large` | 1.5rem (24px) | H4, small headings |
| `--font-size-x-large` | 2rem (32px) | H3, medium headings |
| `--font-size-xx-large` | 3rem (48px) | H1, H2, large headings |

**CSS Usage:**
```css
font-family: var(--font-family-default);
font-size: var(--font-size-basis);
```

**React Usage:**
```jsx
<P>Body text (uses basis size)</P>
<Heading size="large">Heading</Heading>
```
```

- [ ] **Step 4: Add component mapping table**

Read `token-reference.md` component mappings, paste into eufemia.md:

```markdown
### Component Mappings

Quick reference for common UI elements to Eufemia components.

| UI Element | React Component | HTML Pattern |
|------------|-----------------|--------------|
| Primary button | `<Button variant="primary">` | `<button class="dnb-button dnb-button--primary">` |
| Secondary button | `<Button variant="secondary">` | `<button class="dnb-button dnb-button--secondary">` |
| Text input | `<Input label="Name" />` | `<div class="dnb-input"><input /></div>` |
| Textarea | `<Textarea label="Message" />` | `<div class="dnb-textarea"><textarea /></div>` |
| Checkbox | `<Checkbox label="Agree" />` | `<div class="dnb-checkbox"><input type="checkbox" /></div>` |
| Radio button | `<Radio label="Option" />` | `<div class="dnb-radio"><input type="radio" /></div>` |
| Card container | `<Card>...</Card>` | `<div class="dnb-card">...</div>` |
| Form row | `<FormRow>...</FormRow>` | `<div class="dnb-form-row">...</div>` |
| Spacing wrapper | `<Space top="small">` | `<div style="margin-top: var(--spacing-small)">` |
| Heading | `<Heading size="large">` | `<h2 class="dnb-h--large">` |
| Paragraph | `<P>` | `<p class="dnb-p">` |
| Link | `<Anchor href="...">` | `<a class="dnb-anchor">` |
```

- [ ] **Step 5: Commit token reference**

```bash
git add eufemia.md
git commit -m "Embed Eufemia token reference tables"
```

---

### Task 5: Write React Implementation Guidance

**Files:**
- Modify: `eufemia.md` (React Implementation section)

- [ ] **Step 1: Add React implementation patterns**

Replace `[To be filled in Task 5]` with:

```markdown
## React Implementation

When Eufemia mode is active and stack is React, follow these patterns.

### Import Pattern

Always import from `@dnb/eufemia/components`:

```jsx
import { Button, Input, Card, Space } from '@dnb/eufemia/components'
import { Heading, P } from '@dnb/eufemia/elements'
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
// ❌ Wrong
<Button style={{ marginTop: '16px' }}>Submit</Button>

// ✅ Correct
<Button top="small">Submit</Button>
```

### Form Pattern

Use FormRow for consistent form layout:

```jsx
<FormRow>
  <Input label="Name" placeholder="Enter name" />
</FormRow>
<FormRow top="medium">
  <Input label="Email" placeholder="Enter email" type="email" />
</FormRow>
<FormRow top="large">
  <Button variant="primary">Submit</Button>
</FormRow>
```

### Card Pattern

Use Card for grouped content:

```jsx
<Card stack>
  <Heading size="medium">Card Title</Heading>
  <P>Card content with automatic spacing via stack prop</P>
  <Button>Action</Button>
</Card>
```

### Custom Styling Pattern

When custom CSS is needed, use design tokens:

```jsx
<div style={{
  padding: 'var(--spacing-medium)',
  backgroundColor: 'var(--color-mint-green)',
  color: 'var(--color-black-80)'
}}>
  Custom styled container
</div>
```

### Common Replacements

When implementing UI, apply these automatic replacements:

| Generic Request | Eufemia Implementation |
|----------------|------------------------|
| "Add a button" | `<Button variant="primary">` |
| "Add a form field" | `<Input label="..." />` inside `<FormRow>` |
| "Add spacing between X and Y" | Wrap in `<Space bottom="medium">` |
| "Create a card" | `<Card stack>` with content |
| "Add margin" | Use `space` prop or `top`/`bottom`/`left`/`right` props |
```

- [ ] **Step 2: Commit React guidance**

```bash
git add eufemia.md
git commit -m "Add React implementation guidance"
```

---

### Task 6: Write HTML/CSS Implementation Guidance

**Files:**
- Modify: `eufemia.md` (HTML/CSS Implementation section)

- [ ] **Step 1: Add HTML/CSS patterns**

Replace `[To be filled in Task 6]` with:

```markdown
## HTML/CSS Implementation

When Eufemia mode is active and stack is HTML/CSS, follow these patterns.

### Include Eufemia Styles

Reference Eufemia CSS in your HTML:

```html
<link rel="stylesheet" href="https://unpkg.com/@dnb/eufemia/style/dnb-ui-lib.min.css">
```

Or if locally installed:

```html
<link rel="stylesheet" href="node_modules/@dnb/eufemia/style/dnb-ui-lib.min.css">
```

### Spacing Pattern

Use CSS custom properties for spacing:

```css
.container {
  margin-top: var(--spacing-medium);
  margin-bottom: var(--spacing-large);
  padding: var(--spacing-small);
}

.component-spacing {
  gap: var(--spacing-small); /* for flex/grid */
}
```

**Never use fixed pixel values:**
```css
/* ❌ Wrong */
.container {
  margin: 16px;
  padding: 24px;
}

/* ✅ Correct */
.container {
  margin: var(--spacing-small);
  padding: var(--spacing-medium);
}
```

### Color Pattern

Use CSS custom properties for colors:

```css
.text {
  color: var(--color-black-80);
}

.primary-button {
  background-color: var(--color-emerald-green);
  color: var(--color-white);
}

.warning {
  background-color: var(--color-signal-yellow);
}
```

### Typography Pattern

Use CSS custom properties for fonts:

```css
body {
  font-family: var(--font-family-default);
  font-size: var(--font-size-basis);
  color: var(--color-black-80);
}

h1 {
  font-family: var(--font-family-heading);
  font-size: var(--font-size-xx-large);
}

.small-text {
  font-size: var(--font-size-small);
}
```

### Component Classes

Use Eufemia CSS classes for components:

```html
<!-- Button -->
<button class="dnb-button dnb-button--primary">Submit</button>

<!-- Input -->
<div class="dnb-input">
  <label class="dnb-label">Name</label>
  <input type="text" placeholder="Enter name" />
</div>

<!-- Card -->
<div class="dnb-card">
  <h2 class="dnb-h--medium">Card Title</h2>
  <p class="dnb-p">Card content</p>
</div>

<!-- Form Row -->
<div class="dnb-form-row">
  <div class="dnb-input">
    <label>Email</label>
    <input type="email" />
  </div>
</div>
```

### Complete Example

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="https://unpkg.com/@dnb/eufemia/style/dnb-ui-lib.min.css">
  <style>
    .container {
      max-width: 800px;
      margin: var(--spacing-x-large) auto;
      padding: var(--spacing-medium);
    }
    
    .header {
      margin-bottom: var(--spacing-large);
    }
    
    .form-group {
      margin-bottom: var(--spacing-medium);
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1 class="dnb-h--x-large">Login</h1>
    </div>
    
    <div class="form-group">
      <div class="dnb-input">
        <label class="dnb-label">Email</label>
        <input type="email" placeholder="you@example.com" />
      </div>
    </div>
    
    <div class="form-group">
      <div class="dnb-input">
        <label class="dnb-label">Password</label>
        <input type="password" />
      </div>
    </div>
    
    <button class="dnb-button dnb-button--primary">Sign In</button>
  </div>
</body>
</html>
```
```

- [ ] **Step 2: Commit HTML/CSS guidance**

```bash
git add eufemia.md
git commit -m "Add HTML/CSS implementation guidance"
```

---

### Task 7: Write Figma Integration Guidance

**Files:**
- Modify: `eufemia.md` (Figma Integration section)

- [ ] **Step 1: Add Figma patterns**

Replace `[To be filled in Task 7]` with:

```markdown
## Figma Integration

When Eufemia mode is active and stack is Figma (detected by Figma URL or `use_figma` tool), follow these patterns.

### Prerequisites

Before using Eufemia in Figma, ensure:
- You have loaded the `figma:figma-use` skill (MANDATORY before any `use_figma` call)
- The target Figma file exists and you have edit access

### Variable References

When creating or modifying designs via `use_figma`, reference Eufemia variables:

```javascript
// Create a frame with Eufemia spacing
const frame = figma.createFrame()
frame.layoutMode = 'VERTICAL'
frame.itemSpacing = 16  // --spacing-small equivalent
frame.paddingLeft = 24  // --spacing-medium equivalent
frame.paddingRight = 24
frame.paddingTop = 24
frame.paddingBottom = 24

// Apply Eufemia colors
frame.fills = [{ type: 'SOLID', color: { r: 0, g: 0.45, b: 0.45 } }] // emerald-green #007272
```

### Eufemia Variable Mappings

When setting properties, use these equivalents:

**Spacing:**
- `xx-small` (4px) → `itemSpacing: 4`, `padding: 4`
- `x-small` (8px) → `itemSpacing: 8`, `padding: 8`
- `small` (16px) → `itemSpacing: 16`, `padding: 16`
- `medium` (24px) → `itemSpacing: 24`, `padding: 24`
- `large` (32px) → `itemSpacing: 32`, `padding: 32`
- `x-large` (40px) → `itemSpacing: 40`, `padding: 40`
- `xx-large` (56px) → `itemSpacing: 56`, `padding: 56`

**Colors (RGB 0-1 format):**
- `emerald-green` → `{ r: 0, g: 0.45, b: 0.45 }`
- `sea-green` → `{ r: 0.16, g: 0.71, b: 0.51 }`
- `mint-green` → `{ r: 0.65, g: 0.88, b: 0.82 }`
- `black-80` → `{ r: 0, g: 0, b: 0, a: 0.8 }`
- `white` → `{ r: 1, g: 1, b: 1 }`
- `signal-yellow` → `{ r: 0.98, g: 0.68, b: 0.09 }`
- `fire-red` → `{ r: 0.76, g: 0.11, b: 0.11 }`
- `summer-green` → `{ r: 0.62, g: 0.83, b: 0.41 }`

**Typography:**
- `x-small` → `fontSize: 12`
- `small` → `fontSize: 14`
- `basis` → `fontSize: 16`
- `medium` → `fontSize: 18`
- `large` → `fontSize: 24`
- `x-large` → `fontSize: 32`
- `xx-large` → `fontSize: 48`

### Auto Layout Pattern

Use auto layout with Eufemia spacing:

```javascript
const container = figma.createFrame()
container.name = "Eufemia Container"
container.layoutMode = 'VERTICAL'
container.itemSpacing = 16  // small spacing between items
container.paddingLeft = 24  // medium padding
container.paddingRight = 24
container.paddingTop = 24
container.paddingBottom = 24
container.primaryAxisSizingMode = 'AUTO'
container.counterAxisSizingMode = 'AUTO'

// Add child elements with consistent spacing
const title = figma.createText()
await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" })
title.characters = "Title"
title.fontSize = 24  // large size
container.appendChild(title)

const content = figma.createText()
await figma.loadFontAsync({ family: "Inter", style: "Regular" })
content.characters = "Content"
content.fontSize = 16  // basis size
container.appendChild(content)
```

### Component Instance Pattern

If Eufemia component library is available in Figma, prefer using component instances:

```javascript
// Search for Eufemia component
const components = await figma.importComponentByKeyAsync('eufemia-button-primary-key')
if (components) {
  const buttonInstance = components.createInstance()
  // Component already has proper spacing/colors
  frame.appendChild(buttonInstance)
} else {
  // Fallback: create manually with Eufemia tokens
  // [manual creation code]
}
```

### Common Patterns

**Button:**
```javascript
const button = figma.createFrame()
button.name = "Button"
button.layoutMode = 'HORIZONTAL'
button.paddingLeft = 16
button.paddingRight = 16
button.paddingTop = 8
button.paddingBottom = 8
button.itemSpacing = 8
button.fills = [{ type: 'SOLID', color: { r: 0, g: 0.45, b: 0.45 } }]
button.cornerRadius = 4

const buttonText = figma.createText()
await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" })
buttonText.characters = "Submit"
buttonText.fontSize = 16
buttonText.fills = [{ type: 'SOLID', color: { r: 1, g: 1, b: 1 } }]
button.appendChild(buttonText)
```

**Card:**
```javascript
const card = figma.createFrame()
card.name = "Card"
card.layoutMode = 'VERTICAL'
card.itemSpacing = 16  // small
card.paddingLeft = 24  // medium
card.paddingRight = 24
card.paddingTop = 24
card.paddingBottom = 24
card.fills = [{ type: 'SOLID', color: { r: 1, g: 1, b: 1 } }]
card.effects = [{
  type: 'DROP_SHADOW',
  color: { r: 0, g: 0, b: 0, a: 0.1 },
  offset: { x: 0, y: 2 },
  radius: 4,
  visible: true
}]
```
```

- [ ] **Step 2: Commit Figma guidance**

```bash
git add eufemia.md
git commit -m "Add Figma integration guidance"
```

---

### Task 8: Write Audit Mode Section

**Files:**
- Modify: `eufemia.md` (Audit Mode section)

- [ ] **Step 1: Add audit mode instructions**

Replace `[To be filled in Task 8]` with:

```markdown
## Audit Mode

When reviewing or modifying existing code for Eufemia compliance, use these patterns to detect and fix violations silently.

### Detection Patterns

Look for these violations:

**Fixed pixel spacing:**
```
margin: 8px
padding: 16px
gap: 24px
margin-top: 32px
```

**Fixed hex colors:**
```
color: #000000
background: #007272
border-color: #28B482
```

**Fixed font sizes:**
```
font-size: 14px
font-size: 16px
font-size: 24px
```

**Non-Eufemia components:**
```jsx
<button style={{margin: '16px'}}>
<input type="text" style={{padding: '8px'}} />
<div style={{backgroundColor: '#007272'}}>
```

### Silent Fix Patterns

Apply these replacements without announcing them:

**Spacing fixes:**
```
margin: 8px → margin: var(--spacing-x-small)
padding: 16px → padding: var(--spacing-small)
gap: 24px → gap: var(--spacing-medium)
margin-top: 32px → margin-top: var(--spacing-large)
```

**Color fixes:**
```
color: #007272 → color: var(--color-emerald-green)
background: #28B482 → background: var(--color-sea-green)
color: #000000 → color: var(--color-black-80)
background: #FFFFFF → background: var(--color-white)
```

**Typography fixes:**
```
font-size: 12px → font-size: var(--font-size-x-small)
font-size: 14px → font-size: var(--font-size-small)
font-size: 16px → font-size: var(--font-size-basis)
font-size: 24px → font-size: var(--font-size-large)
```

**React component fixes:**
```jsx
// Before
<button style={{margin: '16px', padding: '8px'}}>Submit</button>

// After
<Button top="small" style={{padding: 'var(--spacing-x-small)'}}>Submit</Button>
```

```jsx
// Before
<input type="text" style={{marginBottom: '24px'}} />

// After
<Input bottom="medium" />
```

### Audit Workflow

When user requests audit:

1. **Read the target files** using Read tool
2. **Scan for violations** using patterns above
3. **Apply fixes silently** using Edit tool
4. **No status report** - just fix and continue
5. **Commit changes** with descriptive message

Example commit message:
```
Apply Eufemia design tokens to login form

- Replace fixed spacing with token variables
- Replace hex colors with token references
- Replace button with Eufemia Button component
```

### Common Audit Scenarios

**Scenario 1: CSS file with fixed values**

Before:
```css
.container {
  margin: 32px;
  padding: 16px;
  color: #000000;
  background: #FFFFFF;
}
```

After:
```css
.container {
  margin: var(--spacing-large);
  padding: var(--spacing-small);
  color: var(--color-black-80);
  background: var(--color-white);
}
```

**Scenario 2: React component with inline styles**

Before:
```jsx
function LoginForm() {
  return (
    <div style={{ padding: '24px' }}>
      <h1 style={{ fontSize: '32px', marginBottom: '16px' }}>Login</h1>
      <input type="email" style={{ marginBottom: '16px', padding: '8px' }} />
      <button style={{ padding: '12px 24px', background: '#007272' }}>
        Submit
      </button>
    </div>
  )
}
```

After:
```jsx
import { Button, Input, Heading } from '@dnb/eufemia/components'

function LoginForm() {
  return (
    <div style={{ padding: 'var(--spacing-medium)' }}>
      <Heading size="x-large" bottom="small">Login</Heading>
      <Input type="email" bottom="small" />
      <Button variant="primary">Submit</Button>
    </div>
  )
}
```

**Scenario 3: HTML with inline styles**

Before:
```html
<div style="padding: 24px; background: #FFFFFF;">
  <h1 style="font-size: 32px; margin-bottom: 16px;">Welcome</h1>
  <p style="font-size: 16px; color: #000000;">Content here</p>
</div>
```

After:
```html
<div style="padding: var(--spacing-medium); background: var(--color-white);">
  <h1 class="dnb-h--x-large" style="margin-bottom: var(--spacing-small);">Welcome</h1>
  <p class="dnb-p" style="font-size: var(--font-size-basis); color: var(--color-black-80);">Content here</p>
</div>
```
```

- [ ] **Step 2: Commit audit mode section**

```bash
git add eufemia.md
git commit -m "Add audit mode instructions with silent fix patterns"
```

---

### Task 9: Clean Up and Finalize

**Files:**
- Delete: `token-reference.md`
- Modify: `eufemia.md` (final review)

- [ ] **Step 1: Delete temporary token reference**

```bash
git rm token-reference.md
git commit -m "Remove temporary token reference file"
```

- [ ] **Step 2: Review complete skill file**

Run: `wc -l eufemia.md`

Expected: Approximately 400-600 lines

Read through `eufemia.md` to verify:
- [ ] All sections are filled (no placeholders)
- [ ] Code examples are syntactically correct
- [ ] Token values are consistent across sections
- [ ] Frontmatter is complete

- [ ] **Step 3: Test skill file loads**

In a test session, run: `Skill` tool with `skill: "eufemia"` and `args: ""`

Expected: Skill loads without errors (skill system will parse the frontmatter)

Note: Full functionality testing requires manual testing in Task 10

- [ ] **Step 4: Final commit**

```bash
git add eufemia.md
git commit -m "Finalize Eufemia design system compliance skill

Complete skill with:
- Keyword-triggered activation
- Session persistence via task metadata
- Embedded token reference tables
- React, HTML/CSS, and Figma guidance
- Silent audit mode
"
```

---

### Task 10: Manual Testing

**Files:**
- Create: `TESTING.md` (test results documentation)

- [ ] **Step 1: Create testing checklist**

Create `TESTING.md`:

```markdown
# Eufemia Skill Testing Checklist

## Test 1: Keyword Detection

**Scenario:** Fresh session, user says "build a login form using eufemia"

**Expected:**
1. Skill is invoked
2. Opt-in prompt appears: "Apply full Eufemia compliance for this session?"
3. No premature enforcement

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 2: Session Persistence

**Scenario:** After opting in, user requests another UI component without "eufemia" keyword

**Expected:**
1. Skill checks task metadata
2. Finds `eufemia_mode: true`
3. Enforces automatically without re-prompting

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 3: React Implementation

**Scenario:** User says "create a button" with Eufemia mode active, React stack detected

**Expected:**
1. Uses `<Button>` from @dnb/eufemia
2. Applies spacing props not custom margins
3. No announcement of token application

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 4: HTML/CSS Implementation

**Scenario:** User says "create a card" with Eufemia mode active, HTML/CSS stack detected

**Expected:**
1. Uses CSS custom properties (var(--spacing-small))
2. Applies Eufemia classes (dnb-card)
3. No fixed pixel values

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 5: Figma Implementation

**Scenario:** User provides Figma URL and says "add a button using eufemia"

**Expected:**
1. Detects Figma stack
2. Uses Eufemia spacing values in auto layout
3. Applies Eufemia color RGB values

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 6: Audit Mode

**Scenario:** User says "fix this CSS to use eufemia" with non-compliant CSS

**Expected:**
1. Detects violations (fixed px, hex colors)
2. Replaces silently with tokens
3. No status report of changes

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 7: No Keyword, No Enforcement

**Scenario:** User says "build a login form" without "eufemia" keyword

**Expected:**
1. Skill not invoked
2. Standard implementation (no Eufemia enforcement)

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Test 8: Explanatory Request

**Scenario:** User says "what is eufemia?"

**Expected:**
1. Skill invoked
2. Detects non-UI work
3. Provides brief explanation, exits without enforcement mode

**Result:** [ ] Pass [ ] Fail

**Notes:**

---

## Summary

**Passed:** ___ / 8
**Failed:** ___ / 8

**Critical Issues:**

**Next Steps:**
```

- [ ] **Step 2: Run through test scenarios**

For each test in TESTING.md:
1. Start fresh Claude Code session
2. Execute test scenario
3. Mark result as Pass/Fail
4. Document notes on any issues

- [ ] **Step 3: Document test results**

Update TESTING.md with actual results

- [ ] **Step 4: Commit test results**

```bash
git add TESTING.md
git commit -m "Document manual testing results for Eufemia skill"
```

---

## Implementation Complete

After all tasks complete:
- [ ] Skill file created at `eufemia.md`
- [ ] All sections filled with concrete guidance
- [ ] Manual testing completed
- [ ] All commits pushed to git

The skill is ready for use. Users can invoke it by including "eufemia" keyword in their UI requests.
