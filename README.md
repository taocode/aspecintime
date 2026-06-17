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

## Phase 1 status

- [x] Repo scaffold, Hugo + Tailwind config
- [x] Book page template and seed content
- [x] Sveltia CMS config (collections stub)
- [x] Firebase CSP headers (replace `REPLACE-ME` worker hostname)
- [x] CI workflow stub
- [ ] Book cover images (add to `static/images/books/`)
- [ ] Firebase project + GitHub repo creation
- [ ] CMS OAuth Worker deployment

## License

MIT
