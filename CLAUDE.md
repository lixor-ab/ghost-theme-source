# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

This is a fork of the Ghost "Source" theme (the official Ghost default theme) customized for local use. Ghost is running locally with a symlink pointing to this directory, and the Chrome LiveReload extension is used for live preview.

## Commands

```bash
# Install dependencies
yarn install

# Start development server (builds + watches for changes + triggers livereload)
yarn dev

# One-time build without watching
gulp build

# Validate theme against Ghost theme spec
yarn test

# Package theme into dist/<name>.zip for upload
yarn zip
```

`yarn dev` is the primary development command — it builds CSS/JS, starts gulp-livereload, and watches all source files.

## Build Pipeline

CSS and JS are compiled via Gulp into `assets/built/` — **never edit files in `assets/built/` directly**.

- **CSS**: `assets/css/screen.css` → `assets/built/screen.css`
  - Uses PostCSS with `postcss-easy-import` (supports `@import`), autoprefixer, and cssnano (minification)
  - `screen.css` has a clear table of contents with numbered sections; follow that structure for new rules
- **JS**: `assets/js/lib/*.js` + `assets/js/*.js` → `assets/built/source.js` (concatenated + uglified)

## Theme Architecture

Ghost uses Handlebars (`.hbs`) for templating.

**Template hierarchy:**
- `default.hbs` — Root layout: `<html>`, `<head>`, navigation, footer, scripts. All other templates inject into `{{{body}}}` via `{{!< default}}`.
- `home.hbs` — Homepage: composes header, CTA, featured posts, and post-list partials driven by `@custom.*` settings
- `index.hbs` — Post list (pagination pages)
- `post.hbs` — Individual post
- `page.hbs` — Static page
- `tag.hbs` / `author.hbs` — Archive templates

**Partials** (`partials/`):
- `components/` — Major layout sections: `navigation.hbs`, `header.hbs`, `header-content.hbs`, `footer.hbs`, `cta.hbs`, `post-list.hbs`, `featured.hbs`
- `icons/` — Inline SVG icons, included via `{{> "icons/name"}}`
- `typography/` — Font loading partials (sans, serif, mono, fonts)
- `post-card.hbs`, `feature-image.hbs`, `lightbox.hbs`, `search-toggle.hbs`, `email-subscription.hbs`

**Custom design settings** (configured in Ghost Admin → Design, controlled via `@custom.*` in templates):
- `navigation_layout`: Logo in the middle / Logo on the left / Stacked
- `header_style`: Landing / Highlight / Magazine / Search / Off
- `post_feed_style`: List / Grid
- `title_font` / `body_font`: sans / serif / mono
- `header_and_footer_color`: Background / Accent / Contrast
- These settings are declared in `package.json` under `config.custom`

**CSS custom properties** are set on `:root` in `screen.css` and drive the design system. The background color is also injected inline in `default.hbs` as `--background-color` from the Ghost admin setting. A small inline script in `default.hbs` computes contrast and sets `has-dark-text` or `has-light-text` on `<html>`.

**Localization**: Translation strings use `{{t "..."}}` helpers. Source JSON lives in `locales-local/` (merged into `locales/` by the gulp `mergeLocales` task). The built locale file is `locales/en.json`.

## Ghost Handlebars Helpers

Key helpers used throughout: `{{navigation}}`, `{{body_class}}`, `{{ghost_head}}`, `{{ghost_foot}}`, `{{asset "..."}}`, `{{@site.*}}`, `{{@custom.*}}`, `{{@member}}`, `{{#match}}...{{/match}}`, `{{#is "post, page"}}`, `{{t "..."}}` (i18n).

Full docs: https://ghost.org/docs/themes/
