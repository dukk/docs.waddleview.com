# docs.waddleview.com

Documentation site for [Waddle View](https://waddleview.com/), built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

**Live site:** [https://docs.waddleview.com](https://docs.waddleview.com)

## Local development

Requires Python 3.10+.

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
mkdocs serve
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000). Use `mkdocs build --strict` to match CI (fails on broken links).

## Deploy (GitHub Pages)

Pushes to **`main`** run [`.github/workflows/docs.yml`](.github/workflows/docs.yml):

1. `mkdocs build --strict`
2. Deploy the `site/` artifact to GitHub Pages

**One-time repo setup:**

1. **Settings → Pages → Build and deployment → Source:** GitHub Actions
2. For custom domain **docs.waddleview.com**, add the domain under Pages settings. The repo includes [`docs/CNAME`](docs/CNAME) in the built site.
3. DNS: `CNAME` record `docs` → `<user>.github.io` (or apex per GitHub’s instructions for your account/org)

## Cloudflare Pages (optional)

Same build output as GitHub Pages:

| Setting | Value |
|---------|--------|
| Build command | `pip install -r requirements.txt && mkdocs build` |
| Build output directory | `site` |
| Environment | Python 3.12 |

Point **docs.waddleview.com** at Cloudflare instead of GitHub if you prefer; do not run both deploys to the same hostname without meaning to.

## Content sources

Product behavior is documented from [dukk/waddle-view](https://github.com/dukk/waddle-view). Branding and screenshots are copied from [waddleview.com](https://github.com/dukk/waddleview.com) into `docs/assets/`.

## License

Documentation text follows the same contribution terms as the product repositories unless otherwise noted.
