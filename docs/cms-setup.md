# CMS setup (Sveltia CMS at `/admin/`)

The site uses [Sveltia CMS](https://sveltiacms.app/) at `https://aspecintime.com/admin/`
to edit books, blog posts (The Thoughtful Spot), reviews, and landing pages from a web
form. Every save becomes a commit on the `live` branch, which triggers the GitHub Actions
workflow that builds and deploys to Firebase Hosting.

The CMS UI is shipped in `static/admin/`. The two pieces below must be set up **once**
before anyone can log in.

---

## 1. Create a GitHub OAuth App

Sveltia talks to GitHub on the editor's behalf via OAuth.

1. Go to <https://github.com/settings/developers> → **OAuth Apps** → **New OAuth App**
   (or your org's application settings for org-owned apps).
2. Fill in:
   - **Application name**: `A Spec in Time site editor`
   - **Homepage URL**: `https://aspecintime.com`
   - **Authorization callback URL**: `https://<worker-name>.<account>.workers.dev/callback`
     (pick a worker name now, e.g. `sveltia-cms-auth-aspecintime`, and use
     `https://sveltia-cms-auth-aspecintime.<your-cf-account>.workers.dev/callback`)
3. Click **Register application**.
4. Copy the **Client ID** and generate a **Client secret** — you'll need both in step 2.

---

## 2. Deploy the Sveltia CMS auth Worker to Cloudflare

The auth Worker exchanges OAuth codes for access tokens. It's free on Cloudflare's free tier.

1. Sign in to <https://dash.cloudflare.com>.
2. Follow the [`sveltia/sveltia-cms-auth`](https://github.com/sveltia/sveltia-cms-auth) README
   (one-click **Deploy to Cloudflare Workers**, or fork and `npx wrangler deploy`).
3. Once deployed, note the Worker URL, e.g.
   `https://sveltia-cms-auth-aspecintime.<account>.workers.dev`.
4. Set secrets (Workers & Pages → your worker → **Settings** → **Variables and Secrets**):
   - `GITHUB_CLIENT_ID` → from step 1
   - `GITHUB_CLIENT_SECRET` → from step 1
   - `ALLOWED_DOMAINS` → `aspecintime.com,aspecintime.web.app,localhost`
5. Save and redeploy.

If the Worker URL differs from step 1's callback, update the OAuth App's
**Authorization callback URL** to match.

---

## 3. Point the CMS and CSP at the Worker

1. Edit `static/admin/config.yml` — replace `REPLACE-ME` in `base_url:` with your Worker
   URL (no trailing slash).
2. Edit `firebase.json` — replace both `sveltia-cms-auth.REPLACE-ME.workers.dev` occurrences
   in the CSP `connect-src` directives with the same Worker hostname.
3. Commit and push to `live`.

```yaml
backend:
  name: github
  repo: taocode/aspecintime
  branch: live
  base_url: https://sveltia-cms-auth-aspecintime.your-account.workers.dev
  auth_endpoint: auth
```

---

## 4. Give editors access

1. GitHub repo → **Settings** → **Collaborators** → invite editors with **Write** access.
2. Share:
   - Login URL: <https://aspecintime.com/admin/>
   - Note that first login shows a GitHub OAuth consent screen.
   - Saved changes take ~1–2 minutes to appear (GitHub Actions build + Firebase deploy).

---

## 5. What editors can manage

| Collection | Content |
|------------|---------|
| **Books** | Series novels and novellas (`content/books/<slug>/index.md`) |
| **The Thoughtful Spot** | Blog posts (`content/posts/<slug>/index.md`) |
| **Reviews** | Reader quotes (`content/reviews/<slug>/index.md`) |
| **Landing pages** | Home, My Books, Thoughtful Spot, Author Info, Reviews |

### Book fields

- `series`, `bookNumber`, `kind` (book / novella), `status` (available / coming-soon / in-progress)
- `releaseDate`, `amazonURL`, `cover`, `summary`, `draft`

New entries default to **Draft** until published.

---

## Local development against the CMS

To test `config.yml` without GitHub, add `?local` to `/admin/` in dev (Chrome/Edge), or
temporarily set `backend.name: local`. See
<https://sveltiacms.app/en/docs/configuration/backends/local>.

---

## Notes

- **Hosting**: production on Firebase. The Cloudflare Worker is OAuth-only; it never serves the site.
- **Branches**: `live` (production) and `beta` (staging). CMS commits to `live`.
- **Secrets**: never commit OAuth client secrets or Firebase service account JSON to the repo.
  Store the service account as `FIREBASE_SERVICE_ACCOUNT_ASPECINTIME` in GitHub Actions secrets.
