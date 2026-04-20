# afn-seo

Comprehensive SEO audit skill for Claude Code. Runs with the `/seo` command — detects your project's framework, checks every detail that affects indexing (sitemap, robots, meta, OG, JSON-LD, canonicals, redirects, structured data, performance), reports findings by severity, and never applies fixes without your approval.

Works on Next.js, Nuxt, Astro, SvelteKit, Remix, WordPress, and plain HTML — any stack.

## Installation

### One-liner (recommended)

```bash
mkdir -p ~/.claude/skills/seo && curl -fsSL https://raw.githubusercontent.com/AFN7/afn-seo/main/SKILL.md -o ~/.claude/skills/seo/SKILL.md
```

### Manual

1. Download `SKILL.md`
2. Place it at `~/.claude/skills/seo/SKILL.md`
3. Restart Claude Code

### Project-scoped install

To install only for a single project instead of globally:

```bash
mkdir -p .claude/skills/seo && curl -fsSL https://raw.githubusercontent.com/AFN7/afn-seo/main/SKILL.md -o .claude/skills/seo/SKILL.md
```

## Usage

Inside any project in Claude Code:

```
/seo
```

The skill runs in 6 phases:

1. **Detect** — identifies the stack
2. **Audit** — runs framework-appropriate checks in parallel
3. **Report** — groups findings as `CRITICAL / HIGH / MEDIUM / LOW` with `file:line` references
4. **Wait** — awaits approval (`all` / `1,3,5` / `none`)
5. **Fix** — applies approved fixes as logical commits
6. **Deploy prompt** — if the project has a known deploy flow, asks before deploying

## What it checks

Sitemap & robots, meta tags, Open Graph / Twitter cards, JSON-LD structured data (Article, Product, Organization, WebSite SearchAction, FAQPage, etc.), URL structure & redirects, framework-specific checks, Core Web Vitals signals, indexing directives, internal linking, image SEO, Google Search Console hygiene, AI crawler etiquette (GPTBot / ClaudeBot / CCBot), deprecated tag scanning, and more.

See [SKILL.md](SKILL.md) for the full checklist.

## License

MIT
