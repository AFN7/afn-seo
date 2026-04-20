---
name: seo
description: Use when user invokes /seo on any project. Comprehensive SEO audit — detects framework, checks every indexable detail (sitemap, robots, meta, OG, JSON-LD, canonicals, redirects, structured data, performance), reports findings by severity, waits for user approval before fixing. Works globally on any stack (Next.js, Nuxt, Astro, SvelteKit, Remix, WordPress, static HTML, etc).
---

# SEO Audit Skill

You are running an exhaustive SEO audit on the user's project. Be **thorough, concrete, and honest**. Check every detail — user explicitly asked for deep inspection. Skip nothing. Don't hedge with "probably" — verify.

## Workflow

1. **Phase 1 — Detect** the project's stack
2. **Phase 2 — Audit** using framework-appropriate checks
3. **Phase 3 — Report** findings grouped by severity with specific file:line references
4. **Phase 4 — Wait** for user approval (all / selected / none)
5. **Phase 5 — Fix** approved issues one commit per logical group, verify each
6. **Phase 6 — Deploy prompt** if project has known deploy flow

**Never fix without explicit approval.** The report comes first.

---

## Phase 1: Detect the Stack

Run these parallel checks to identify the project:

- **Framework signals:** `package.json` dependencies — look for `next`, `nuxt`, `astro`, `@sveltejs/kit`, `@remix-run/*`, `gatsby`, `vite`, `express`, `hono`, `@angular/core`
- **CMS signals:** `wp-config.php` (WordPress), `themes/` (Hugo/Jekyll), `_config.yml` (Jekyll), `gatsby-config.js`
- **Static site:** plain `.html` files without a build framework
- **App Router vs Pages Router** (Next.js): check `src/app/` vs `src/pages/` or `app/` vs `pages/`
- **Language/locale:** check for i18n configs, hreflang patterns
- **Build output:** `.next/`, `dist/`, `out/`, `public/` directories

State what you found in one sentence before auditing. Example: "Next.js 16 App Router with Prisma backend, deployed on custom server."

---

## Phase 2: Audit Checklist

**Run checks in parallel where possible. For each issue, record: file path, line number, what's wrong, why it matters, severity.**

### A. Sitemap & Robots

- [ ] `robots.txt` exists and is accessible
- [ ] `robots.txt` references the sitemap URL
- [ ] `sitemap.xml` exists and returns valid XML
- [ ] Sitemap includes all public pages (compare against routes)
- [ ] Sitemap does NOT include utility/form/admin pages
- [ ] Sitemap does NOT reference missing sub-sitemaps (e.g. `news-sitemap.xml` that 404s)
- [ ] No conflict: pages in sitemap but also noindex
- [ ] No conflict: pages blocked in robots.txt but noindex header set (Google can't read the header)
- [ ] Dynamic sitemaps don't hit timeouts with large datasets (>40K urls should split into sitemap index)

### B. Meta Tags (per page)

For each public route:

- [ ] `<title>` exists, 30-60 chars
- [ ] `<meta name="description">` exists, 100-160 chars
- [ ] `<link rel="canonical">` exists and points to correct URL
- [ ] `<html lang="...">` attribute set
- [ ] `<meta charset>` and `<meta viewport>` present
- [ ] Only ONE `<h1>` per page
- [ ] No duplicate title/description across distinct pages

### C. Open Graph & Twitter

- [ ] `og:title`, `og:description`, `og:url`, `og:image`, `og:type` on all public pages
- [ ] `og:image` dimensions ideal (1200x630)
- [ ] `twitter:card`, `twitter:title`, `twitter:description` present
- [ ] Default/root og-image exists (framework-appropriate: `opengraph-image.png` for Next.js App Router)
- [ ] Per-route OG customization for dynamic content pages

### D. Structured Data (JSON-LD)

- [ ] Uses correct schema.org `@type` for each page type
- [ ] `Article`/`BlogPosting` has required fields (headline, datePublished, author, publisher)
- [ ] `Product`/`SoftwareApplication` has required fields (name, aggregateRating needs both ratingValue AND ratingCount/reviewCount)
- [ ] `BreadcrumbList` on pages with breadcrumbs (should match visible breadcrumbs)
- [ ] `CollectionPage`/`ItemList` on listing pages
- [ ] No `aggregateRating` on types that don't support it (e.g. `SoftwareSourceCode`)
- [ ] **`Organization` schema** on homepage (logo, sameAs for social profiles, contactPoint)
- [ ] **`WebSite` schema with `potentialAction: SearchAction`** on homepage — unlocks sitelinks searchbox in Google SERP
- [ ] **`FAQPage`, `HowTo`, `Recipe`, `Event`, `LocalBusiness`** where content type matches (rich snippet eligibility)
- [ ] **`Review`/`AggregateRating`** — Google requires `ratingCount` OR `reviewCount`, `itemReviewed`, and the rating must be on a reviewable thing
- [ ] **Author schema** — `author` field should be `Person` with `name` and `url`, not a string
- [ ] `datePublished` and `dateModified` in ISO 8601 format
- [ ] Test with Google Rich Results Test mentally: required vs recommended fields
- [ ] No orphan structured data (type declared but no visible content backing it up — Google penalizes)

### E. URL Structure & Redirects

- [ ] HTTP → HTTPS redirect (301)
- [ ] www → non-www (or vice versa) redirect consistent with canonical
- [ ] Trailing slash consistency
- [ ] Case sensitivity (no `/About` and `/about` both returning 200)
- [ ] No chains of redirects (A→B→C should be A→C — max 1 hop)
- [ ] Clean URLs (avoid query strings for primary content)
- [ ] 404 page returns actual 404 status, not 200
- [ ] **Soft 404** — pages returning 200 with "not found" content (check `/nonexistent-slug-xyz123`)
- [ ] **Mixed content** — HTTPS pages loading HTTP resources (scan for `src="http://`, `href="http://`)
- [ ] **SSL certificate** valid, not expiring soon
- [ ] **Faceted navigation** (?filter=X&sort=Y) handled: canonical to base or noindex to prevent infinite variations

Use `curl -sI` to verify each. Cloudflare proxies need `-I` specifically.

### F. Framework-Specific

**Next.js App Router:**
- [ ] `generateMetadata()` on all dynamic routes
- [ ] `generateStaticParams()` where viable (for ISR)
- [ ] `force-dynamic` only where necessary (hurts TTFB, every request hits DB)
- [ ] `revalidate` set on data-dependent pages
- [ ] `next/image` used for images (not plain `<img>` for local assets)
- [ ] `next/font` for custom fonts (prevents layout shift)
- [ ] `next/script` for third-party scripts
- [ ] Route groups don't accidentally exclude pages from metadata
- [ ] `opengraph-image.png`/`.tsx` at root for default OG

**Next.js Pages Router:**
- [ ] `<Head>` from `next/head` used
- [ ] `getStaticProps`/`getServerSideProps` return appropriate caching

**Nuxt / SvelteKit / Astro:**
- [ ] Meta tags via framework-appropriate helpers (`useHead`, `<svelte:head>`, Astro frontmatter)
- [ ] SSG/SSR/CSR balance appropriate for content type

**WordPress:**
- [ ] SEO plugin present (Yoast/Rank Math/SEO Framework)
- [ ] Permalinks configured (not default `?p=N`)
- [ ] XML sitemap generation on
- [ ] No duplicate tag/category archives

### G. Performance (affects crawl budget & Core Web Vitals — real ranking factors)

- [ ] Homepage TTFB < 500ms (measure with `curl -w "%{time_starttransfer}"`)
- [ ] Total HTML size < 2MB (Googlebot ignores bytes after 2MB)
- [ ] Critical meta tags in first 5KB of HTML
- [ ] No massive inline CSS/JS blocks in `<head>` (Sonner, Radix, etc. commonly inline)
- [ ] Images compressed and properly sized
- [ ] Fonts subset and preloaded if custom
- [ ] **`font-display: swap`** on custom fonts (prevents invisible text during load = CLS)
- [ ] **`<link rel="preconnect">`** for third-party origins (analytics, CDNs, fonts)
- [ ] **`<link rel="preload">`** for critical fonts/hero images
- [ ] **Images have width/height attrs** (or aspect-ratio CSS) to prevent CLS
- [ ] **Lazy loading** on below-fold images (`loading="lazy"`)
- [ ] **Modern image formats** (WebP/AVIF) where supported
- [ ] **No render-blocking scripts** in `<head>` without `async`/`defer`
- [ ] **Third-party trackers consolidated** (GTM is one script vs. 5 separate pixels)

**Core Web Vitals (check manually via PageSpeed Insights or WebPageTest):**
- LCP (Largest Contentful Paint) < 2.5s
- INP (Interaction to Next Paint) < 200ms
- CLS (Cumulative Layout Shift) < 0.1

### H. Indexing Directives

- [ ] Pages that should be indexed don't have `noindex`
- [ ] Utility/private pages DO have `noindex` (favorites, submit, search, admin)
- [ ] `X-Robots-Tag` headers match in-HTML robots meta
- [ ] No pages blocked in robots.txt AND have noindex (conflict)
- [ ] Thin-content pages (tag pages with <N items) correctly noindexed

### I. Internal Linking

- [ ] No orphan pages (every route linked from somewhere)
- [ ] No broken internal links (404s)
- [ ] Nav links consistent across pages
- [ ] Deep pages reachable in ≤3 clicks from homepage
- [ ] Link anchor text descriptive (not "click here")
- [ ] **Related content modules** on detail pages (e.g. "Alternatives", "Similar repos")
- [ ] **Pagination links** present (not infinite scroll only — Googlebot doesn't scroll)
- [ ] **Hub/category pages** have enough links to individual items (min 10-20 visible per page)

### J. Content Quality Signals

- [ ] No duplicate pages across slight URL variations (?utm=, ?ref=)
- [ ] Each page has unique `<title>` and meta description
- [ ] No auto-generated thin content pages (e.g. tag page for tag with 1 item)
- [ ] Alt text on all non-decorative images
- [ ] Breadcrumbs present on nested pages

### K. Known Google Pitfalls

- [ ] `hreflang` correct if multi-language
- [ ] No cloaking (what Googlebot sees = what users see)
- [ ] No JavaScript-only content where critical info depends on JS execution
- [ ] Mobile viewport set
- [ ] No `<meta http-equiv="refresh">` redirects

### L. AI / LLM Crawler Etiquette (modern sites)

- [ ] `User-agent: GPTBot`, `User-agent: ClaudeBot`, `User-agent: CCBot` rules in robots.txt if user cares
- [ ] `llms.txt` present if project has documentation (optional, emerging standard)

### M. Deprecated / Harmful Tags (flag if present)

- [ ] **`<meta name="keywords">`** — deprecated by Google 15+ years; remove
- [ ] **AMP pages** (`<link rel="amphtml">`) — Google no longer prefers; consider removing AMP path
- [ ] **`<meta http-equiv="refresh">`** redirects — use proper 301s instead
- [ ] **`rel="next"`/`rel="prev"`** — Google officially stopped using in 2019; not harmful but useless
- [ ] **Flash content** / **ActiveX** — kill immediately
- [ ] **Cloaked content** — different HTML for Googlebot vs users

### N. Image SEO (detailed)

- [ ] All non-decorative images have descriptive `alt` (not filename, not "image")
- [ ] Decorative images have `alt=""` (empty, not missing)
- [ ] Images have `width` and `height` attributes (prevents CLS)
- [ ] `loading="lazy"` on below-fold images
- [ ] `loading="eager"` + `fetchpriority="high"` on LCP image
- [ ] Modern formats (AVIF/WebP) with fallbacks
- [ ] Descriptive filenames (`dashboard-admin-panel.jpg` not `IMG_4532.jpg`)
- [ ] **`ImageObject` schema** with `contentUrl`, `license`, `creator` for important images

### O. Search Engine Verification & Tooling

- [ ] **Google Search Console** verification meta tag OR DNS record set up
- [ ] **Bing Webmaster Tools** (`msvalidate.01`) if targeting Bing
- [ ] **Yandex**, **Baidu** verifications if relevant market
- [ ] **`<link rel="alternate" type="application/rss+xml">`** for RSS discovery
- [ ] **`manifest.json`** linked for PWA (if applicable)
- [ ] **Analytics / tracking** loads after critical content (defer/async)

### P. Sitemap Quality Limits

- [ ] Single sitemap < 50,000 URLs (Google's hard limit)
- [ ] Single sitemap < 50MB uncompressed
- [ ] Split into sitemap index with sub-sitemaps if approaching limits
- [ ] `lastmod` dates present and accurate (not fake "now" on every URL)
- [ ] URLs in sitemap return 200 (not 3xx/4xx)
- [ ] Canonical URLs in sitemap (not redirected versions)

### Q. Accessibility → SEO overlap

- [ ] `<html lang="xx">` set
- [ ] `<main>`, `<nav>`, `<article>`, `<header>`, `<footer>` semantic landmarks
- [ ] Skip-to-content link
- [ ] Form inputs have `<label>` associations
- [ ] Buttons have accessible names (`aria-label` when text is icon-only)

### R. Google Search Console Hygiene (manual, note only)

Note to user — these can't be fixed in code:
- Sitemap submitted in GSC
- Property verified (both www and non-www if applicable)
- Request Indexing used for new priority pages
- Inspect GSC "Pages" report for specific rejection reasons

---

## Phase 3: Report Format

Produce a single consolidated report. Use this exact structure:

```
# SEO Audit — [project name]
Detected: [framework + key facts]

## CRITICAL (blocks indexing or causes Google penalties)
### [N] [Issue name]
- File: `path/file.tsx:123`
- Problem: what exactly is wrong
- Why: one-sentence impact
- Fix: what you'd do

## HIGH (hurts rankings/crawl budget significantly)
[...]

## MEDIUM (suboptimal but not urgent)
[...]

## LOW (polish)
[...]

## PASSED (already correct — listed briefly for confidence)
- [x] HTTPS redirect working
- [x] Canonical on all main routes
- [x] Sitemap lists N pages
...
```

Each issue needs: **file:line**, exact problem, severity reason, proposed fix. Don't pad.

After the report, ask:

> **Which fixes do you want applied?**
> - Reply `all` to apply everything
> - Reply with numbers (e.g. `1,3,5`) to pick specific fixes
> - Reply `none` to skip fixes, just audit

---

## Phase 4: Fixing Protocol

When user approves:

1. **Group related fixes** into logical commits (don't commit each change separately)
2. **Read each file before editing** (required by Edit tool)
3. **Type-check after changes** — run project's type-check command if TypeScript
4. **Commit with clear messages** — use conventional commits (`fix:`, `feat:`, `docs:`)
5. **Show the diff before pushing** if user didn't pre-approve push
6. **If project has CI**, don't skip hooks (`--no-verify` forbidden)

After all approved fixes applied:

- Summarize what changed
- If the project has a detectable deploy method (PM2, Vercel, Netlify, Docker), ask: "Deploy?"
- Never auto-deploy without asking

---

## Principles

- **Be concrete.** "Missing meta description" is useless. "src/app/contact/page.tsx:8 has no `description` field — Google generates one from page content which may not match intent" is useful.
- **Verify, don't assume.** If you think TTFB is slow, measure it. If you think a file has inline CSS bloat, read the file.
- **Prioritize ruthlessly.** Not everything is CRITICAL. If you mark >3 things CRITICAL per audit, recalibrate.
- **Call out false positives from other tools.** If the user mentions a GSC warning that's actually fine, say so.
- **Don't invent external links/tools.** Only reference resources you can verify exist.
- **Skip generic advice.** "Add backlinks" isn't SEO audit. Focus on code/config.

## What NOT to check

- SEO copywriting quality (subjective, not a code issue)
- Keyword density (outdated concept)
- Page speed scores from Lighthouse (separate concern, use different tooling)
- Backlink strategy (not a code issue)
- Social media presence
- Google Ads configuration

## Output length discipline

- Audit report: as long as needed, but tight. No filler.
- Each finding: ≤3 lines.
- Fix summary after applying: ≤5 bullets.
