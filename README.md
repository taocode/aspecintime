# A Spec in Time

Literary sci-fi book series by **M. Narf** — *What side of the ampersand are you?*

Static site built with [Hugo](https://gohugo.io/) 0.161.0 Extended, [Tailwind CSS v4](https://tailwindcss.com/) (via `css.TailwindCSS`), [Sveltia CMS](https://sveltiacms.app/), and [Firebase Hosting](https://firebase.google.com/docs/hosting).

## Stack

| Layer | Choice |
|-------|--------|
| Generator | Hugo 0.161.0 Extended |
| CSS | Tailwind v4 via `css.TailwindCSS` + `hugo_stats.json` |
| CMS | Sveltia CMS at `/admin/` |
| Hosting | Firebase Hosting (`live` / `beta` channels) |
| CI | GitHub Actions on push to `live` or `beta` |

## Local development

**Prerequisites:** Hugo Extended 0.161.0, Node.js 20+

```bash
npm install
npm run dev
```

Open <http://localhost:1313>

## Build

```bash
npm run build
```

Output goes to `public/`.

## Deploy

Deployments are automated via GitHub Actions when pushing to `live` (production) or `beta` (staging).

Manual deploy (requires Firebase CLI + login):

```bash
npm run build
firebase deploy --only hosting
```

### One-time setup

1. Create Firebase project `aspecintime` and connect custom domain `aspecintime.com`.
2. Add GitHub secret `FIREBASE_SERVICE_ACCOUNT_ASPECINTIME` (service account JSON).
3. Follow [docs/cms-setup.md](docs/cms-setup.md) for Sveltia CMS OAuth + Cloudflare Worker.

## Content structure

```
content/
├── _index.md          # Home
├── books/             # Series + novellas
├── posts/             # The Thoughtful Spot
├── about/             # Author Info
└── reviews/
```

## Phase status

**Phase 1** — scaffold, layouts, CI stub: done

**Phase 2a** — content migration: done
- Reviews, Author Info, More of Me from WordPress
- Real Amazon, Patreon, and Facebook links
- Book submenu + Author Info submenu
- `/reviews-2/` redirect

**Next (Phase 2b / 3)**
- [ ] Sitting Room cover image
- [ ] Thoughtful Spot post import
- [ ] Firebase project + deploy secrets
- [ ] CMS OAuth Worker deployment
- [ ] Domain cutover

## License

MIT
