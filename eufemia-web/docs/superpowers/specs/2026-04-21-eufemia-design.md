# Eufemia Design System Compliance Skill

**Created:** 2026-04-21  
**Purpose:** Enforce DNB Eufemia design system compliance for designers building POCs and interactive prototypes

## Problem Statement

Designers at DNB build prototypes across varying technical stacks (React, HTML/CSS, Figma-to-code). These prototypes often diverge from Eufemia design system principles—particularly in spacing, color tokens, and typography—leading to inconsistent UI that requires rework when moving to production.

The skill solves this by providing silent, automatic enforcement of Eufemia tokens when requested, working across all three implementation approaches.

## User Personas

**Primary:** Designers with mixed technical depth building POCs
- Some comfortable with React components
- Some prefer HTML/CSS
- Some use Figma-to-code workflows
- Need guardrails without constant explanations

## Goals

1. **Silent enforcement** - Apply Eufemia tokens automatically without status reports
2. **Keyword-triggered** - Only activate when "eufemia" keyword is present
3. **Session persistence** - Once opted-in, all UI work uses Eufemia until session ends
4. **Token efficiency** - Embed reference data once, avoid repeated queries
5. **Multi-stack support** - React, HTML/CSS, and Figma workflows

## Non-Goals

- Explaining Eufemia principles (just apply them)
- Supporting partial compliance modes (it's all-or-nothing per session)
- Working outside UI contexts (skill only applies to visual components)
- Auto-detecting Eufemia codebases (requires explicit keyword trigger)

## Architecture

### Single-File Structure

**File:** `eufemia.md` skill in the plugin directory

**Sections:**
1. Frontmatter and skill metadata
2. Activation logic (keyword detection, opt-in, session state)
3. Embedded token reference (spacing, colors, typography)
4. React implementation guidance
5. HTML/CSS implementation guidance
6. Figma workflow integration
7. Audit mode instructions

### Session State Management

Uses TaskCreate metadata as session marker:
```
{
  eufemia_mode: true,
  eufemia_stack: "react" | "html-css" | "figma"
}
```

On each skill invocation:
1. Check for existing task with `eufemia_mode: true`
2. If found, enforce automatically
3. If not found and UI work detected, ask opt-in question
4. Store preference in task metadata for session

### Token Embedding Strategy

**Source:** Extract from `node_modules/@dnb/eufemia/` during skill creation
- CSS custom properties from distributed stylesheets
- Component props/patterns from TypeScript definitions
- Design tokens from package exports

**Format:** Markdown tables embedded in skill file
```markdown
## Spacing Tokens

| Token | Value | Usage |
|-------|-------|-------|
| --spacing-xx-small | 0.25rem (4px) | Minimal gaps, tight layouts |
| --spacing-x-small | 0.5rem (8px) | Compact spacing |
...
```

**Fallback:** If node_modules unavailable, reference https://eufemia.dnb.no/uilib/ with note to user

### Activation Flow

**Keyword detection:** Case-insensitive "eufemia" as whole word (not partial match)

**UI work detection:** Request involves creating, modifying, or auditing visual components (buttons, forms, layouts, cards, etc.)

```
User message contains "eufemia" (case-insensitive, whole word)
  ↓
Skill invoked
  ↓
Check for session task with eufemia_mode metadata
  ↓
├─ Found → Enforce silently (skip opt-in, go straight to implementation)
  ↓
└─ Not found + UI work detected
     ↓
     Ask: "Apply full Eufemia compliance for this session?"
     ↓
     ├─ Yes → Create task with eufemia_mode: true, detect stack, enforce
     └─ No → Exit skill, proceed with standard approach

If "eufemia" keyword present but no UI work (e.g., "what is eufemia?")
  → Provide brief explanation, don't activate enforcement mode
```

### Enforcement Behavior

**React Mode:**
- Use `@dnb/eufemia` components by default
- Apply spacing props (`space`, `top`, `bottom`, etc.)
- Use design tokens for custom CSS
- Example: `<Button space={{top: 'small', bottom: 'medium'}}>` instead of custom margins

**HTML/CSS Mode:**
- Reference CSS custom properties
- Apply class names from Eufemia core styles
- Example: `margin-top: var(--spacing-small);` instead of `margin-top: 8px;`

**Figma Mode:**
- When generating Figma designs, apply Eufemia variable references
- Use component instances from Eufemia Figma library if available
- Apply auto-layout with Eufemia spacing values

**Audit Mode:**
- Scan existing code for non-compliant values
- Replace silently: `margin: 8px` → `margin: var(--spacing-x-small)`
- No status report, just fix and continue

### Stack Detection

**Precedence when multiple indicators present:**
1. Explicit user mention ("using React", "in Figma", "plain HTML")
2. Figma URL or `use_figma` tool call → Figma mode
3. Imports from `@dnb/eufemia` → React mode
4. HTML tags with `<style>` or `.css` files → HTML/CSS mode
5. If still ambiguous → Ask: "React, HTML/CSS, or Figma?"

**Store detected stack** in task metadata (`eufemia_stack` field) for session consistency

## Token Reference Structure

The embedded token reference will contain:

### Spacing Scale
- xx-small through xx-large
- Values in rem and px equivalents
- Usage guidance per token

### Color Palette
- Core colors (blacks, whites, grays)
- Brand colors (emerald green, sea green, mint green)
- Semantic colors (success, warning, error, info)
- Status indicators
- Sbanken theme variant colors

### Typography Scale
- Font families (default, heading, code)
- Font sizes (x-small through xx-large)
- Line heights matched to each size
- Font weights

### Component Patterns
- Common component usage patterns
- Prop defaults for layout/spacing
- Accessibility requirements

## Implementation Details

### Skill File Structure

```markdown
---
name: eufemia
description: Enforce DNB Eufemia design system compliance - spacing, colors, typography tokens across React, HTML/CSS, Figma
---

# Activation

[Keyword detection logic]
[Session state check]
[Opt-in prompt]

# Token Reference

## Spacing Tokens
[Table]

## Color Tokens
[Table]

## Typography Tokens
[Table]

# React Implementation
[Guidance]

# HTML/CSS Implementation
[Guidance]

# Figma Integration
[Guidance]

# Audit Mode
[Instructions]
```

### Key Behaviors

**Silence is golden:**
- No "Applying Eufemia tokens..." announcements
- No "Fixed 12 issues" reports
- Just implement correctly
- Exception: If user explicitly asks "what Eufemia token should I use for X?", answer directly

**Prescriptive, not consultative:**
- Don't ask "Should I use Button or Anchor?"
- Just use the right component based on Eufemia patterns
- Exception: If genuinely ambiguous (both valid), ask for clarification

**Session-scoped:**
- Opt-in question appears once per session
- After opt-in, all UI work is Eufemia-compliant
- No way to disable mid-session (user can ignore keyword to build non-Eufemia UI in parallel)

## Token Extraction Process

**Timing:** During implementation of this design spec (one-time process before skill is used)

**Steps:**
1. Locate `node_modules/@dnb/eufemia/` directory
2. Read CSS files from `style/` subdirectory
3. Parse CSS custom properties (e.g., `--spacing-small: 0.5rem;`)
4. Extract component prop patterns from TypeScript definitions
5. Organize into markdown tables by category
6. Embed tables directly in skill file
7. Verify accuracy against https://eufemia.dnb.no/uilib/

**If node_modules unavailable during implementation:**
- Use fallback: manually reference https://eufemia.dnb.no/uilib/ documentation
- Extract token values from documentation pages
- Note in skill file: "Tokens extracted from documentation on [date]"
- Skill will still function, just won't have package-specific overrides

**Token update strategy:**
- Tokens are static in skill file (not fetched at runtime)
- To update: re-run extraction process, replace tables, increment skill version
- Expected update frequency: quarterly or when major Eufemia releases occur

## Testing Strategy

Manual testing scenarios:
1. **Fresh session, keyword trigger:** Verify opt-in prompt appears
2. **Post opt-in React:** Build a form, verify Eufemia components used
3. **Post opt-in HTML/CSS:** Build a card, verify CSS custom properties used
4. **Audit existing code:** Provide non-compliant CSS, verify silent fixes
5. **No keyword, UI request:** Verify skill doesn't interfere
6. **Session persistence:** After opt-in, verify second UI request doesn't re-prompt

## Success Criteria

1. Skill loads without errors
2. Keyword detection works reliably
3. Session state persists within conversation
4. Token tables render correctly in skill file
5. Designers receive Eufemia-compliant code without being told about it
6. Audit mode fixes violations silently
7. Works across React, HTML/CSS, and Figma contexts

## Future Enhancements (Out of Scope)

- MCP server integration for live documentation
- Partial compliance modes (spacing-only, colors-only)
- Auto-detection without keyword
- Component variation recommendations
- Accessibility checker integration
- Visual diff previews of token applications

## Open Questions

- Should the skill validate component prop combinations for accessibility?
  - Decision: No, keep it focused on tokens. Accessibility is separate concern.
  
- What if designer explicitly requests non-compliant values?
  - Decision: Eufemia mode is all-or-nothing. If they need custom values, they don't use the keyword.

- Should we track token usage analytics?
  - Decision: No, adds complexity without clear value.

## References

- Eufemia Documentation: https://eufemia.dnb.no/uilib/
- Eufemia NPM Package: `@dnb/eufemia`
- MCP Server: `node_modules/@dnb/eufemia/mcp/mcp-docs-server.js`
