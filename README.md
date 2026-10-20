# web-template-astro

Personal Astro site template (profile + portfolio + blog/knowledge-base) based on [AstroPaper](https://github.com/satnaing/astro-paper) by Sat Naing (MIT).

Live demo (GitHub Pages): [https://thaonbt.github.io/web-template-astro/](https://thaonbt.github.io/web-template-astro/)

The demo on GitHub Pages is only a preview. Production sites built from this template are meant to be deployed on Cloudflare Pages.

## Stack

- [Astro](https://astro.build/) with TypeScript and Tailwind CSS
- Static search with [Pagefind](https://pagefind.app/)
- Markdown and MDX content collections (`posts`, `pages`, `portfolios`)
- Light and dark mode, RSS, sitemap and dynamic OG images

## Pages

Compared with upstream AstroPaper, this template adds a **Profile** page and a **Portfolios** section. The header menu order is About, Profile, Portfolios, Posts, Tags, Notes (Archives and Search are icons). **Notes** is an external link to a separate notes site: see [Link to the notes site](#link-to-the-notes-site).

| Route                                             | Source                                                                 | How to edit                                                                                                      |
| ------------------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `/`                                             | `src/pages/index.astro`                                              | Intro text lives in the page itself; Featured and Recent posts update automatically                              |
| `/about`                                        | `src/content/pages/about.md` (rendered by `src/pages/about.astro`) | Edit the markdown file only. The build fails if `about.md` is missing                                          |
| `/profile`                                      | `src/pages/profile.astro`                                            | Data is hardcoded in the page: see [Profile page](#profile-page)                                                  |
| `/portfolios`                                   | `src/pages/portfolios/index.astro` (default view)                    | One markdown file per project: see [Portfolios](#portfolios). Currently shows the Grid view                       |
| `/portfolios/grid`, `/portfolios/list`        | `src/pages/portfolios/grid.astro`, `list.astro`                    | Both views are always available, with a toggle on the page                                                       |
| `/portfolios/<id>`                              | `src/pages/portfolios/[...slug]/index.astro`                         | `<id>` is the markdown file name without extension. Reserved names: do not name a project `grid` or `list` |
| `/posts`, `/tags`, `/archives`, `/search` | upstream AstroPaper pages                                              | Posts live in `src/content/posts/`                                                                             |

### Profile page

`src/pages/profile.astro` has three sections: intro with avatar, technical skills and work experience timeline. All sample values are placeholders (`[Your Name]`, `ABC Tech Company`...). To make it yours:

- Replace `src/assets/images/avatar.png` with your own image (same file name, or update the import in `profile.astro`)
- Edit the heading and intro text in the _Profile_ section
- Edit the `skills` array (category and items) at the top of the file
- Edit the `experiences` array (period, role, company, description) at the top of the file

### Portfolios

Each project is a markdown file in `src/content/portfolios/`. Frontmatter schema (defined in `src/content.config.ts`):

| Field           | Required | Description                                                                                                        |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------------------ |
| `title`       | yes      | Project name                                                                                                       |
| `description` | yes      | One-line summary shown on the list page                                                                            |
| `tags`        | no       | Array of strings, default `[]`                                                                                   |
| `thumbnail`   | no       | Image path relative to the markdown file, e.g. `../../assets/images/my-project.png`. Shown in the Grid view only |
| `repo`        | no       | Full URL of the repository. Only fill in for **public** repos                                                |
| `demo`        | no       | Full URL of a live demo                                                                                            |
| `order`       | no       | Number, default `100`. Smaller numbers are listed first                                                          |

Example `src/content/portfolios/my-project.md`:

```md
---
title: My Project
description: Short summary of what it does
tags: [TypeScript, Astro]
thumbnail: ../../assets/images/my-project.png
repo: https://github.com/<account>/<repo>
order: 1
---

Details, screenshots and lessons learned in markdown.
```

`thumbnail` is validated by Astro's `image()` helper, so the file must exist (put images in `src/assets/images/`) or the build fails. `repo` and `demo` are validated as URLs, so a value without `https://` fails the build. The detail page shows **Repo** and **Demo** links only when the fields are set (it does not show the thumbnail).

#### Grid and List views

The Portfolios page comes in two layouts, both built from the same data. A **List / Grid** toggle at the top right of the page switches between them (plain links, no JavaScript).

| View | Route                | Component                              | What it shows                                                                                                                                          |
| ---- | -------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Grid | `/portfolios/grid` | `src/components/PortfolioGrid.astro` | Responsive cards (16:9 thumbnail, title, 3-line description, tag chips). The whole card is clickable. A card without `thumbnail` simply has no image |
| List | `/portfolios/list` | `src/components/PortfolioList.astro` | Title, description and tags on one line. Thumbnails are ignored                                                                                        |

Both routes render `src/components/PortfolioPage.astro` with a `view` prop (`"grid"` or `"list"`); the page also owns the toggle and the sorting by `order`.

**Choosing the default view** (what `/portfolios` and the header menu show): edit `src/pages/portfolios/index.astro` and switch the `view` prop between `"grid"` and `"list"`. If you only want one view, delete the other route file and the unused component, then remove it from the `views` array in `PortfolioPage.astro` so the toggle disappears.

## Link to the notes site

The **Notes** menu item opens a different site (by default the [Quartz](https://quartz.jzhao.xyz/) digital garden from `kb-template-quartz`). It is a plain external link, not a page of this site.

- The address is written directly in `src/components/Header.astro`:

  ```astro
  <li class="col-span-2">
    <a
      href="https://thaonbt.github.io/kb-template-quartz"
      target="_blank"
      rel="noopener noreferrer"
    >
      {t.nav.notes}
    </a>
  </li>
  ```
- The label comes from the `nav.notes` key in `src/i18n/lang/en.ts` (and its type in `src/i18n/types.ts`). If you add another language file, add a `notes` entry there too, otherwise `astro check` fails.
- In this template the link points to the demo of `kb-template-quartz`. In the repo you create from this template, replace the `href` with the address of your own notes site.
- To remove the link, delete the `<li>` in `Header.astro` and the `notes` key in `en.ts` and `types.ts`.

The notes site links back to this site from its own footer (see its `README.md`). Keep both addresses in sync when a domain changes.

## Requirements

- Node.js 22.12 or later (24 recommended, matching the CI default)
- pnpm (`corepack enable` is enough on recent Node versions)

## Use as a template

1. Click **Use this template** on GitHub to create a new repo (follow the naming convention, for example `kb-thaonbt-blog`).
2. Clone it and install dependencies:
   ```bash
   pnpm install
   ```
3. Edit `astro-paper.config.ts`: `site.url`, `site.title`, `site.description`, `site.author`, social links and `features.editPost.url` (it still points to the upstream AstroPaper repo).
4. Update `base` in `astro.config.ts` to a fixed value matching the new repo name, e.g. `base: '/kb-thaonbt-blog'`. Do not make it conditional on `NODE_ENV` — see the `site` and `base` table below for which value to use.
5. Replace the placeholder content:
   - About page: `src/content/pages/about.md`
   - Profile page: `src/pages/profile.astro` and `src/assets/images/avatar.png`
   - Portfolio entries: `src/content/portfolios/`
   - Posts: add markdown files to `src/content/posts/` (the folder is empty in this template, only `.gitkeep`)
   - Notes link: replace the `href` in `src/components/Header.astro` (see [Link to the notes site](#link-to-the-notes-site))
6. Remove what you do not need. To drop a page (for example Portfolios), delete its files under `src/pages/` and `src/content/`, remove the collection from `src/content.config.ts` and remove its entry from the menu in `src/components/Header.astro` (labels are in `src/i18n/lang/en.ts`).

## `site` and `base`

GitHub Pages serves a project repo at `https://<username>.github.io/<repo>/`, so the site root is `/<repo>` instead of `/`.

| Target                                                           | `site.url`                   | `base`         |
| ---------------------------------------------------------------- | ------------------------------ | ---------------- |
| GitHub Pages (project repo)                                      | `https://thaonbt.github.io/` | `/<repo-name>` |
| GitHub Pages (user/root repo, e.g. `thaonbt.github.io` itself) | `https://thaonbt.github.io/` | `/`            |
| Cloudflare Pages or custom domain                                | your final URL                 | remove it        |

`base` must be a **fixed string**, not conditional on `NODE_ENV` or any environment variable. Its correct value only depends on where the repo is hosted — that never changes between dev and production, so a fixed value keeps dev, build and deploy all consistent and avoids relying on Astro's undocumented (and deprecated) fallback of inferring `base` from `site`'s pathname when `base` is left empty.

When `base` is set, every internal link must include the prefix (use `getRelativeLocaleUrl` from `astro:i18n` for pages and `getAssetPath` from `src/utils/withBase.ts` for files instead of hardcoded paths). After changing either value, always run a production build and click through the site (see below).

## Commands

| Command          | Action                                                                             |
| ---------------- | ---------------------------------------------------------------------------------- |
| `pnpm install` | Install dependencies                                                               |
| `pnpm dev`     | Dev server at `http://localhost:4321/<repo-name>/`                               |
| `pnpm build`   | Type-check (`astro check`), build to `./dist/` and generate the Pagefind index |
| `pnpm preview` | Serve the production build locally                                                 |
| `pnpm lint`    | Run ESLint                                                                         |
| `pnpm format`  | Format the code with Prettier (`pnpm format:check` only checks)                  |

Search only works after `pnpm build`. In dev mode the search index may be empty.

### Docker (optional)

- `docker compose up` starts the dev server on port 4321 (project mounted into the container)
- `Dockerfile` builds the static site and serves `dist` with nginx on port 80

## Deploy to GitHub Pages (demo)

1. In the repo, open **Settings > Pages** and set **Source** to **GitHub Actions**.
2. Push to `main`. The workflow in `.github/workflows/deploy.yml` builds and publishes the site.

## Deploy to Cloudflare Pages (production)

- Build command: `pnpm build` (so Pagefind indexing runs after the Astro build)
- Output directory: `dist`
- Remove `base` and set `site.url` to the final URL

## Common pitfalls

- **404 on assets or links after deploy:** `base` is missing or a link is hardcoded without the prefix. Test with `pnpm build && pnpm preview`.
- **A post does not show up:** check `draft` and `pubDatetime` in its frontmatter. Drafts and future-dated posts can be skipped in production builds.
- **Build fails on a portfolio entry:** check the frontmatter against the schema above. `title` and `description` are required, `repo` / `demo` must be full URLs and `thumbnail` must point to an existing image (path relative to the markdown file).
- **Thumbnail does not show:** thumbnails only appear in the Grid view. Check which view `/portfolios` uses in `src/pages/portfolios/index.astro`.
- **The Notes menu item opens the template demo instead of my notes:** the `href` in `src/components/Header.astro` still points to `kb-template-quartz`.
- **Build fails with "Missing content entry":** `src/content/pages/about.md` was renamed or deleted.
- **Profile page still shows placeholder text:** its content is hardcoded in `src/pages/profile.astro`, not in the markdown collections.
- **Build fails offline or behind a proxy:** the build downloads the Google Sans Code font (used for code blocks and OG images). Without network access to Google Fonts, `pnpm build` stops with `Cannot find the font path`.
- **Two lockfiles:** keep only `pnpm-lock.yaml`. CI picks the package manager from the lockfile.

## Credits and license

Based on [AstroPaper](https://github.com/satnaing/astro-paper) by [Sat Naing](https://satnaing.dev/).

Released under the [MIT License](./LICENSE). The original copyright notice is preserved as required.
