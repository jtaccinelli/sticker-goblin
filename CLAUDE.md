# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a custom Shopify theme for **sticker-goblin.myshopify.com**, built on the Ritual theme framework (v3.5.1). It uses Shopify's Liquid templating language, CSS custom properties, and vanilla Web Components.

## Commands

```bash
pnpm dev    # Start local dev server against the store (hot-reload via Shopify CLI)
pnpm push   # Push local theme files to the connected Shopify store
pnpm pull   # Pull latest theme from Shopify store to local filesystem
```

All commands require an authenticated Shopify CLI session connected to `stickergoblin.myshopify.com`.

The `shopify.theme.toml` defines three deploy environments:
- **all** — every file (used by `dev`)
- **build** — code only (Liquid, assets, locales, schema)
- **content** — JSON data files only (settings, templates)

## Git: What Is and Isn't Tracked

The following are **excluded from git** to avoid conflicts with merchant edits via the theme editor:

```
theme/config/settings_data.json
theme/templates/*.json
theme/sections/*-group.json
```

Only track code: `.liquid`, `.js`, `.css`, `settings_schema.json`, `locales/`.

## Architecture

### Directory Layout (`theme/`)

| Directory | Purpose |
|-----------|---------|
| `blocks/` | Reusable UI elements (button, icon, accordion, swatches, etc.) |
| `sections/` | Page-level layout containers (hero, carousel, main-product, etc.) |
| `snippets/` | Shared Liquid partials (product-form, variant-picker, meta-tags, etc.) |
| `templates/` | Page type templates (index, product, collection, blog, etc.) |
| `layout/` | Root layout files (`theme.liquid`, `password.liquid`) |
| `assets/` | JS and CSS files |
| `config/` | Theme settings schema and data |
| `locales/` | i18n translation files |

### JavaScript (Web Components)

All interactive UI is built as **custom HTML elements**. The base class is `assets/component.js` — a custom `HTMLElement` with ref management and mutation observer support. Components are registered globally and hydrated on demand via `assets/section-hydration.js`.

Key patterns:
- Web Components for encapsulated interactivity
- View Transitions API for smooth page changes (`assets/utilities.js`)
- Inline `<script>` in `.liquid` files for component registration

### CSS

Three global files: `base.css` (resets, CSS vars, utility classes) and two others. Component-scoped styles live in `<style>` blocks inside `.liquid` files. CSS custom properties (`--color-foreground`, `--color-background`, `--header-height`, etc.) drive the entire color and layout system.

### Liquid / Schema

Each section and block includes a `{% schema %}` block at the bottom defining:
- Settings (text, color_scheme, select, checkbox, etc.)
- Accepted child blocks
- Presets for default configurations

Color schemes and font choices are hardcoded to the brand palette (Pirata One + DM Sans). Do not add references to the Shopify font picker or theme-level font settings — fonts are managed directly.

### Localization

All user-facing strings must use translation keys (`{{ 'key.path' | t }}`), with definitions in `locales/en.default.json`. Other locales exist but English is the default.
