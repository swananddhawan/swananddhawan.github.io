# Swanand Dhawan | Tinkering thoughts

Source for my personal blog, [swanand.me](https://swanand.me), a static website built with [Hugo](https://gohugo.io) and the [Paper](https://github.com/nanxiaobei/hugo-paper) theme.

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| [Git](https://git-scm.com/downloads) | any recent | |
| [Hugo **extended**](https://gohugo.io/installation/) | `0.158.0` or newer | CI uses `0.167.0`. Older versions fail to build. |

No Node.js / npm required.

### Installing Hugo

Official install guide for all platforms: <https://gohugo.io/installation/>

#### Linux

Pick **one** of these. Full Linux guide: <https://gohugo.io/installation/linux/>

**Option A: Snap** (easiest, auto-updates, extended edition):

```sh
sudo snap install hugo
```

**Option B: `.deb` from GitHub releases** (Debian / Ubuntu; same method CI uses):

```sh
HUGO_VERSION=0.167.0
wget -O /tmp/hugo.deb "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb"
sudo dpkg -i /tmp/hugo.deb
```

Use `linux-arm64.deb` instead of `linux-amd64.deb` on ARM machines.

**Option C: Prebuilt binary** (any distro):

```sh
HUGO_VERSION=0.167.0
wget -O /tmp/hugo.tar.gz "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
tar -xzf /tmp/hugo.tar.gz -C /tmp hugo
sudo mv /tmp/hugo /usr/local/bin/hugo
```

> [!WARNING]
> Avoid `sudo apt install hugo` and other distro package managers unless you've checked their version. They often ship an old or non-extended Hugo, and this site needs **extended 0.158.0 or newer**.

#### macOS

```sh
brew install hugo
```

Guide: <https://gohugo.io/installation/macos/>

#### Windows

```powershell
winget install Hugo.Hugo.Extended
```

Guide: <https://gohugo.io/installation/windows/>

#### BSD and others

See <https://gohugo.io/installation/bsd/> or the [releases page](https://github.com/gohugoio/hugo/releases).

### Verify

```sh
hugo version   # should mention "extended" and be v0.158.0 or newer
```

> Older Hugo fails with errors like `can't evaluate field Locale`. Upgrade Hugo (`brew upgrade hugo`).

## Setup

The theme is a **git submodule**, so clone with `--recurse-submodules`:

```sh
git clone --recurse-submodules git@github.com:swananddhawan/swananddhawan.github.io.git
cd swananddhawan.github.io
```

Already cloned without submodules? Run:

```sh
git submodule update --init --recursive
```

> If `themes/paper/` is empty, the site will build blank or with "layout not found" warnings. The command above fixes it.

Commit with my personal email in this repo:

```sh
git config user.email swananddhawan@gmail.com
```

## Commands

| Command | What it does |
|---|---|
| `hugo server -D` | Local preview at <http://localhost:1313>, **including drafts**. Live-reloads on save. |
| `hugo server` | Local preview of published posts only (what the live site shows). |
| `hugo new content posts/my-post.md` | Create a new post from the archetype. |
| `hugo --gc --minify` | Production build into `public/` (CI does this for you). |
| `git submodule update --remote themes/paper` | Update the theme to latest upstream. Then refresh the `layouts/_default/` overrides: see [Theme overrides](#theme-overrides). |

## Writing a new post

### 1. Create the file

```sh
hugo new content posts/my-post-slug.md
```

- Use an **English, lowercase, kebab-case** slug (e.g. `on-scientific-temper.md`).
- The slug becomes the URL: `https://swanand.me/posts/my-post-slug/`.

### 2. Edit the front matter

`hugo new` creates YAML front matter between `---` lines (from `archetypes/default.md`):

```yaml
---
title: "My Post Slug"
date: 2026-10-02T20:52:34+05:30
draft: true
tags: ["humanism", "scientific-temper"]
---
```

Posts exported from Emacs org-mode with [ox-hugo](https://ox-hugo.scripter.co/) use TOML between `+++` lines instead. Both formats work.

| Field | Required | Description |
|---|---|---|
| `title` | yes | Post title. |
| `date` | yes | Publish date. Auto-filled by `hugo new`. |
| `draft` | yes | `true` hides the post on the live site. **Set to `false` to publish.** |
| `tags` | no | e.g. `tags: ["pseudoscience", "humanism"]` |
| `author` | no | Defaults to `Swanand Dhawan` (set in `config.toml`). Only set it for a guest author, as plain text (`author: "Name"`), **not** a list. |
| `comments` | no | `false` to hide Disqus comments on this post. |
| `math` | no | `true` to enable KaTeX math rendering. |
| `mermaid` | no | `true` to enable Mermaid diagrams. |
| `hideReadingTime` | no | `true` to hide the "N min read" on the post and in the post list. |

> [!IMPORTANT]
> **Check the `date`.** `hugo new` fills in the current date and time. Hugo hides posts dated in the future, both locally and on the live site, until that moment arrives. If a post is missing from `hugo server -D`, check its `date` first. To preview a future-dated post on purpose, use `hugo server -D -F`.

> [!NOTE]
> **ox-hugo exports** add `author = ["Swanand Dhawan"]` (a list). The theme would show it as `[Swanand Dhawan]`, so delete that line after exporting; the author comes from `config.toml`. ox-hugo also adds a raw-HTML table of contents (`<div class="ox-hugo-toc toc">`). Hugo drops the HTML wrappers and keeps the links; the warning about it is silenced in `config.toml`.

### 3. Write the content (Markdown)

```markdown
## Section heading

Regular paragraph with **bold** and *italic* text.

> A quote.

A claim that needs a source.[^fn:source]

[^fn:source]: Source or footnote text.
```

Useful shortcodes:

```markdown
{{< youtube lCHssQZU40A >}}
{{< figure src="/ox-hugo/image.png" caption="Caption" >}}
{{< collapse summary="Click to expand" content="Hidden **markdown** text" >}}
```

#### Pasting text from a document or email

Plain text that looks fine in an editor can render differently as Markdown:

| In the pasted text | Renders as | Fix |
|---|---|---|
| Paragraphs separated by a single line break | One merged paragraph | Put a **blank line** between paragraphs. |
| A line starting with `- ` (e.g. a sign-off `- Name`) | A bullet list | Escape the dash: `\- Name`. |
| A line starting with `1) ` or `1. ` | A numbered list | Fine for lists. Keep list items on consecutive lines. |
| A line containing only `+++` | Literal `+++` text | Use `---` for a horizontal rule. |
| A line of text directly above a `---` line | A heading | Put a blank line between the text and `---`. |

### 4. Adding images

**Preferred: page bundle**, a folder with `index.md` plus images next to it:

```
content/posts/my-post-slug/
├── index.md
└── photo.jpg
```

```sh
hugo new content posts/my-post-slug/index.md
```

```markdown
![Description of photo](photo.jpg)
```

Older posts keep images in `static/ox-hugo/` and reference them as `/ox-hugo/<file>`.

### 5. Preview and publish

```sh
hugo server -D
```

Open <http://localhost:1313>, check the post, set `draft: false`, then commit and push to `main` (or open a PR and merge it).

## Project structure

```
.
├── archetypes/default.md        # Template used by `hugo new`
├── content/
│   ├── about.md                 # About page
│   └── posts/                   # Blog posts go here
├── layouts/_default/            # Overrides of theme templates (see below)
├── static/ox-hugo/              # Images used by older posts
├── themes/paper/                # Theme (git submodule, upstream nanxiaobei/hugo-paper — do not edit)
├── config.toml                  # Site configuration (title, author, analytics, comments, menu)
└── .github/workflows/hugo.yml   # Build + deploy pipeline
```

Analytics and comments are configured in `config.toml`:

- Google Analytics: `[services.googleAnalytics] ID` (only loaded in production builds).
- Disqus comments: `[services.disqus] shortname`.

### Theme overrides

To change something the theme renders, copy the file from `themes/paper/layouts/...` into the same path under `layouts/` and edit it there.

Current overrides:

- `layouts/_default/baseof.html`: `site.LanguageCode` (deprecated) replaced by `site.Language.Locale`.
- `layouts/_default/single.html`: post byline shows "Published on X · Last modified on Y · N min read · Author". "Last modified" appears only when it differs from the publish date (taken from git history); read time is skipped when `hideReadingTime` is set.
- `layouts/_default/list.html`: "· N min read" after each post's date on the home and tag pages.

> [!NOTE]
> **Updating the theme?** Hugo overrides whole files, so each file above hides any upstream changes to the theme's version. After running `git submodule update --remote themes/paper`, refresh the overrides:
>
> ```sh
> cp layouts/_default/single.html /tmp/single.old.html   # keep a copy for reference
> cp layouts/_default/list.html /tmp/list.old.html
> for f in baseof single list; do cp themes/paper/layouts/_default/$f.html layouts/_default/$f.html; done
> ```
>
> Then re-apply the custom changes:
>
> - In `baseof.html`, change `site.LanguageCode` to `site.Language.Locale`.
> - In `single.html`, replace the date `<time>` in the byline with:
>
>   ```go-html-template
>   <time>Published on {{ .Date | time.Format ":date_medium" -}}</time>
>   {{- end -}}<!---->
>   {{- if and .Lastmod (ne (.Lastmod.Format "2006-01-02") (.Date.Format "2006-01-02")) -}}
>   <span class="mx-1">&middot;</span>
>   <time>Last modified on {{ .Lastmod | time.Format ":date_medium" -}}</time>
>   {{- end -}}<!---->
>   {{- if not .Params.hideReadingTime -}}
>   <span class="mx-1">&middot;</span>
>   <span>{{- .ReadingTime }} min read</span>
>   {{- end -}}<!---->
>   ```
>
> - In `list.html`, after the post's `</time>`, add:
>
>   ```go-html-template
>   {{- if not .Params.hideReadingTime -}}
>   <span class="text-xs antialiased opacity-60">
>     <span class="mx-1">&middot;</span>{{- .ReadingTime }} min read</span
>   >
>   {{- end -}}
>   ```
>
> Run `git diff layouts/` to compare against the previous version, then `hugo server -D` and check that bylines and the post list show the extra items and no deprecation warnings appear.

## Deployment

- Every push to `main` triggers [`.github/workflows/hugo.yml`](.github/workflows/hugo.yml).
- It builds with Hugo extended `0.167.0` and deploys to **GitHub Pages**.
- Can also be triggered manually from the repository's **Actions** tab (`workflow_dispatch`).
- `public/` is git-ignored; never commit build output.
- The custom domain `swanand.me` is set in **Settings → Pages**, not by a `CNAME` file. DNS points the apex at GitHub Pages' IPs and `www` at `swananddhawan.github.io`.

### If the site goes down after changing repository visibility

Switching the repository to private (on a free plan) and back to public disables GitHub Pages and clears its settings. DNS is unaffected. To restore it, open <https://github.com/swananddhawan/swananddhawan.github.io/settings/pages> and:

1. **Source**: set **Build and deployment → Source** to **GitHub Actions**.
2. **Custom domain**: enter `swanand.me` and save. Wait for the DNS check to pass.
3. **Enforce HTTPS**: tick it once the certificate is issued.
4. **Redeploy**: Actions tab → **Deploy swanand.me** → **Run workflow**.

Same with the GitHub CLI:

```sh
gh api -X PUT repos/swananddhawan/swananddhawan.github.io/pages \
  -f build_type=workflow -f cname=swanand.me -F https_enforced=true
gh workflow run hugo.yml -R swananddhawan/swananddhawan.github.io --ref main
```

> [!NOTE]
> Setting the custom domain while the source is "Deploy from a branch" makes GitHub commit a `CNAME` file to `main`. It's harmless with GitHub Actions deploys; run `git pull` before your next commit.

## License

Everything in this repository (posts, templates, configuration) is licensed under [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/). The full text is in [LICENSE](LICENSE).

In short, you may share and adapt the material if you:

- **Attribute**: credit Swanand Dhawan and link to the license.
- **NonCommercial**: don't use it for commercial purposes.
- **ShareAlike**: release your adaptations under the same license.

The Paper theme in `themes/paper/` is a separate project with its own license: see [`themes/paper/LICENSE`](themes/paper/LICENSE).

The site footer shows the license notice. It comes from the `copyright` setting in `config.toml`.
