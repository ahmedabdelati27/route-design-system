# AI coding instructions — Route dark mode

Copy this file's content into the project's `CLAUDE.md` (Claude Code) and/or `AGENTS.md` (Cursor, Copilot, Codex …), and copy `route-dark-mode-tokens.css` + `route-dark-mode-guide.md` into the project (e.g. a `design-system/` folder). Adjust the paths below if you put them elsewhere.

---

## Colors & dark mode

Before writing or changing any UI colors, read `design-system/route-dark-mode-guide.md` and use the tokens in `design-system/route-dark-mode-tokens.css`.

- Never hardcode hex colors — use `var(--color-…)` tokens so the screen works in light and dark (`<html data-theme="dark">`).
- Cards use `--color-surface-card`, never `--color-surface-page` (they look the same in light but the card disappears in dark).
- Fades and overlays use `--color-surface-page` with opacity, never white.
- Items listed as "stays the same in both modes" in the guide (logos, photos, brand cards) must not be bound to theme tokens.
- When touching existing code that uses the old variables (`--ink`, `--body`, `--line`, `--primary` …), migrate them using the table in section 7 of the guide.
