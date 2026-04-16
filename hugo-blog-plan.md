# Hugo Blog — Implementation Plan

## Problem Statement
Build a simple personal blog with Hugo where posts are `.md` files committed to a GitHub repository. No database, no backend, no infrastructure to manage. Posts live in git; the site rebuilds and deploys automatically on every push.

## Stack Decision
**Hugo** + **PaperMod theme** + **Netlify** for hosting + **Cloudflare Registrar** for the `adamroe.dev` domain. Posts as `.md` files, auto-deploy on push.

## Goals
- Homepage shows latest posts
- Archive/list page to browse older posts
- Posts authored as `.md` files in the repo
- Free hosting on Netlify, zero ongoing maintenance

---

## Chosen Theme: PaperMod

- **GitHub:** `adityatelange/hugo-PaperMod` (~10k stars, most popular Hugo blog theme)
- **Live demo:** https://adityatelange.github.io/hugo-PaperMod/
- **Appearance:** Ultra-clean, minimal, white/dark background. Feels like a well-designed Medium article.
- **Homepage modes:** Three options (configure in `hugo.toml`):
  - `list` — straight list of latest posts (default)
  - `home-info` — large header/intro text + posts below
  - `profile` — photo, bio, social links + posts below
- **Features:** Dark mode toggle, reading time, table of contents, social links, search (Fuse.js), cover images, tags/categories, breadcrumbs, RSS feed, copy-code buttons
- **Install:** `git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod`

---

## Tasks

### Phase 1 — Local Setup
1. **Install Hugo** — `brew install hugo` (macOS). Verify with `hugo version`.
2. **Scaffold project** — `hugo new site my-blog && cd my-blog && git init`
3. **Install PaperMod theme** — `git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod`
4. **Configure `hugo.toml`** — Set `title`, `baseURL`, `theme`, and key params:
   - Enable `showReadingTime`, `showToc`, `showPostNavLinks`
   - Set `homeInfoParams` for homepage intro text

### Phase 2 — Content Structure
5. **Define post structure:**
   ```
   content/
   └── posts/
       ├── my-first-post.md
       └── another-post.md
   ```
6. **Create first post** — `hugo new posts/my-first-post.md`. Each post uses YAML front matter:
   ```yaml
   ---
   title: "My First Post"
   date: 2025-04-16
   draft: false
   tags: ["engineering"]
   description: "Short summary shown on list page"
   ---
   Post content in Markdown here...
   ```
7. **Homepage** — Hugo/PaperMod shows the latest posts list by default. Configure the number of posts shown via `paginate` in `hugo.toml` (default 10). PaperMod's `profileMode` adds a bio header above the list.
8. **Archive page** — Enable the built-in `archives` layout for a posts-grouped-by-year view:
   ```markdown
   <!-- content/archives.md -->
   ---
   title: "Archive"
   layout: "archives"
   url: "/archives/"
   ---
   ```

### Phase 3 — Local Development
9. **Run dev server** — `hugo server -D` (includes drafts). Hot-reloads on file save.
10. **Verify pages:**
    - `/` — homepage with latest posts
    - `/posts/` — paginated list of all posts
    - `/archives/` — grouped by year
    - `/posts/my-first-post/` — individual post
11. **Build for production** — `hugo --minify` → outputs to `public/`

### Phase 4 — GitHub Repo & Deployment
12. **Create GitHub repo** — push the project (themes submodule included)
13. **Configure deployment** — choose one option below
14. **Verify deploy** — push a commit, confirm site rebuilds and updates

### Phase 5 — Custom Domain (`adamroe.dev` via Cloudflare)
15. **Check availability** — verify `adamroe.dev` is available at cloudflare.com/products/registrar
16. **Create a Cloudflare account** (if you don't have one) at cloudflare.com → sign up (free)
17. **Purchase `adamroe.dev` on Cloudflare Registrar** — Cloudflare dashboard → "Domain Registration" → "Register Domains" → search `adamroe.dev` → purchase (~$13/yr, at-cost)
18. **Add domain to Netlify** — Netlify dashboard → your site → "Domain management" → "Add a domain" → enter `adamroe.dev`
19. **Get Netlify nameservers** — Netlify will display 4 nameservers (e.g. `dns1.p05.nsone.net`, `dns2.p05.nsone.net`, etc.)
20. **Point Cloudflare nameservers to Netlify** — Cloudflare dashboard → `adamroe.dev` → "DNS" → "Nameservers" → switch to "Custom nameservers" → enter Netlify's 4 nameservers → Save
21. **Wait for propagation** — DNS propagates in 5–60 minutes. Netlify auto-provisions SSL once it detects the domain.
22. **Verify** — visit `https://adamroe.dev` → should load your blog with a valid SSL certificate

---

## Hosting — Netlify

**Why Netlify:**
- Free tier: 100GB bandwidth/month, 300 build minutes/month — a blog never hits these
- No workflow file needed — connect repo in UI, fill 2 fields, done
- PR/branch deploy previews — draft a post in a branch, get a sharable preview URL before publishing
- Automatic HTTPS, instant rollbacks, global CDN

**Setup steps (Phase 4, task 13) — detailed:**

1. Go to [app.netlify.com](https://app.netlify.com) → sign up/login with your GitHub account
2. Click **"Add new site"** → **"Import an existing project"** → **"Deploy with GitHub"**
3. Authorise Netlify to access your GitHub account (one-time OAuth)
4. Search for and select your blog repository
5. Netlify auto-detects Hugo and pre-fills the build settings. Confirm:
   - **Branch to deploy:** `main`
   - **Build command:** `hugo --minify`
   - **Publish directory:** `public`
6. Click **"Add environment variables"** → add `HUGO_VERSION` = `0.145.0` (pins Hugo version, prevents surprise breakage on upgrades)
7. Click **"Deploy site"** — Netlify clones your repo, runs Hugo, and publishes the output. First deploy takes ~1 minute.
8. Netlify assigns a random URL like `https://silver-fox-abc123.netlify.app` — your site is live immediately
9. *(Optional)* In **"Site settings" → "Domain management"** you can set a custom subdomain (e.g. `my-blog.netlify.app`) for free, or add a custom domain (e.g. `adamroe.dev`) — Netlify handles DNS verification and SSL automatically

From this point on, every `git push` to `main` triggers an automatic rebuild and redeploy. No further configuration is ever needed unless you change the Hugo version.

**Custom domain (optional):** Add in Netlify UI → "Domain management" → SSL provisioned automatically.

**Maintenance:** None. Netlify handles everything. Update `HUGO_VERSION` once or twice a year when a major Hugo release lands.

---

## Custom Domain — `adamroe.dev` via Cloudflare Registrar

**Decision:** Buy `adamroe.dev` through **Cloudflare Registrar** (~$13/yr, at-cost, renewal never increases). Point nameservers to **Netlify DNS** for seamless hosting integration.

### About `.dev`
- Google Registry TLD, on the HSTS preload list — HTTPS required on all `.dev` sites
- Netlify provides free SSL automatically, so this is a non-issue
- Popular and credible with engineering audiences

### Why Cloudflare Registrar
- At-cost pricing: you pay exactly what Google Registry charges, no markup
- Renewal price is identical to registration price, forever
- Free WHOIS privacy
- If you ever switch hosting providers, domain stays put — no vendor lock-in

### DNS Setup — Cloudflare Registrar + Netlify DNS (step-by-step)

**Step 1 — Check domain availability**
- Go to https://www.cloudflare.com/products/registrar/
- Log in or create a free Cloudflare account
- Search for `adamroe.dev`

**Step 2 — Purchase domain on Cloudflare**
- Cloudflare dashboard → "Domain Registration" → "Register Domains"
- Search `adamroe.dev` → add to cart → complete purchase (~$13/yr)
- WHOIS privacy is included and enabled by default

**Step 3 — Add domain to your Netlify site**
- In Netlify → your site → "Domain management" → "Add a domain" → "Add a domain you already own"
- Enter `adamroe.dev` → click "Verify" → "Add domain"
- Netlify will display 4 nameserver addresses (e.g. `dns1.p05.nsone.net`, `dns2.p05.nsone.net`, etc.)

**Step 4 — Point Cloudflare nameservers to Netlify**
- In Cloudflare dashboard → select `adamroe.dev` → "DNS" tab → "Nameservers"
- Click "Change nameservers" → select "Use custom nameservers"
- Replace the existing Cloudflare nameservers with the 4 nameservers Netlify provided
- Save

**Step 5 — Wait for propagation**
- DNS propagation takes 5–60 minutes (usually under 15 min with Cloudflare)
- Netlify automatically detects the DNS change and provisions a free Let's Encrypt SSL certificate
- You'll get an email from Netlify when the domain is live

**Step 6 — Verify**
- Visit `https://adamroe.dev` → your blog loads with a padlock ✅
- Visit `https://www.adamroe.dev` → Netlify auto-redirects to the apex domain ✅

### Ongoing Maintenance
- **Nothing.** Cloudflare auto-renews the domain annually at the at-cost price. Netlify auto-renews the SSL certificate. No action needed.

---

## Cost Summary

| Item | Cost | Effort |
|---|---|---|
| Hugo | Free | Install once |
| GitHub repo | Free | New repo |
| Netlify hosting | Free | ~5 min setup |
| `adamroe.dev` (Cloudflare Registrar) | ~$13/yr | ~10 min DNS setup (one-time) |
| **Total** | **~$13/yr** | **< 1 hour total** |

---

## Day-to-Day Writing Workflow (after setup)
1. `hugo new posts/my-new-post.md`
2. Write post in your editor
3. Set `draft: false` in front matter when ready
4. `git add . && git commit -m "Add: My New Post" && git push`
5. Site rebuilds and deploys in ~30 seconds ✅

---

## Notes
- RSS feed, syntax highlighting, tags, reading time, dark mode — all included via theme, zero config
- Existing `Blog/` (ASP.NET Core + React + SQLite) is untouched

