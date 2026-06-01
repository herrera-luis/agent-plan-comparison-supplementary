<!--
Frozen prompt body for the personal-blog-scaffold benchmark.

The prompt is sent verbatim to every (tool, leg) cell. The SHA-256 fingerprint
at the bottom is computed over the byte range strictly between the markers
below (exclusive on both sides):

    awk '/^--- prompt body begins ---$/{flag=1;next} /^--- prompt body ends ---$/{flag=0} flag' \
      research/agent-plan-comparison-final/prompts/personal-blog-scaffold.md \
      | shasum -a 256
-->

--- prompt body begins ---
Scaffold a brand-new personal website/blog in the repository `cognitiverun/personal-site` (read it at ref `main` — it is an empty greenfield repo). Build a minimal, content-first, bilingual (English + Spanish) site as a static build deployable to Vercel. Implement it with Astro (v4+) and MDX as static output, using Astro's built-in i18n routing and the `@astrojs/vercel` adapter; add `@astrojs/rss` and `@astrojs/sitemap` for the feed and sitemap. Use a generic placeholder identity throughout (e.g. site name "Personal Site", author "A. Writer", domain `personal-site.example`) — do not use any real person's name.

Site shell
- One shared layout on every page: top-left site name plus a bracketed roles tagline; a minimal top-right nav (Home, Articles, About) and a language switcher. Footer has only social/RSS links and a copyright line — no subscribe form.
- Typography-driven design, restrained palette, dark/light following `prefers-color-scheme`, no web-font downloads.

Internationalization (English + Spanish)
- Both locales are URL-prefixed (`/en/...` and `/es/...`). The root path `/` redirects to a default locale. All UI strings are translated through a typed dictionary, and a language switcher toggles the current page between locales (preserving the path). Every page emits `hreflang` alternates for `en`, `es`, and `x-default`.

Pages
- Home: an identity strip (name + bracketed-roles tagline) plus a reverse-chronological list of articles for the active locale (title, dek/subtitle, date, "full article" link); handle the empty-list case.
- About: a short bio plus an optional external profile link. No companies/course/contact sections.
- Article: a long-form reading view — H1 title, H3 dek, a meta line, and body prose. An article must be able to present a custom structure (import shared components, vary layout) via optional frontmatter controls.

Content model
- Folder-per-article: each article lives under `src/content/articles/<slug>/` with per-locale content (`<slug>/en/README.mdx`, `<slug>/es/README.mdx`) and colocated assets. Adding an article must require only creating one folder with its per-locale files. Include one sample article in both locales that exercises the article template end-to-end.

SEO + feeds
- Localized title/description/OpenGraph, a per-locale RSS feed, and a sitemap.

Quality
- The project must build cleanly as a static site, and reading pages must work with JavaScript disabled (progressive enhancement only).
- Also implement tests/checks that validate the feature works as expected: the build succeeds, routes render for both locales, the language switcher links each page to its counterpart, and `hreflang` alternates are present.

Use the GitHub MCP to interact with the repository. During implementation, commit your work incrementally to a dedicated branch in `cognitiverun/personal-site` via the GitHub MCP — do not commit to the default branch and do not merge the branch.
--- prompt body ends ---

<!-- sha256: b59225a1ee2d503dd0c1d08b4e669edc19196a74695008d7642206ba3763e13f -->
