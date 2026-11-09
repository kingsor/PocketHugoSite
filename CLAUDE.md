# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

"The Neetpiq Lab", a personal blog built with Hugo using the third-party **pocket-hugo-theme** (https://github.com/h2dcc/pocket-hugo-theme). It's a bilingual site in Italian and English. Much of the content is still the theme's demo content, which is gradually being replaced.

## Commands

The theme needs Hugo **Extended** >= 0.149.0. The installed version is 0.168.0 extended. There are no tests, linter, or package manager.

```sh
hugo server -D        # dev server with drafts, live reload (http://localhost:1313/)
hugo --gc --minify    # production build into public/
hugo new content posts/YYYY-MM-DD-slug/index.it.md   # new post bundle (Italian)
```

`public/` and `resources/` (the image-processing cache) are generated output. Don't edit them by hand.

## Configuration

- All site config is split by top-level key under `config/_default/` (there is deliberately no root `hugo.toml`; don't recreate one, as Hugo would merge it with these files):
  - `hugo.yaml`: root settings (baseURL, theme, languages defaults, output formats, permalinks, taxonomies, related)
  - `languages.yaml`: `it` / `en` blocks, including per-language `params`
  - `params.yaml`: theme options (contents of `params`, without the `params:` wrapper)
  - `markup.yaml`: Goldmark, TOC, and syntax highlighting
  In each split file, keys go at the top level without the file's name as a wrapper. Comments describe each option, and the theme's `README.md` is the authoritative reference for params (its examples are TOML).
- To check the effective merged config, run `hugo config`.
- `baseURL` is still the placeholder `https://example.org/`, and several params (social links, Twikoo demo comments with `path = "theme"`, SEO tagline/description) still hold theme demo values.

## Multilingual content model

- Languages are `it` and `en`. `defaultContentLanguage = "en"` and `defaultContentLanguageInSubdir = true`, so every URL is prefixed with `/en/` or `/it/`. Both languages currently have `weight = 1`.
- Translations are page bundles with language-suffixed files: `index.en.md` / `index.it.md`. A plain `index.md` belongs to the default language (`en`). Files must be named exactly `index.<lang>.md`. For example, `content/page/guide/ndex.it.md` is misnamed, so it isn't a translation of the guide.
- `index.zh-cn.md` files left over from the theme demo are ignored, because `zh-cn` is not a configured language.
- Language-specific params live under `[languages.<lang>.params]` (e.g. the sidebar subtitle). The UI strings come from the theme's `i18n/*.toml` (it.toml and en.toml are included).

## Content conventions

- Posts: `content/posts/<YYYY-MM-DD-slug>/index.<lang>.md`. The permalink is `/article/:slug/`, so set `slug` in front matter (translations may use different slugs).
- Static pages: `content/page/<name>/`. The permalink is `/:slug/`, and pages appear in the nav through `menu.main.weight` in front matter.
- Common front matter: `title`, `description` (shown under the title and used for SEO), `date`, `slug`, `categories`, `tags`, `comments`, `image` (cover), `coverCard`, `listTitle`, `extensions`.
  - `coverCard: "a".."h"` (light) or `"A".."H"` (dark) forces a generated gradient title card instead of `image`. Any other value picks a random preset.
  - Mermaid/KaTeX are enabled globally in `params.extensions`, but they only load on a page that lists them in its own front matter: `extensions = ["mermaid"]` / `["katex"]`.
- Special pages depend on theme layouts: `talks` uses `layout: "talk"` with outputs `HTML`, `TalksRSS`, `TalksJSON` (the output formats are defined in `config/_default/hugo.yaml`). Each talk entry is an `## heading` followed by a `> YYYY-MM-DD HH:MM` timestamp. `archives` and `search` use the theme's archives/search layouts. `links` uses the `{{< friendlinks >}}` shortcode with a YAML list.
- Goldmark `unsafe = true`, so raw HTML in Markdown is rendered.
- The theme provides these shortcodes: audio, badge, bilibili, button, column(s), fold, friendlinks, gallery, note, timeline/timeline-item, title-card-showcase, video, youtube. See `content/posts/shortcodes-demo/` for usage.

## Theme customization

- `themes/pocket-hugo-theme/` is a git clone of the upstream theme (the site root itself is not a git repo). **Don't modify theme files.** To change a template, copy it into the site-level `layouts/` at the same relative path (Hugo's lookup order lets that copy win), or add files under `assets/` / `static/`.
- Site-level `layouts/` currently holds copies of 9 theme templates (`_default/baseof.html`, `_default/rss.xml`, the `*.talksrss.xml` / `*.talksjson.json` files, `partials/helpers/translation-toggle.html`). Their only change is replacing APIs deprecated in Hugo 0.158 (`.Site.LanguageCode`/`.Language.LanguageCode` → `.Language.Locale`, `.LanguageName` → `.Label`, `.LanguageDirection` → `.Direction`). After pulling theme updates, re-sync these copies from the theme and re-apply the replacements, or delete them if upstream has fixed the deprecations.
- The theme provides stable extension points that you override from the site's `layouts/partials/custom/`:
  - `fonts.html`: font loaders
  - `css.html`: an extra stylesheet loaded after the theme CSS (e.g. `static/css/custom.css`)
  - `comments.html`: a custom comment provider, used when `params.comments.provider = "custom"`
- The theme's `exampleSite/` is a full reference for its features. Run it with `hugo server --source exampleSite --themesDir ../..` from inside the theme directory.
