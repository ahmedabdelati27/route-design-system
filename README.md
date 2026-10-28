# Route — Dark Mode

Color tokens and rules for building the Route website in **light and dark mode**. Exported from the Figma file *Route Landing Page (Copy)*, variables collection **Mapped** (Light Mode / Dark Mode).

| File | What it is |
|---|---|
| [`route-dark-mode-tokens.css`](./route-dark-mode-tokens.css) | All color tokens as CSS variables, with light and dark values. |
| [`route-dark-mode-guide.md`](./route-dark-mode-guide.md) | Setup, which token goes where, special dark-mode cases, a full example, how to test, and a migration table from the current site's variables. |
| [`ai-coding-instructions.md`](./ai-coding-instructions.md) | Short instructions to paste into `CLAUDE.md` / `AGENTS.md` so AI coding tools follow these rules. |

## Quick start

```html
<link rel="stylesheet" href="route-dark-mode-tokens.css">
<html data-theme="dark"> <!-- or leave it out to follow the OS setting -->
```

```css
.card {
  background: var(--color-surface-card);
  color: var(--color-text-body-1);
  border: 1px solid var(--color-border-default);
}
```

Figma is the source of truth: change a value in Figma first, then update `route-dark-mode-tokens.css`.
