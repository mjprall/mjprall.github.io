# mjprall.github.io

Personal site for Matthew Prall. It is a static page, published with GitHub Pages at [https://matthewprall.com/](https://matthewprall.com/).

There is no framework and no build step. Light and dark follow the visitor’s `prefers-color-scheme` setting.

## What’s in the repo

| Path | Purpose |
| --- | --- |
| `index.html` | The page: bio, a few work numbers, and project cards |
| `style.css` | Layout, type, and the light/dark themes |
| `assets/` | Screenshots, favicon, Open Graph image, and self-hosted fonts |
| `.github/workflows/pages.yml` | Deploys the site on every push to `main` |
| `CNAME` | Custom domain. The file contains `matthewprall.com`, and it is published at the site root. |
| `LICENSE` | MIT license. Leave this file in place. |

Fonts are [Newsreader](assets/fonts/NEWSREADER-OFL.txt) and [IBM Plex](assets/fonts/IBM-PLEX-OFL.txt), both under the SIL Open Font License.

To preview locally, from the repo root:

```bash
python3 -m http.server
```

Then open `http://127.0.0.1:8000/`. Opening `index.html` as a file works too, because every asset path is relative.

## Edit the projects list

Projects are ordinary HTML in `index.html`. There is no data file.

- The featured project is the `<article class="feature">` in the Projects section.
- Each game is an `<article class="card">` inside `<div class="cards">`, under the “Just for fun” heading.

To add a card, copy an existing `<article class="card">` and change the title, the one-line description, both links (the live site and the source repo), and the image. Put the image in `assets/` and write an `alt` that describes what is actually in the picture. Keep `width` and `height` in step with the file so the layout does not jump.

Paths are relative (`assets/tee-ball.jpg`, `style.css`). They are served from the domain root of `https://matthewprall.com/`. Do not prefix them with a repository name.

## Publish

### One-time: choose GitHub Actions as the Pages source

The workflow is in the repo, and GitHub will not use it until this is set once:

1. Open this repository on GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to **GitHub Actions**.

After that, every push to `main` runs **Deploy GitHub Pages** (`.github/workflows/pages.yml`) and publishes the site. You can also run it by hand from the Actions tab.

The workflow checks out the repo, runs `actions/configure-pages`, uploads this folder with `actions/upload-pages-artifact`, and deploys with `actions/deploy-pages`. It needs `contents: read` so checkout can run, plus `pages: write` and `id-token: write` so the deploy can authenticate.

If the first run fails because Pages has no site yet, set the source as above and re-run the workflow.

### What gets published

The action uploads the repository root and skips `.git` and `.github`. `index.html` is the site. `README.md` and `LICENSE` are published as files and are not linked from the page.

## Custom domain

The primary host is **matthewprall.com** (the apex). `www.matthewprall.com` redirects to `https://matthewprall.com/` through GitHub Pages when DNS for both names is in place. The `CNAME` file stays the apex only. Do not put `www` in that file.

The Pages workflow uploads the repository root (`path: .` in `.github/workflows/pages.yml`). `CNAME` is not a hidden file, so it is part of the artifact and is published at the site root. Once the custom domain is active, `https://mjprall.github.io/` redirects to the apex. Removing the `CNAME` file, or clearing the custom domain in Pages settings, sends the site back to `mjprall.github.io`.

Canonical, Open Graph, Twitter image, and JSON-LD URLs in `index.html` use `https://matthewprall.com/`. Project links that were absolute `mjprall.github.io` URLs use the same host and keep their paths (`/factory-telemetry-sim/`, `/tee-ball-game/`, `/hoppy-Easter/`, `/is-it-plane/`). GitHub project sites for this account are also served under the user site’s custom domain.

### 1. DNS at Squarespace

Leave the domain’s nameservers at Squarespace. You are changing records, not moving DNS hosting.

1. In Squarespace, open **Domains**, select the domain, and open **DNS** / **DNS Settings**.
2. If the domain is connected to a Squarespace website, disconnect it first so Squarespace does not put its own website records back.
3. Remove the existing website records that point the name at Squarespace (their apex `A` records, and the `www` `CNAME`, often aimed at a Squarespace host). Leave mail records alone if you still use them.
4. Add four **A** records for the apex (`@`):

   | Host | Type | Value |
   | --- | --- | --- |
   | `@` | A | `185.199.108.153` |
   | `@` | A | `185.199.109.153` |
   | `@` | A | `185.199.110.153` |
   | `@` | A | `185.199.111.153` |

5. Add four **AAAA** records for `@` as well. GitHub uses these for IPv6:

   | Host | Type | Value |
   | --- | --- | --- |
   | `@` | AAAA | `2606:50c0:8000::153` |
   | `@` | AAAA | `2606:50c0:8001::153` |
   | `@` | AAAA | `2606:50c0:8002::153` |
   | `@` | AAAA | `2606:50c0:8003::153` |

6. Add a **CNAME** record for `www` whose target is `mjprall.github.io`. Point `www` at `mjprall.github.io`, not at the apex domain. Pointing `www` at the apex breaks HTTPS on GitHub Pages. If Squarespace asks for a fully qualified name, use `mjprall.github.io.` with the trailing dot.

These addresses are the GitHub Pages values published in [Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site). Check that page before you change DNS, in case GitHub has published new addresses.

### 2. GitHub Pages

The `CNAME` file already sets the custom domain to `matthewprall.com`. In **Settings → Pages**, confirm that custom domain and wait until the DNS check succeeds. Certificate provisioning can take a while, sometimes up to about a day.

With DNS records for both the apex and `www`, GitHub redirects `www.matthewprall.com` to `https://matthewprall.com/`.

### 3. Enforce HTTPS

When the certificate is ready, in **Settings → Pages**, turn on **Enforce HTTPS**. Leave it off until GitHub says the certificate is provisioned.

Relative image and stylesheet paths stay as they are. If the canonical host ever changes, update `CNAME` and the absolute URLs in `index.html` together: `rel="canonical"`, `og:url`, `og:image`, `twitter:image`, and the JSON-LD `url`.
