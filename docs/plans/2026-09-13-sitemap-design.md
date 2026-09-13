# Design: sitemap.xml and SEO basics for the new URL

Date: 2026-09-13
Status: approved

## Context

The site moved from the custom domain `damarowahutu.com.br` to the GitHub Pages
project URL `https://damarowahutu.github.io/gramatica/`. The old domain expired
(2026-07-29, registry status `inactive`) and will not be renewed (Scenario 2:
no redirect possible). Google still lists old-domain URLs, and those entries can
only decay naturally. Goal: make the new URL fully indexable and accelerate
Google's transition.

## Scope

Two repo changes, plus manual post-deploy steps outside the repo.

### 1. `_config.yml` — url, baseurl, sitemap plugin

```yaml
url: https://damarowahutu.github.io
baseurl: /gramatica
plugins:
  - jekyll-relative-links
  - jekyll-remote-theme
  - jekyll-sitemap   # add
```

- `jekyll-sitemap` is whitelisted on GitHub Pages; no Gemfile needed (repo has none).
- Generates `/gramatica/sitemap.xml` on every push: ~68 absolute URLs (index +
  all 67 content pages), including pages not yet linked in the sumário.
- Pages can opt out later with `sitemap: false` front matter.
- Side benefit: the `{% seo %}` tag in `_layouts/default.html` (jekyll-seo-tag)
  starts emitting correct absolute canonical/og URLs, which previously could not
  be generated without `url`.
- Adding `baseurl` is safe: navigation links are relative markdown links
  rewritten by `jekyll-relative-links` (verified on the live site — leading-slash
  links like `/conteudo/...` already render with the `/gramatica` prefix).

### 2. Google Search Console ownership verification

Verify the URL-prefix property `https://damarowahutu.github.io/gramatica/` with
the meta-tag method: add Google's `google-site-verification` meta tag to
`_includes/head-custom.html` (already included by `_layouts/default.html:13`,
so it lands on every page).

- The token string comes from the user's Google account when creating the
  property — interactive step; repo work is a one-line addition once the token
  exists. (Alternative if preferred: commit the `googleXXXX.html` file GSC
  offers — jekyll copies it as-is; both work, meta tag chosen for tidiness.)

## Out of scope (manual steps after deploy)

1. In GSC: submit sitemap `https://damarowahutu.github.io/gramatica/sitemap.xml`,
   request indexing for key pages.
2. Google "Remove Outdated Content" public tool for old-domain URLs.
3. Update inbound links we control (YouTube video descriptions, social profiles).
4. Old-domain rankings cannot be transferred without a redirect; expect natural
   decay of old entries over weeks/months.

## Verification

No local Jekyll/Ruby available — verification is post-deploy:

1. After push, GitHub Pages rebuilds; `https://damarowahutu.github.io/gramatica/sitemap.xml`
   must return valid XML with ~68 absolute URLs.
2. Sample URLs from the sitemap must return 200.
3. `{% seo %}` output in page source shows canonical URLs with the full new host/path.
4. GSC meta tag present in rendered pages; property verifies in Search Console.
