# miniblog — CONTENT_STUDIO's free `*.pages.dev` mini-blog

A tiny, theme-less [Hugo](https://gohugo.io/) site that the CONTENT_STUDIO **heartbeat**
auto-posts to. No API keys, no server: the heartbeat drops a markdown file into
`content/posts/`, commits it, and `git push`es. Cloudflare Pages then builds and
deploys it for free at `https://<your-subdomain>.pages.dev`.

## 1. Connect Cloudflare Pages (one time)

1. Log in to [Cloudflare Dashboard → Pages](https://dash.cloudflare.com/?to=/:account/pages).
2. **Create a project → Connect to Git** and authorize the git provider you'll use
   (GitHub or GitLab). Create a new empty repo for this blog (e.g. `miniblog`).
3. **Build settings:**
   - Framework preset: **Hugo**
   - Build command: `hugo`
   - Build output directory: `public`
   - (Cloudflare auto-detects Hugo; no `HUGO_VERSION` needed for simple sites.)
4. Deploy once. Cloudflare assigns `https://<your-subdomain>.pages.dev`.

## 2. Point this local repo at Cloudflare's git remote

```bash
cd miniblog
git init -b main                      # if not already a repo
git remote add origin <your-git-repo-url>   # from step 1
git add -A
git commit -m "init miniblog"
git push -u origin main
```

Update `baseURL` in `config.toml` to your assigned `*.pages.dev` URL.

## 3. Register the target in CONTENT_STUDIO

```bash
python examples/publish_article.py add-pages "My Pages Blog" \
    --repo miniblog \
    --base-url https://<your-subdomain>.pages.dev \
    --status publish
python examples/publish_article.py test "My Pages Blog"   # checks the repo exists
```

The heartbeat (`tools/heartbeat.py --run-once`, scheduled daily via the platform
automation) will now publish every `output/package_*.json` to this blog. Posts
appear at `https://<your-subdomain>.pages.dev/<slug>/` after the next Cloudflare
build (usually a few seconds after push).

## Notes

- `draft: true` in a post's front matter keeps it out of the production build (Hugo
  skips drafts unless built with `--buildDrafts`). Use `--status draft` on `add-pages`
  if you want to review before going live.
- Money-site backlinks configured via `publish_article.py backlinks add ...` are
  appended as a markdown "Recommended reading" block on every post.
- `miniblog/` is git-ignored by the parent CONTENT_STUDIO repo — it is its own
  deployable repository.
