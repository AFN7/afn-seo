# afn-seo

Claude Code için kapsamlı SEO audit skill'i. `/seo` komutuyla çalışır — projenizin framework'ünü algılar, SEO'yu etkileyen her detayı kontrol eder (sitemap, robots, meta, OG, JSON-LD, canonical, redirect, structured data, performance), bulguları önem sırasına göre raporlar ve onayınızı almadan düzeltme yapmaz.

Next.js, Nuxt, Astro, SvelteKit, Remix, WordPress, saf HTML — her stack'te çalışır.

## Kurulum

### Tek satırla (önerilen)

```bash
mkdir -p ~/.claude/skills/seo && curl -fsSL https://raw.githubusercontent.com/AFN7/afn-seo/main/SKILL.md -o ~/.claude/skills/seo/SKILL.md
```

### Manuel

1. `SKILL.md` dosyasını indirin
2. `~/.claude/skills/seo/SKILL.md` konumuna yerleştirin
3. Claude Code'u yeniden başlatın

### Proje bazlı kurulum

Sadece tek bir projede kullanmak için global `~/.claude/` yerine projenin içine koyun:

```bash
mkdir -p .claude/skills/seo && curl -fsSL https://raw.githubusercontent.com/AFN7/afn-seo/main/SKILL.md -o .claude/skills/seo/SKILL.md
```

## Kullanım

Claude Code'da herhangi bir projenin içindeyken:

```
/seo
```

Skill 6 fazda çalışır:

1. **Detect** — stack'i tespit eder
2. **Audit** — framework'e uygun kontrolleri paralel çalıştırır
3. **Report** — bulguları `CRITICAL / HIGH / MEDIUM / LOW` olarak gruplar, `file:line` referanslarıyla
4. **Wait** — onay bekler (`all` / `1,3,5` / `none`)
5. **Fix** — onaylananları mantıksal commit'ler halinde uygular
6. **Deploy prompt** — proje bilinen bir deploy akışına sahipse sorar

## Ne kontrol ediyor?

Sitemap & robots, meta tags, Open Graph / Twitter cards, JSON-LD structured data (Article, Product, Organization, WebSite SearchAction, FAQPage, vb.), URL yapısı & redirects, framework-specific checks, Core Web Vitals sinyalleri, indexing directives, internal linking, image SEO, Google Search Console hygiene, AI crawler etiquette (GPTBot / ClaudeBot / CCBot), deprecated tag taraması ve daha fazlası.

Tam liste için [SKILL.md](SKILL.md)'yi inceleyin.

## Lisans

MIT
